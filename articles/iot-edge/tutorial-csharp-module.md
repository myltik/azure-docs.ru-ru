---
title: Модуль Azure IoT Edge с кодом C# | Документация Майкрософт
description: Создание модуля IoT Edge с кодом C# и его развертывание на пограничном устройстве
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 11c737adb6578437a3708bb97397a24114e39585
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Разработка модуля IoT Edge с кодом C# и его развертывание на имитированном устройстве (предварительная версия)

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы используете имитированное устройство IoT Edge, созданное при работе с руководством по развертыванию Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять такие задачи:    

> [!div class="checklist"]
> * Использование кода Visual Studio для создания модуля IoT Edge на основе .NET Сore 2.0.
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикация в реестре. 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, созданные вашим устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

## <a name="prerequisites"></a>предварительным требованиям

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или первым руководством.
* Строка подключения первичного ключа для устройства IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) на компьютере с Visual Studio Code. Для этого руководства достаточно выпуска Community Edition (CE). 
* [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.
2. Укажите имя реестра, выберите подписку, выберите группу ресурсов и задайте номер SKU **Базовый**. 
3. Нажмите кнопку **Создать**.
4. После создания реестра контейнеров перейдите к нему и выберите **Ключи доступа**. 
5. **Включите** параметр **Пользователь-администратор**.
6. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения будут использоваться далее в руководстве при публикации образа Docker в реестре и добавлении учетных данных реестра в среду выполнения Edge. 

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать модуль IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge.
1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.
2. Во встроенном терминале введите следующую команду для установки (или обновления) шаблона **AzureIoTEdgeModule** в dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Создайте проект для нового модуля. Приведенная ниже команда создает папку проекта **FilterModule** в указанном репозитории контейнеров. Если вы используете реестр контейнеров, второй параметр должен быть в формате `<your container registry name>.azurecr.io`. В текущей рабочей папке введите следующую команду:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Выберите **Файл** > **Открыть папку**.
5. Перейдите в папку **FilterModule** и щелкните **Выбрать папку**, чтобы открыть проект в VS Code.
6. В проводнике VS Code щелкните файл **Program.cs**, чтобы открыть его.

   ![Откройте файл Program.cs.][1]

7. В верхней части пространства имен **FilterModule** добавьте три инструкции `using` для типов, которые будут использоваться позже:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Добавьте переменную `temperatureThreshold` в класс **Program**. Эта переменная задает значение, которое должно быть превышено измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Добавьте классы `MessageBody`, `Machine` и `Ambient` в класс **Program**. Эти классы определяют ожидаемую схему текста входящего сообщения.

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

10. В методе **Init** код создает и настраивает объект **DeviceClient**. Этот объект позволяет модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. Строка подключения, используемая в методе **Init**, предоставляется модулю средой выполнения IoT Edge. После создания **DeviceClient** этот код читает пороговое значение температуры из соответствующих свойств двойника модуля и регистрирует обратный вызов для получения сообщений из концентратора IoT Edge через конечную точку **input1**. Замените метод `SetInputMessageHandlerAsync` новым методом и добавьте метод `SetDesiredPropertyUpdateCallbackAsync` для обновления нужных свойств. Чтобы внести это изменение, замените последнюю строку метода **Init** следующим кодом:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    if (moduleTwinCollection["TemperatureThreshold"] != null)
    {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Добавьте метод `onDesiredPropertiesUpdate` в класс **Program**. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный двойник модуля, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

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

12. Замените метод `PipeMessage` методом `FilterMessages`. Этот метод вызывается каждый раз, когда модуль получает сообщение из центра IoT Edge. Он отфильтровывает сообщения о температуре ниже порогового значения, настроенного с помощью двойника модуля. Он также добавляет свойство **MessageType** в сообщение со значением **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

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
    ```

13. Сохраните этот файл.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала VS Code. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Чтобы найти имя пользователя, пароль и сервер входа для использования в этой команде, перейдите на [портал Azure] (https://portal.azure.com). В разделе **Все ресурсы** щелкните плитку вашего реестра контейнеров Azure, чтобы открыть его свойства, а затем щелкните **Ключи доступа**. Скопируйте значения полей **Имя пользователя**, **Пароль** и **Сервер входа**. 

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **module.json** и выберите действие **Build and Push IoT Edge module Docker image** (Создать и отправить образ Docker для модуля IoT Edge). Во всплывающем раскрывающемся списке в верхней части окна VS Code выберите платформу контейнера: **amd64** для контейнера Linux или **windows-amd64** для контейнера Windows. VS Code соберет код, поместит в контейнер файл `FilterModule.dll` и передаст его в указанный вами реестр контейнеров.


3. Полный адрес образа контейнеров с тегом можно получить через интегрированный терминал VS Code. Дополнительные сведения об определении сборки и отправки см. в файле `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Добавление учетных данных реестра в среду выполнения Edge
Добавьте учетные данные для своего реестра в среду выполнения Edge на компьютере, где запущено устройство Edge. С их помощью среда выполнения сможет получить доступ для извлечения контейнера. 

- В ОС Windows выполните следующую команду:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- В ОС Linux выполните следующую команду:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Запуск решения

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com).
2. Щелкните **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
3. Щелкните **Set Modules** (Настроить модули). 
4. Убедитесь, что модуль **tempSensor** заполняется автоматически. Если это не так, следуйте инструкциям ниже, чтобы добавить его.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.
5. Добавьте модуль **filterModule**, созданный в предыдущих разделах. 
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `filterModule`.
    3. В поле **URI изображения** введите адрес образа, например `<your container registry address>/filtermodule:0.0.1-amd64`. Полный адрес образа можно найти в предыдущем разделе.
    4. Установите флажок **Включить**, чтобы получить возможность изменить двойник модуля. 
    5. Замените JSON-файл в текстовом поле для двойника модуля следующим JSON-файлом: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Выберите команду **Сохранить**.
6. Нажмите кнопку **Далее**.
7. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Модули публикуют все сообщения в среду выполнения Edge. Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры в модуль фильтра через конечную точку input1, которая настроена с помощью обработчика **FilterMessages**. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте `upstream` является специальным пунктом назначения, который говорит концентратору Edge отправлять сообщения в Центр Интернета вещей. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Нажмите кнопку **Далее**.
9. На шаге **Review Template** (Проверка шаблона) щелкните **Отправить**. 
10. Вернитесь на страницу сведений об устройстве IoT Edge и щелкните **Обновить**. Вы должны увидеть новый модуль **filtermodule**, работающий вместе с модулем **tempSensor** и **средой выполнения IoT Edge**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отслеживать сообщения между устройством и облаком, отправленные с вашего устройства IoT Edge в Центр Интернета вещей:
1. Настройте расширение Azure IoT Toolkit со строкой подключения для вашего Центра Интернета вещей: 
    1. Откройте обозреватель VS Code, выбрав **Вид** > **Обозреватель**. 
    2. В проводнике щелкните **IOT HUB DEVICES** (Устройства Центра Интернета вещей) и нажмите кнопку **...**. Щелкните **Set IoT Hub Connection String** (Настроить строку подключения Центра Интернета вещей) и введите во всплывающем окне строку подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge. 

        Чтобы найти строку подключения, щелкните плитку своего Центра Интернета вещей на портале Azure и выберите **Политики общего доступа**. В колонке **Политики общего доступа** выберите политику **iothubowner**, а затем скопируйте строку подключения Центра Интернета вещей в окне **iothubowner**.   

2. Чтобы отслеживать данные, поступающие в Центр Интернета вещей, выберите**Вид** > **Палитра команд** и щелкните пункт меню **IoT: Start monitoring D2C message** (Интернет вещей: начать мониторинг сообщений D2C). 
3. Чтобы перестать отслеживать данные, щелкните пункт меню **IoT: Stop monitoring D2C message** (Интернет вещей: остановить мониторинг сообщений D2C). 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Вы можете перейти к одному из следующих руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве

> [!div class="nextstepaction"]
> [Deploy Azure Function as an IoT Edge module - preview](tutorial-deploy-function.md)
>  (Развертывание функции Azure в виде модуля IoT Edge (предварительная версия))[Deploy Azure Stream Analytics as an IoT Edge module - preview](tutorial-deploy-stream-analytics.md) (Развертывание Azure Stream Analytics в виде модуля IoT Edge (предварительная версия))


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
