---
title: Непрерывная интеграция и непрерывное развертывание Azure IoT Edge | Документация Майкрософт
description: Общие сведения о непрерывной интеграции и непрерывном развертывании Azure IoT Edge
services: iot-Edge
documentationcenter: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 4/30/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 2f635a4c02dd8fd2b58598e53662d1a4d82ea611
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311744"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge---preview"></a>Непрерывная интеграция и непрерывное развертывание в Azure IoT Edge (предварительная версия)
В этом руководстве показано, как с помощью функций непрерывной интеграции и непрерывного развертывания Visual Studio Team Services (VSTS) и Microsoft Team Foundation Server (TFS) быстро и эффективно выполнять сборку, тестирование и развертывание приложений в Azure IoT Edge. 

Из этого учебника вы узнаете следующее:
> [!div class="checklist"]
> * Создание примера решения IoT Edge, содержащего модульные тесты, и запись данных после изменения.
> * Установка расширения Azure IoT Edge для VSTS.
> * Настройка непрерывной интеграции (CI) для создания решения и запуска модульных тестов.
> * Настройка непрерывного развертывания (CD) для развертывания решения и просмотра ответов.

Выполнение задач этого руководства займет 30 минут.

![Непрерывная интеграция и непрерывное развертывание](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Создание примера решения Azure IoT Edge с помощью Visual Studio Code

В рамках этого раздела вы создадите пример решения IoT Edge, содержащий модульные тесты, которые можно выполнять как часть процесса сборки. Перед выполнением инструкций в этом разделе выполните действия, описанные в статье о [разработке решения IoT Edge с несколькими модулями в Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. В палитре команд VS Code введите и выполните команду **Edge: New IoT Edge solution**. Затем выберите папку рабочей области, введите имя решения (по умолчанию используется имя **EdgeSolution**) и создайте в этом решении первый модуль C# (**FilterModule**). Кроме того, для первого модуля следует указать репозиторий образов Docker. По умолчанию используется репозиторий образов на базе локального реестра Docker (`localhost:5000/filtermodule`). Измените его на реестр контейнеров Azure (`<your container registry address>/filtermodule`) или центр Docker для дальнейшей непрерывной интеграции.

    ![Настройка записи контроля доступа](./media/how-to-ci-cd/acr.png)

2. Окно VS Code загрузит рабочую область решения IoT Edge. При необходимости можно ввести и выполнить команду **Edge: Добавить модуль IoT Edge** для добавления дополнительных модулей. В корневой папке содержатся папка `modules`, папка `.vscode` и файл шаблона для манифеста развертывания. Коды всех пользовательских модулей находятся во вложенных папках каталога `modules`. `deployment.template.json` — это шаблон манифеста развертывания. Для некоторых параметров в этом файле значения извлекаются из файла `module.json`, который есть в папке каждого модуля.

3. Пример решения IoT Edge готов. Модуль C# по умолчанию действует как модуль сообщений для канала. В `deployment.template.json` это решение отображается с двумя модулями. Сообщение создается в модуле `tempSensor` и передается напрямую через `FilterModule`, а затем отправляется в Центр Интернета вещей. Замените весь файл **Program.cs** приведенным ниже содержимым. Дополнительные сведения об этом фрагменте кода см. в статье о [создании проекта модуля C# в IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                // The Edge runtime gives us the connection string we need -- it is injected as an environment variable
                string connectionString = Environment.GetEnvironmentVariable("EdgeHubConnectionString");

                // Cert verification is not yet fully functional when using Windows OS for the container
                bool bypassCertVerification = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
                if (!bypassCertVerification) InstallCert();
                Init(connectionString, bypassCertVerification).Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Add certificate in local cert store for use by client for secure connection to IoT Edge runtime
            /// </summary>
            static void InstallCert()
            {
                string certPath = Environment.GetEnvironmentVariable("EdgeModuleCACertificateFile");
                if (string.IsNullOrWhiteSpace(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing path to certificate file.");
                }
                else if (!File.Exists(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing certificate file.");
                }
                X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
                store.Open(OpenFlags.ReadWrite);
                store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
                Console.WriteLine("Added Cert: " + certPath);
                store.Close();
            }
            /// <summary>
            /// Initializes the DeviceClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init(string connectionString, bool bypassCertVerification = false)
            {
                Console.WriteLine("Connection String {0}", connectionString);

                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                // During dev you might want to bypass the cert verification. It is highly recommended to verify certs systematically in production
                if (bypassCertVerification)
                {
                    mqttSetting.RemoteCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true;
                }
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                DeviceClient ioTHubModuleClient = DeviceClient.CreateFromConnectionString(connectionString, settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

                // Read TemperatureThreshold from Module Twin Desired Properties
                var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
                var moduleTwinCollection = moduleTwin.Properties.Desired;
                try {
                    temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
                } catch(ArgumentOutOfRangeException) {
                    Console.WriteLine("Proerty TemperatureThreshold not exist");
                }

                // Attach callback for Twin desired properties updates
                await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
            }

            static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
            {
                try
                {
                    Console.WriteLine("Desired property change:");
                    Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                    if (desiredProperties["TemperatureThreshold"] != null)
                        temperatureThreshold = desiredProperties["TemperatureThreshold"];

                }
                catch (AggregateException ex)
                {
                    foreach (Exception exception in ex.InnerExceptions)
                    {
                        Console.WriteLine();
                        Console.WriteLine("Error when receiving desired property: {0}", exception);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
                }
                return Task.CompletedTask;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message {counterValue}: [{messageString}]");

                // Get message body
                var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                {
                    Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                        $"exceeds threshold {temperatureThreshold}");
                    var filteredMessage = new Message(messageBytes);
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    return filteredMessage;
                }
                return null;
            }

            static async Task<MessageResponse> FilterMessages(Message message, object userContext)
            {
                try
                {
                    DeviceClient deviceClient = (DeviceClient)userContext;

                    var filteredMessage = filter(message);
                    if (filteredMessage != null)
                    {
                        await deviceClient.SendEventAsync("output1", filteredMessage);
                    }

                    // Indicate that the message treatment is completed
                    return MessageResponse.Completed;
                }
                catch (AggregateException ex)
                {
                    foreach (Exception exception in ex.InnerExceptions)
                    {
                        Console.WriteLine();
                        Console.WriteLine("Error in sample: {0}", exception);
                    }
                    // Indicate that the message treatment is not completed
                    var deviceClient = (DeviceClient)userContext;
                    return MessageResponse.Abandoned;
                }
                catch (Exception ex)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", ex.Message);
                    // Indicate that the message treatment is not completed
                    DeviceClient deviceClient = (DeviceClient)userContext;
                    return MessageResponse.Abandoned;
                }
            }
        }
    }
    ```

4. Создайте проект модульного теста .Net Core. В проводнике VS Code создайте новую папку **tests\FilterModuleTest** в рабочей области. Затем во встроенном терминале VS Code (**Ctrl + `**) выполните приведенные ниже команды, чтобы создать тестовый проект xunit и добавить ссылку в проект **FilterModule**.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Структура папок](./media/how-to-ci-cd/add-test-project.png)

