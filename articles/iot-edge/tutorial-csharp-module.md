---
title: "Модуль Azure IoT Edge с кодом C# | Документация Майкрософт"
description: "Создание модуля IoT Edge с кодом C# и его развертывание на пограничном устройстве"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Разработка модуля IoT Edge с кодом C# и его развертывание на имитированном устройстве (предварительная версия)

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве описывается создание и развертывание модуля IoT Edge, который фильтрует данные датчиков на имитированном устройстве IoT Edge, созданном по указаниям в руководствах по развертыванию Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять такие задачи:    

> [!div class="checklist"]
> * Использование кода Visual Studio для создания модуля IoT Edge на основе .NET Сore 2.0.
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикация в реестре. 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных.


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные о температуре, сформированные вашим устройством, и отправляет сообщения вышестоящим объектам только тогда, когда температура превышает заданный порог. 

## <a name="prerequisites"></a>Предварительные требования

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или предыдущим руководством.
* Строка подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Расширение можно установить с помощью панели расширений в Visual Studio Code.)
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Расширение можно установить с помощью панели расширений в Visual Studio Code.)
* Расширение Azure IoT Edge для Visual Studio Code
* [Docker](https://docs.docker.com/engine/installation/). Для этого руководства достаточно выпуска Community Edition (CE) для вашей платформы. Убедитесь, что вы установили его на компьютер, где выполняется Visual Studio Code.
* [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Выбор или регистрация для использования реестра Docker
В этом руководстве вы создадите модуль и образ [Docker](https://docs.docker.com/glossary/?term=image), используя расширение Azure IoT Edge для VS Code. Затем вы отправите образ Docker в [репозиторий Docker](https://docs.docker.com/glossary/?term=repository), размещенный в [реестре Docker](https://docs.docker.com/glossary/?term=registry). Наконец, вы развернете образ Docker, упакованный в виде [контейнера Docker](https://docs.docker.com/glossary/?term=container), из вашего реестра на свое устройство IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — **реестр контейнеров Azure** и **Центр Docker**:

- [Реестр контейнеров Azure](https://docs.microsoft.com/en-us/azure/container-registry/) доступен в [платной подписке](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Для работы с этим руководством достаточно подписки уровня **Стандартный**. 

- [Центр Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) предлагает один бесплатный частный репозиторий, если вы регистрируетесь для получения (бесплатного) идентификатора Docker. 
    1. Чтобы зарегистрироваться для получения идентификатора Docker, следуйте инструкциям [по регистрации](https://docs.docker.com/docker-id/#register-for-a-docker-id) на сайте Docker. 

    2. Чтобы создать частный репозиторий Docker, следуйте инструкциям [по созданию репозитория в Центре Docker](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) на сайте Docker.

В этом руководстве предоставлены команды для реестра контейнеров Azure и Центра Docker, где это необходимо.

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать модуль IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge.
1. Откройте VS Code.
2. Используйте команду меню **Вид | Интегрированный терминал**, чтобы открыть встроенный терминал VS Code.
3. Во встроенном терминале введите следующую команду для установки (или обновления) шаблона **AzureIoTEdgeModule** в dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. В окне встроенного терминала введите следующую команду, чтобы создать проект нового модуля:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Эта команда создает папку проекта **FilterModule** в текущей рабочей папке. Если вы хотите создать ее в другом расположении, перейдите в другой каталог перед выполнением команды.
 
3. Используйте команду меню **Файл | Открыть папку**, перейдите в папку **FilterModule** и щелкните **Выбрать папку**, чтобы открыть проект в VS Code.
4. В проводнике VS Code щелкните файл **Program.cs**, чтобы открыть его.
5. Добавьте следующее поле в класс **Program**.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Добавьте следующие классы в класс **Program**. Эти классы определяют ожидаемую схему текста входящего сообщения.

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

1. В методе **Init** код создает и настраивает объект **DeviceClient**. Этот объект позволяет модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. Параметр строки подключения, используемый в методе **Init**, предоставляется модулю с помощью среды выполнения IoT Edge в переменной среды **EdgeHubConnectionString**. После создания **DeviceClient** этот код регистрирует обратный вызов для получения сообщений из концентратора IoT Edge через конечную точку **input1**. Замените метод, используемый в качестве обратного вызова для обработки сообщений, на новый и добавьте обратный вызов для получения необходимых обновлений свойств в двойнике модуля. Чтобы внести это изменение, замените последнюю строку метода **Init** следующим кодом:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Добавьте следующий метод в класс **Program**, чтобы обновить поле **temperatureThreshold** на основе желаемых свойств, отправленных внутренней службой через двойник модуля. Все модули имеют собственный двойник. Двойник модуля позволяет встроенной службе настраивать код, выполняемый внутри модуля.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. Замените метод **PipeMessage** следующим методом: Этот метод вызывается каждый раз, когда модуль отправляет сообщение из концентратора Edge. Он фильтрует сообщения на основе значения температуры в тексте сообщения и пороге температуры, настроенном через двойник модуля.

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

11. Создайте проект. Используйте команду меню **Вид | Проводник**, чтобы открыть проводник VS Code. В проводнике щелкните правой кнопкой мыши файл **FilterModule.csproj** и выберите **Build IoT Edge module** (Создать модуль IoT Edge), чтобы скомпилировать модуль и экспортировать двоичный файл и его зависимости в папку, из которой создается образ Docker на следующем шаге.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. Создайте образ Docker.
    1. В проводнике VS Code щелкните папку **Docker**, чтобы открыть ее. Выберите папку для платформы своего контейнера: **linux-x64** или **windows-nano**. 
    2. Щелкните правой кнопкой мыши файл **Dockerfile** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge). 
    3. В поле **Выбор папки** перейдите к папке или введите путь `./bin/Debug/netcoreapp2.0/publish`. Щелкните **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR).
    4. В текстовом поле всплывающего окна в верхней части окна VS Code введите имя образа. Например, `<docker registry address>/filtermodule:latest`, где *docker registry address* — это идентификатор Docker, если вы используете концентратор Docker, или аналогичный `<your registry name>.azurecr.io`, если вы используете реестр контейнеров Azure.
 
4. Войдите в Docker. Во встроенном терминале введите следующую команду: 

    - Центр Docker (введите учетные данные при появлении запроса):
        
        ```csh/sh
        docker login
        ```

    - Для реестра контейнеров Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Чтобы найти имя пользователя, пароль и сервер входа для использования в этой команде, перейдите на [портал Azure] (https://portal.azure.com). В разделе **Все ресурсы** щелкните плитку вашего реестра контейнеров Azure, чтобы открыть его свойства, а затем щелкните **Ключи доступа**. Скопируйте значения полей **Имя пользователя**, **Пароль** и **Сервер входа**. Сервер входа должен иметь формат `<your registry name>.azurecr.io`.

3. Отправьте образ в репозиторий Docker. Используйте команду меню **Вид | Палитра команд... | Edge: Push IoT Edge module Docker image** (Передать образ Docker для модуля IoT Edge) и введите имя образа в текстовое поле всплывающего окна в верхней части окна VS Code. Укажите имя образа, выбранное на шаге 1.3.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Добавление учетных данных реестра в среду выполнения Edge на устройстве Edge
Добавьте учетные данные для своего реестра в среду выполнения Edge на компьютере, где запущено устройство Edge. Таким образом среда выполнения сможет получить доступ к контейнеру. 

- В ОС Windows выполните следующую команду:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- В ОС Linux выполните следующую команду:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Запуск решения

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com).
2. Перейдите к **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
3. Щелкните **Set Modules** (Настроить модули). 
2. Добавьте модуль **tempSensor**. Это действие необходимо только в том случае, если ранее на устройстве IoT Edge не был развернут модуль **tempSensor**.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.
9. Добавьте **filtermodule**.
    1. Снова выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `filtermodule`.
    3. В поле **Образ** введите адрес образа, например `<docker registry address>/filtermodule:latest`.
    4. Установите флажок **Edit module twin** (Редактировать двойник модуля).
    5. Замените JSON-файл в текстовом поле для двойника модуля следующим JSON-файлом: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Щелкните **Сохранить**.
12. Щелкните **Далее**.
13. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Модули публикуют все сообщения в среду выполнения Edge. Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры в модуль фильтра через конечную точку input1, которая настроена с помощью обработчика **FilterMessages**. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте `upstream` является специальным пунктом назначения, который говорит концентратору Edge отправлять сообщения в Центр Интернета вещей. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Щелкните **Далее**.
5. На шаге **Review Template** (Проверка шаблона) щелкните **Отправить**. 
6. Вернитесь на страницу сведений об устройстве IoT Edge и щелкните **Обновить**. Вы должны увидеть новый модуль **filtermodule**, работающий вместе с модулем **tempSensor** и **средой выполнения IoT Edge**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отслеживать сообщения между устройством и облаком, отправленные с вашего устройства IoT Edge в Центр Интернета вещей:
1. Настройте расширение Azure IoT Toolkit со строкой подключения для вашего Центра Интернета вещей: 
    1. Используйте команду меню **Вид | Проводник**, чтобы открыть проводник VS Code. 
    3. В проводнике щелкните **IOT HUB DEVICES** (Устройства Центра Интернета вещей) и нажмите кнопку **...**. Щелкните **Set IoT Hub Connection String** (Настроить строку подключения Центра Интернета вещей) и введите во всплывающем окне строку подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge. 

        Чтобы найти строку подключения, щелкните плитку своего Центра Интернета вещей на портале Azure и выберите **Политики общего доступа**. В колонке **Политики общего доступа** выберите политику **iothubowner**, а затем скопируйте строку подключения Центра Интернета вещей в окне **iothubowner**.   

1. Чтобы отслеживать данные, поступающие в Центр Интернета вещей, используйте команду меню **Вид | Палитра команд... | Центр Интернета вещей: Start monitoring D2C message** (Начать мониторинг сообщений D2C). 
2. Чтобы перестать отслеживать данные, используйте команду меню **Вид | Палитра команд... | Центр Интернета вещей: Stop monitoring D2C message** (Остановить мониторинг сообщений D2C). 

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Вы можете перейти к одному из следующих руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве

> [!div class="nextstepaction"]
> [Deploy Azure Function as an IoT Edge module - preview](tutorial-deploy-function.md)
>  (Развертывание функции Azure в виде модуля IoT Edge (предварительная версия))[Deploy Azure Stream Analytics as an IoT Edge module - preview](tutorial-deploy-stream-analytics.md) (Развертывание Azure Stream Analytics в виде модуля IoT Edge (предварительная версия))


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
