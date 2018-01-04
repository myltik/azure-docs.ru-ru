---
title: "Использовать кода Visual Studio для разработки модуля C# с краем IoT Azure | Документы Microsoft"
description: "Разработки и развертывания модуля C# с краем IoT Azure в VS Code без переключения контекста"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Использовать кода Visual Studio для разработки модуля C# с краем IoT Azure
В этой статье приведены подробные инструкции по использованию [кода Visual Studio](https://code.visualstudio.com/) как основное средство разработки для разработки и развертывания модулей IoT Edge. 

## <a name="prerequisites"></a>Технические условия
Предполагается, что вы используете компьютер или виртуальную машину с компьютера разработчика Windows или Linux. IoT пограничного устройства может быть другое физическое устройство или устройство пограничного IoT, вы можете имитировать на компьютере разработки.

Убедитесь, что у вас есть завершенного следующие учебники перед началом в этом руководстве.
- Развертывание на имитированное устройство в Azure IoT Edge [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) или [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Разрабатывать и развертывать модуля IoT Edge C# имитированное устройство](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Ниже приведен контрольный список, который показывает элементы должны иметь после завершения предыдущей учебники.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Сценарий IoT края элемента управления](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Шаблон AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Центр IoT active с по крайней мере IoT периферийным устройством.

Также рекомендуется установить [Docker поддержка VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для лучшего управления образов и контейнеры.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Развернуть модуль Azure IoT Edge в VS Code

### <a name="list-your-iot-hub-devices"></a>Список устройств IoT hub
Чтобы вывести список устройств IoT hub в коде VS двумя способами. Можно продолжать в любом случае.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>Войдите в учетную запись Azure в VSCode и выберите концентратор IoT
1. Введите в палитру команд (F1 или Ctrl + Shift + P) и выберите **Azure: вход**. Нажмите кнопку  **копирования* & откройте** в всплывающем окне. Вставьте (Ctrl + V) в браузере в код и нажмите кнопку "Продолжить". Войдите в систему с учетной записью Azure. Можно просмотреть сведения вашей учетной записи в строке состояния VS Code.
2. Введите в палитру команд (F1 или Ctrl + Shift + P) и выберите **IoT: выберите центр IoT**. Сначала выбрать подписки, где вы создали концентратор IoT из предыдущего учебника. Выберите центр IoT, содержащий IoT Пограничное устройство.


#### <a name="set-iot-hub-connection-string"></a>Задание строки подключения концентратора IoT
1. Введите в палитру команд (F1 или Ctrl + Shift + P) и выберите **IoT: задайте строку подключения концентратора IoT**. Убедитесь, что вы вставьте строку подключения в рамках политики **iothubowner** (можно найти его в ваш концентратора IoT. политики общего доступа на портале Azure).
 

Появится список устройств, в обозревателе устройств IoT Hub в левой боковой панели.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Запуск среды выполнения к IoT Edge и развертывание модуля
Установите и запустите среду выполнения Azure IoT Edge на устройстве. И развернуть модуль имитацию датчика, который будет отправлять данные телеметрии в центр IoT.
1. В палитру команд, выберите **Edge: Edge установки** и выберите ваш IoT Edge идентификатор устройства. Или щелкните правой кнопкой мыши идентификатор устройства Edge в список устройств и выберите **Edge установки**.
2. В палитру команд, выберите **Edge: запуск Edge** для запуска вашей среды выполнения Edge. Можно увидеть соответствующие выходные данные в интеграции терминала.
3. Проверьте состояние среды выполнения Edge в обозревателе Docker. Зеленый цвет означает, что он работает. Ваш среда выполнения IoT Edge запущена успешно.
4. Теперь работает на край среды выполнения, что означает ПК теперь имитирует периферийным устройством. Следующим шагом является sensorthing, который отслеживает отправлять сообщения на ваше устройство пограничного имитации. В палитру команд, введите и выберите **Edge: файл конфигурации для создания границы**. И выберите папку для создания этого файла. В файле созданный deployment.json замените строку «<registry>/<image>:<tag>» с `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
5. Выберите **Edge: Создание развертывания для периферийным устройством** и выберите код пограничного устройства для создания нового развертывания. Или щелкните правой кнопкой мыши идентификатор устройства Edge в списке устройств и выберите **Создание развертывания для периферийным устройством**. 
6. Вы увидите вашей IoT Edge для запуска в обозревателе Docker с имитацию датчика. Щелкните правой кнопкой мыши контейнер, в обозревателе Docker. Вы можете ознакомиться с docker журналы для каждого модуля.
7. Щелкните правой кнопкой мыши на край ИД устройства, и можно отслеживать сообщения D2C в VS Code.
8. Для остановки вашей IoT Edge среды выполнения и модуль датчика, можно ввести и выберите **Edge: остановить Edge** в палитру команд.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Разработка и развертывание модуля C# в VS Code
В учебнике [разработке модуля C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), обновлением построения и опубликовать модуль изображения в VS Code и затем посетите портал Azure для развертывания модуля C#. В этом разделе Знакомство с использованием VS Code для развертывания и контроля модуля C#.

### <a name="start-a-local-docker-registry"></a>Запуск реестра локального docker
Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом разделе используется [локального реестра Docker](https://docs.docker.com/registry/deploying/), который проще для тестирования во время разработки раннее.
В VS Code **интеграции терминалов**(Ctrl + "), выполнить следующие команды для запуска в реестре локального компьютера.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Предыдущий пример показывает конфигурации реестра, которые подходят только для тестирования. Готовый реестра должен быть защищен протоколом TLS и в идеале следует использовать механизм управления доступом. Рекомендуется использовать [реестра контейнера Azure](https://docs.microsoft.com/azure/container-registry/) или [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) размещать модули IoT край рабочей среде.

### <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать модуль IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge. После выполнения в этом разделе в предыдущем учебнике можно безопасно пропустить в этом разделе.
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
5. В проводнике VS Code щелкните файл **Program.cs**, чтобы открыть его.
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


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. В обозревателе VS Code разверните папку **Docker**. Затем разверните папку для платформы своего контейнера: **linux-x64** или **windows-nano**.
2. Щелкните правой кнопкой мыши файл **Dockerfile** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge). 
3. В окне **Выбор папки** перейдите к папке или введите путь `./bin/Debug/netcoreapp2.0/publish`. Щелкните **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR).
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filtermodule:latest`. Если развертывается в локальном реестре, он должен быть `localhost:5000/filtermodule:latest`.
5. Отправьте образ в репозиторий Docker. Используйте **Edge: образа Docker модуля Push IoT Edge** команд и введите URL-адрес изображения в поле всплывающие подсказки в верхней части окна VS Code. Используйте же URL-адрес изображения использовался выше шаг.

### <a name="deploy-your-iot-edge-modules"></a>Развертывание модулей IoT Edge

1. Откройте `deployment.json` файла, замените **модули** с разделом содержимого:
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

2. Замените **маршруты** с разделом содержимого:
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения из датчик температуры модуля фильтра через конечную точку «input1», который является конечной точкой, настроенной с обработчиком FilterMessages. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте вышестоящего специальные назначения, который сообщает концентратора Edge для отправки сообщений в центр IoT.

3. Сохраните этот файл.
4. Выберите в палитру команд **Edge: Создание развертывания для периферийным устройством**. Выберите ИД устройства IoT Edge для создания развертывания. Или щелкните правой кнопкой мыши идентификатор устройства в списке устройств и выберите **Создание развертывания для периферийным устройством**.
5. Выберите `deployment.json` вам о новостях. В окне вывода появится соответствующие выходные данные для развертывания.
6. Запуск вашей среды выполнения Edge в палитру команд. **Граница: Начальному краю**
7. Можно просмотреть на край IoT среды выполнения для запуска в обозревателе Docker с имитацию датчика и модуль фильтра.
8. Щелкните правой кнопкой мыши на край ИД устройства, и можно отслеживать сообщения D2C в VS Code.


## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике модуль IoT Edge создан и развернут в IoT периферийным устройством в VS Code. Можно перейти к любой из следующих учебников, чтобы узнать о других сценариях при разработке Azure IoT край VS Code.

> [!div class="nextstepaction"]
> [Отладка модуля C# в VS Code](how-to-vscode-debug-csharp-module.md)
