---
title: "Использование Visual Studio Code для разработки модуля C# с помощью Azure IoT Edge | Документация Майкрософт"
description: "Разработка и развертывание модуля C# с помощью Azure IoT Edge в VS Code без переключения контекста"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cad28b4e6d4e46058641da19795cd71efdbd0c92
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Использование Visual Studio Code для разработки модуля C# с помощью Azure IoT Edge
В этой статье содержатся подробные инструкции по использованию [Visual Studio Code](https://code.visualstudio.com/) в качестве основного инструмента разработки и развертывания модулей IoT Edge. 

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве предполагается, что в качестве компьютера для разработки вы используете компьютер или виртуальную машину под управлением Windows или Linux. Устройством Azure IoT Edge может быть другое физическое устройство или же его можно сымитировать на компьютере разработки.

Перед началом работы с этим руководством убедитесь, что вы ознакомились со следующим руководством.
- Развертывание Azure IoT Edge на имитированном устройстве в ОС [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) или [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).
- [Develop and deploy a C# IoT Edge module to your simulated device — preview](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module) (Разработка модуля IoT Edge с кодом C# и его развертывание на имитированном устройстве (предварительная версия)).

Ниже приведен контрольный список элементов, которые должны быть у вас после выполнения инструкций предыдущих руководств.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/).
- [Сценарий элемента управления IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl).
- Шаблон AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`).
- Активный Центр Интернета вещей с устройством IoT Edge (как минимум).

Кроме того, рекомендуем установить [службу поддержки Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для оптимального управления образами и контейнерами модуля.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Развертывание модуля Azure IoT Edge в VS Code

### <a name="list-your-iot-hub-devices"></a>Составление списка устройств Центра Интернета вещей
Существует два способа перечислить ваши устройства Центра Интернета вещей в VS Code. Вы можете выбрать любой из них.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>Выбор Центра Интернета вещей в учетной записи Azure в VSCode
1. В палитре команд (F1 или Ctrl + Shift + P) введите и выберите **Azure: Sign in** (Azure: вход). Затем во всплывающем окне нажмите **Копировать* и Открыть**. Вставьте (Ctrl + V) код в свой браузер и нажмите кнопку "Продолжить". Войдите в свою учетную запись Azure. Вы можете просмотреть информацию об учетной записи в строке состояния VS Code.
2. В палитре команд (F1 или Ctrl + Shift + P) введите и выберите  **IoT: Select IoT Hub**  (IoT: выбрать Центр Интернета вещей). Сначала выберите подписку, в которой создали Центр Интернета вещей в предыдущем руководстве. Затем выберите Центр Интернета вещей, который содержит устройство IoT Edge.

    ![Список устройств](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-iot-hub-connection-string"></a>Установка строки подключения Центра Интернета вещей
В палитре команд (F1 или Ctrl + Shift + P) введите и выберите **IoT: Set IoT Hub Connection String** (Центр Интернета вещей: установить строку подключения Центра Интернета вещей). Убедитесь, что вы вставляете строку подключения в раздел политики **iothubowner** (ее можно найти в политике общего доступа Центра Интернета вещей на портале Azure).
 
На панели слева вы можете увидеть список устройств в обозревателе устройств Центра Интернета вещей.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Запуск среды выполнения IoT Edge и развертывание модуля
Установите и запустите среду выполнения Azure IoT Edge на устройстве. Разверните имитационный модуль датчика, который отправит данные телеметрии в Центр Интернета вещей.
1. В палитре команд выберите **Edge: Setup Edge** (Edge: установить Edge) и идентификатор устройства IoT Edge. Или щелкните идентификатор устройства Edge правой кнопкой мыши в списке устройств и выберите **Setup Edge** (Установить Edge).

    ![Установка среды выполнения Edge](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. В палитре команд выберите **Edge: Start Edge**(Edge: запустить Edge), чтобы запустить среду выполнения Edge. В окне интегрированного терминала можно увидеть соответствующие выходные данные.

    ![Среда выполнения Edge](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Проверьте состояние среды выполнения Edge в обозревателе Docker. Зеленый цвет означает, что он работает. Среда выполнения IoT Edge запущена успешно.

    ![Среда выполнения работает](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Теперь среда выполнения Edge работает. Это означает, что ваш компьютер имитирует устройство Edge. Следующим шагом является симуляция sensorthing, который отправляет сообщения на устройство Edge. В палитре команд введите и выберите **Edge: Generate Edge configuration file** (Edge: создать файл конфигурации Edge). Выберите папку для создания этого файла. В созданном файле deployment.json замените содержимое `<registry>/<image>:<tag>` на `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. После этого сохраните файл.

    ![Модуль датчика](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Выберите **Edge: Create deployment for Edge device** (Edge: создать развертывание для устройства Edge) и идентификатор устройства Edge, чтобы создать развертывание. Или же вы можете щелкнуть идентификатор устройства Edge правой кнопкой мыши в списке устройств и выбрать **Create deployment for Edge device**(Создать развертывание для устройства Edge). 

6. Вы должны увидеть, что ваше устройство IoT Edge запускается в обозревателе Docker с помощью имитируемого датчика. Щелкните контейнер правой кнопкой мыши в обозревателе Docker. Вы можете просматривать журналы Docker для каждого модуля. Кроме того, вы можете просмотреть список модулей в списке устройств.

    ![Список модулей](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Щелкните идентификатор устройства Edge правой кнопкой мыши и вы сможете отслеживать сообщения D2C в VS Code.
8. Чтобы остановить работу среды выполнения IoT Edge и сенсорного модуля, в палитре команд введите и выберите **Edge: Stop Edge** (Edge: остановить Edge).

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Разработка и развертывание модуля C# в VS Code
В руководстве [Develop and deploy a C# IoT Edge module to your simulated device — preview](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module) (Разработка модуля IoT Edge с кодом C# и его развертывание на имитированном устройстве (предварительная версия)) вы обновляете, создаете и публикуете образ модуля в VS Code, а затем переходите на портал Azure для развертывания модуля C #. В этом разделе описывается, как использовать VS Code для развертывания и мониторинга модуля C#.

### <a name="start-a-local-docker-registry"></a>Запуск локального реестра Docker
Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом разделе используется [локальный реестр Docker](https://docs.docker.com/registry/deploying/), который легче тестировать на этапе ранней разработки.
В **интегрированном терминале**(Ctrl + `) VS Code выполните следующие команды для запуска локального реестра.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> В приведенном выше примере показаны конфигурации реестра, которые подходят только для тестирования. Готовый к выпуску реестр должен быть защищен протоколом TLS и, в идеале, использовать механизм контроля доступа. Для развертывания готовых к выпуску модулей IoT Edge рекомендуем использовать [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или [центр Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

### <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать модуль IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge. Если вы выполнили этот раздел в предыдущем руководстве, здесь его можно пропустить.
1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.
3. Во встроенном терминале введите следующую команду для установки (или обновления) шаблона **AzureIoTEdgeModule** в dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Создайте проект для нового модуля. Приведенная ниже команда создает папку проекта **FilterModule** в текущей рабочей папке.

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Выберите **Файл** > **Открыть папку**.
4. Перейдите в папку **FilterModule** и щелкните **Выбрать папку**, чтобы открыть проект в VS Code.
5. В проводнике VS Code щелкните файл **Program.cs**, чтобы открыть его. В верхней части **program.cs** укажите следующие пространства имен.
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Добавьте переменную `temperatureThreshold` в класс **Program**. Эта переменная задает значение, которое должно быть превышено измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Добавьте классы `MessageBody`, `Machine` и `Ambient` в класс **Program**. Эти классы определяют ожидаемую схему текста входящего сообщения.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

8. В методе **Init** код создает и настраивает объект **DeviceClient**. Этот объект позволяет модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. Строка подключения, используемая в методе **Init**, предоставляется модулю средой выполнения IoT Edge. После создания **DeviceClient** этот код регистрирует обратный вызов для получения сообщений из концентратора IoT Edge через конечную точку **input1**. Замените метод `SetInputMessageHandlerAsync` новым методом и добавьте метод `SetDesiredPropertyUpdateCallbackAsync` для обновления нужных свойств. Чтобы внести это изменение, замените последнюю строку метода **Init** следующим кодом:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Добавьте метод `onDesiredPropertiesUpdate` в класс **Program**. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный двойник модуля, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
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
    ```

10. Замените метод `PipeMessage` методом `FilterMessages`. Этот метод вызывается каждый раз, когда модуль получает сообщение из центра IoT Edge. Он отфильтровывает сообщения о температуре ниже порогового значения, настроенного с помощью двойника модуля. Он также добавляет свойство **MessageType** в сообщение со значением **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
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
            DeviceClient deviceClient = (DeviceClient)userContext;
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
    ```

11. Чтобы выполнить сборку проекта, щелкните правой кнопкой мыши файл **FilterModule.csproj** в обозревателе и выберите **Build IoT Edge module** (Выполнить сборку модуля IoT Edge). Модуль будет скомпилирован, после чего в папку, которая используется для создания образа Docker, будет экспортирован двоичный файл и его зависимости. 

    ![Модуль сборки](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. В обозревателе VS Code разверните папку **Docker**. Затем разверните папку для платформы своего контейнера: **linux-x64** или **windows-nano**.
2. Щелкните правой кнопкой мыши файл **Dockerfile** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge). 

    ![Сборка образа Docker](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. В окне **Выбор папки** перейдите к папке или введите путь `./bin/Debug/netcoreapp2.0/publish`. Щелкните **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR).
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filtermodule:latest`. Если развертывание выполняется в локальном реестре, необходимо использовать `localhost:5000/filtermodule:latest`.
5. Отправьте образ в репозиторий Docker. Используйте команду **Edge: Push IoT Edge module Docker image** (Edge: передать образ Docker для модуля IoT Edge) и введите URL-адрес образа во всплывающее текстовое поле в верхней части окна VS Code. Используйте URL-адрес образа, применяемый на предыдущем шаге. Проверьте журнал консоли и убедитесь, что образ был успешно отправлен.

    ![Передать образ Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Pushed docker image](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Развертывание модулей IoT Edge

1. Откройте файл `deployment.json`, замените раздел **модулей** следующим содержимым:
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Замените раздел **маршрутов** следующим содержимым:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры в модуль фильтра через конечную точку input1, которая настроена с помощью обработчика FilterMessages. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте вышестоящий источник является специальным пунктом назначения, который говорит концентратору Edge отправлять сообщения в Центр Интернета вещей.

3. Сохраните этот файл.
4. В палитре команд выберите **Edge: Create deployment for Edge device** (Edge: создать развертывание для устройства Edge). Затем выберите идентификатор устройства IoT Edge для создания развертывания. Или щелкните идентификатор устройства правой кнопкой мыши в списке устройств и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge).

    ![Создать развертывание](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Выберите обновленный файл `deployment.json`. В окне вывода можно увидеть соответствующие выходные данные для развертывания.

    ![Развертывание выполнено](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Запустите среду выполнения Edge в палитре команд. **Edge: запустить Edge**
7. Запуск среды выполнения IoT Edge происходит в обозревателе Docker с помощью имитируемого датчика и модуля фильтра.

    ![Решение IoT Edge работает](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Щелкните идентификатор устройства Edge правой кнопкой мыши и вы сможете отслеживать сообщения D2C в VS Code.


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge и развернули его на устройстве IoT Edge в VS Code. Теперь вы можете перейти к изучению любого из следующих руководств, чтобы узнать о других сценариях при разработке Azure IoT Edge в VS Code.

> [!div class="nextstepaction"]
> [Использование Visual Studio Code для отладки модуля C# с помощью Azure IoT Edge](how-to-vscode-debug-csharp-module.md)
