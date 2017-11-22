---
title: "Развертывание Функции Azure с помощью Azure IoT Edge | Документация Майкрософт"
description: "Развертывание Функции Azure в виде модуля на граничном устройстве"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 34ed5083b952c42d4ed119b6986db965eb9eb67a
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Развертывание Функции Azure в виде модуля IoT Edge — предварительный просмотр
Вы можете использовать Функции Azure для развертывания кода, который реализует нужную бизнес-логику, непосредственно на устройствах IoT Edge. В этом руководстве описывается создание и развертывание Функции Azure, которая фильтрует данные датчиков на виртуальном устройстве IoT Edge, созданном по инструкциям в руководствах по развертыванию Azure IoT Edge на виртуальном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять такие задачи:     

> [!div class="checklist"]
> * Использование Visual Studio Code для создания функции Azure
> * Использование VS Code и Docker для создания образа Docker и его публикация в реестре. 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Функция Azure, которую вы создадите в этом руководстве, фильтрует сформированные устройством данные о температуре и отправляет сообщения в вышестоящий Центр Интернета вещей Azure, если температура превышает заданный порог. 

## <a name="prerequisites"></a>Предварительные требования

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или предыдущим руководством.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). Расширение можно установить с помощью панели расширений в Visual Studio Code.
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). Расширение можно установить с помощью панели расширений в Visual Studio Code.
* [Docker](https://docs.docker.com/engine/installation/). Для этого руководства достаточно выпуска Community Edition (CE) для вашей платформы. 
* [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Настройка реестра Docker
В этом руководстве используется расширение Azure IoT Edge для VS Code, чтобы создать функцию Azure и собрать на ее основе образ [Docker](https://docs.docker.com/glossary/?term=image). Затем вы отправите этот образ Docker в [репозиторий Docker](https://docs.docker.com/glossary/?term=repository), размещенный в [реестре Docker](https://docs.docker.com/glossary/?term=registry). Наконец, вы развернете образ Docker, упакованный в виде [контейнера Docker](https://docs.docker.com/glossary/?term=container), из вашего реестра на свое устройство IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — **реестр контейнеров Azure** и **Центр Docker**:

- [Реестр контейнеров Azure](https://docs.microsoft.com/en-us/azure/container-registry/) доступен в [платной подписке](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Для работы с этим руководством достаточно подписки уровня **Стандартный**. 

- [Центр Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) предлагает один бесплатный частный репозиторий, если вы регистрируетесь для получения (бесплатного) идентификатора Docker. 
    1. Чтобы зарегистрироваться для получения идентификатора Docker, следуйте инструкциям [по регистрации](https://docs.docker.com/docker-id/#register-for-a-docker-id) на сайте Docker. 

    2. Чтобы создать частный репозиторий Docker, следуйте инструкциям [по созданию репозитория в Центре Docker](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) на сайте Docker.

В этом руководстве предоставлены команды как для реестра контейнеров Azure, так и для Центра Docker, где это необходимо.

## <a name="create-a-function-project"></a>Создание проекта функции
Следующий процесс позволяет создать модуль IoT Edge с использованием Visual Studio Code и расширения Azure IoT Edge.
1. Откройте VS Code.
2. Используйте команду меню **Вид | Интегрированный терминал**, чтобы открыть встроенный терминал VS Code.
3. Во встроенном терминале введите следующую команду для установки (или обновления) шаблона **AzureIoTEdgeFunction** в dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. В окне встроенного терминала введите следующую команду, чтобы создать проект нового модуля:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Эта команда создает папку проекта **FilterFunction** в текущей рабочей папке. Если вы хотите создать ее в другом расположении, перед выполнением команды перейдите в другой каталог.

3. Используйте команду меню **Файл | Открыть папку**, перейдите в папку **FilterFunction** и щелкните **Выбрать папку**, чтобы открыть проект в VS Code.
4. В обозревателе VS Code щелкните папку **EdgeHubTrigger-Csharp** а затем откройте файл **run.csx**.
5. Добавьте после оператора `#r "Microsoft.Azure.Devices.Client"` следующий оператор:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Добавьте после существующего оператора `using` следующий оператор:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Добавьте следующие классы. Эти классы определяют ожидаемую схему текста входящего сообщения.

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

1. Замените текст метода **Run** следующим кодом. Он фильтрует сообщения на основе значения температуры в тексте сообщения и порогового значения температуры.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transfered a message with temperature above the threshold");
        }
    }
    ```

11. Сохраните файл.

## <a name="publish-a-docker-image"></a>Публикация образа Docker

1. Выполните сборку образа Docker.
    1. В проводнике VS Code откройте папку **Docker**. Выберите папку для платформы своего контейнера: **linux-x64** или **windows-nano**. 
    2. Щелкните правой кнопкой мыши файл **Dockerfile** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge). 
    3. В поле **Выбор папки** перейдите к папке **Docker/linux-x64** и щелкните **Select Folder as EXE_DIR** (Выбрать папку как EXE_DIR). 
    4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<docker registry address>/filterfunction:latest`, где *docker registry address* — это идентификатор Docker, если вы используете центр Docker, или адрес `<your registry name>.azurecr.io`, если вы используете реестр контейнеров Azure.
 
4. Войдите в Docker. Во встроенном терминале введите следующую команду: 

    - Центр Docker (введите учетные данные при появлении запроса):
        
        ```csh/sh
        docker login
        ```

    - Реестр контейнеров Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Чтобы найти имя пользователя, пароль и сервер входа для использования в этой команде, перейдите на [портал Azure] (https://portal.azure.com). В разделе **Все ресурсы** щелкните плитку вашего реестра контейнеров Azure, чтобы открыть его свойства, а затем щелкните **Ключи доступа**. Скопируйте значения полей **Имя пользователя**, **Пароль** и **Сервер входа**. Сервер входа должен иметь формат `<your registry name>.azurecr.io`.

3. Отправьте образ в репозиторий Docker. Используйте команду меню **Вид | Палитра команд... | Edge: Push IoT Edge module Docker image** (Передать образ Docker для модуля IoT Edge) и введите имя образа в текстовое поле всплывающего окна в верхней части окна VS Code. Укажите имя образа, выбранное на шаге 1.3.

## <a name="add-registry-credentials-to-your-edge-device"></a>Добавление учетных данных реестра на устройство Edge
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

1. Найдите нужный Центр Интернета вещей на **портале Azure**.
2. Перейдите к **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
1. Щелкните **Set Modules** (Настроить модули). 
2. Добавьте модуль **tempSensor**. Это действие необходимо только в том случае, если ранее на устройстве IoT Edge не был развернут модуль **tempSensor**.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.
1. Добавьте модуль **filterfunction**.
    1. Снова выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `filterfunction`.
    3. В поле **Образ** введите адрес образа, например `<docker registry address>/filterfunction:latest`.
    74. Щелкните **Сохранить**.
2. Щелкните **Далее**.
3. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Модули публикуют все сообщения в среду выполнения Edge. Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры в модуль фильтра через конечную точку input1, которая настроена с помощью обработчика **FilterMessages**. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте `upstream` является специальным пунктом назначения, который говорит концентратору Edge отправлять сообщения в Центр Интернета вещей. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
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

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Чтобы продолжить изучение функций Azure IoT Edge, перейдите к статье об использовании устройства IoT Edge в качестве шлюза. 

> [!div class="nextstepaction"]
> [Create an IoT Edge gateway device](how-to-create-gateway-device.md) (Создание устройства шлюза IoT Edge для обработки данных с других устройств Интернета вещей (предварительная версия))

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