5. В папке **FilterModuleTest** замените имя файла **UnitTest1.cs** на **FilterModuleTest.cs**. Выберите и откройте **FilterModuleTest.cs**. Затем замените весь код приведенным ниже фрагментом кода, содержащим модульные тесты для проекта FilterModule.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. Во встроенном терминале можно ввести приведенные ниже команды для локального выполнения модульных тестов. 
    ```cmd
    dotnet test
    ```

    ![Модульный тест](./media/how-to-ci-cd/unit-test.png)

7. Сохраните эти проекты и запишите их после изменения в репозиторий VSTS или TFS.
    

> [!NOTE]
> Дополнительные сведения см. в статье о [совместном использовании кода с помощью Visual Studio и VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Настройка непрерывной интеграции
В рамках этого раздела вы создадите определение сборки, настроенное для автоматического запуска при записи данных после изменения в пример решения IoT Edge. При этом будут автоматически выполняться содержащиеся внутри модульные тесты.

1. Войдите в учетную запись VSTS (**https://**_ваша_учетная_запись_**.visualstudio.com**) и откройте проект, в который внесен пример приложения.

    ![Код записи после изменения](./media/how-to-ci-cd/init-project.png)

1. Ознакомьтесь со сведениями на странице [Azure IoT Edge для VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) в разделе решений VSTS в Marketplace. Нажмите кнопку **Получить бесплатно** и следуйте указаниям в мастере, чтобы установить это расширение для вашей учетной записи VSTS или скачать его в TFS.

    ![Установка расширения](./media/how-to-ci-cd/install-extension.png)

1. В VSTS откройте центр **Build &amp; Release** (Сборка и выпуск) и на вкладке **Сборки** выберите **+ Новое определение**. Или, если определения сборки уже есть, нажмите кнопку **+ Создать**. 

    ![Новая сборка](./media/how-to-ci-cd/add-new-build.png)

1. При получении запроса выберите тип источника **VSTS Git**. Затем выберите проект, репозиторий и ветвь, где находится ваш код. Выберите **Продолжить**.

    ![Выбор VSTS git](./media/how-to-ci-cd/select-vsts-git.png)

1. В окне **Select a template** (Выбор шаблона) выберите **start with an Empty process** (Начать с пустым процессом).

    ![Начало с пустым процессом](./media/how-to-ci-cd/start-with-empty.png)

1. Щелкните **+** справа от **Phase 1** (Фаза 1), чтобы добавить задачу в фазу. Затем найдите и выберите **.Net Core** и щелкните **Добавить**, чтобы добавить эту задачу в фазу.

    ![Тест Dotnet](./media/how-to-ci-cd/add-dot-net-core.png)

1. Измените **отображаемое имя** на **dotnet test** (тест dotnet), а в раскрывающемся списке **Команда** выберите **test**. Добавьте указанный ниже путь в поле **Path to project(s)** (Путь к проектам).

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Настройка теста dotnet](./media/how-to-ci-cd/dotnet-test.png)

1. Щелкните **+** справа от **Phase 1** (Фаза 1), чтобы добавить задачу в фазу. Найдите и выберите **Azure IoT Edge**, затем **дважды** нажмите кнопку **Добавить**, чтобы добавить эти задачи в фазу.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. В первой задаче Azure IoT Edge замените **отображаемое имя** на **Module Build and Push** (Сборка и отправка модуля), а в раскрывающемся списке **Действие** выберите **Build and Push** (Сборка и отправка). В текстовое поле **Module.json File** (Файл Module.json) добавьте указанный ниже путь. Затем выберите **Тип реестра контейнеров**. Не забудьте настроить и выбрать тот же реестр в коде. Эта задача обеспечит сборку и отправку всех модулей в решении и их публикацию в указанном реестре контейнеров. 

    ```
    **/module.json
    ```

    ![Сборка и отправка модуля](./media/how-to-ci-cd/module-build-push.png)

1. Во второй задаче Azure IoT Edge замените **отображаемое имя** на **Deploy to IoT Edge device** (Развертывание на устройстве IoT Edge), а в раскрывающемся списке **Действие** выберите **Deploy to IoT Edge device** (Развертывание на устройстве IoT Edge). Выберите подписку Azure и введите имя вашего Центра Интернета вещей. Можно указать идентификатор развертывания IoT Edge и приоритет развертывания. Также можно выбрать развертывание на одном или нескольких устройствах. При развертывании на нескольких устройствах необходимо указать целевое состояние устройства. Например, если в качестве состояния нужно использовать теги устройства, необходимо обновить теги соответствующих устройств перед развертыванием. 

    ![Развертывание в Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. Нажмите **Обработать** и убедитесь, что **Очередь агентов** имеет значение **Hosted Linux Preview** (Размещенная предварительная версия Linux).

    ![Настройка](./media/how-to-ci-cd/configure-env.png)

1. Откройте вкладку **Триггеры** и включите триггер **Непрерывная интеграция**. Убедитесь, что включена ветвь, содержащая ваш код.

    ![Триггер](./media/how-to-ci-cd/configure-trigger.png)

1. Сохраните новое определение сборки и поставьте в очередь новую сборку. Нажмите кнопку **Save & queue** (Сохранить и поставить в очередь).

1. Выберите ссылку на сборку на появившейся панели сообщений. Или же перейдите к определению сборки, чтобы просмотреть последнее поставленное в очередь задание сборки.

    ![Создание](./media/how-to-ci-cd/build-def.png)

1. По завершении сборки откроется сводка для каждой задачи и результаты в файле журнала в режиме реального времени. 
    
    ![Завершено](./media/how-to-ci-cd/complete.png)

1. Можно вернуться в VS Code и проверить обозреватель устройств Центра Интернета вещей. Устройство Edge с модулем должно начать работу (убедитесь, что учетные данные реестра добавлены в среду выполнения Edge).

    ![Выполнение Edge](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Непрерывное развертывание на устройствах IoT Edge

Для непрерывного развертывания, по сути, необходимо настроить задания CI с надлежащими устройствами IoT Edge, включая **триггеры** для соответствующих ветвей в проекте. В классической практике DevOps проект содержит две основные ветви. Главная ветвь должна содержать стабильную версию кода, а ветвь разработки — последние изменения в коде. Каждому разработчику в команде следует отделить ветвь разработки в свою ветвь компонентов при начале обновления кода. Это означает, что все фиксации будут происходить с ветвями компонентов за пределами ветви разработки. Каждую же отправленную фиксацию следует протестировать посредством системы непрерывной интеграции. После полного локального тестирования кода ветвь компонентов следует объединить с ветвью разработки посредством запроса на вытягивание. Когда код в ветви разработчика тестируется с помощью системы CI, ветвь можно объединить с главной ветвью посредством запроса на вытягивание.

Таким образом, при развертывании на устройствах IoT Edge есть три основные среды.
- В ветви компонентов можно использовать имитированное устройство IoT Edge на компьютере разработки или выполнить развертывание на физическом устройстве IoT Edge.
- В ветви разработки следует выполнить развертывание на физическом устройстве IoT Edge.
- В главной ветви целевыми устройствами IoT Edge должны быть устройства в рабочей среде.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве показано, как можно использовать функции непрерывной интеграции и непрерывного развертывания компонентов в VSTS или TFS. 

* Основные сведения о развертывании IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Основные сведения о развертываниях IoT Edge для отдельных устройств или в требуемом масштабе).
* Ознакомьтесь со статьей о [развертывании и мониторинге модулей IoT Edge в нужном масштабе][how-to-deploy-monitor.md], чтобы узнать, как создавать, обновлять или удалять развертывания.









