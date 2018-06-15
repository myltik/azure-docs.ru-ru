---
title: Развертывание Функции Azure с помощью Azure IoT Edge | Документация Майкрософт
description: Развертывание Функции Azure в виде модуля на граничном устройстве
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6102a28ec92f841fe32652e4dac36848d69e389c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631706"
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Развертывание Функции Azure в виде модуля IoT Edge — предварительный просмотр
Вы можете использовать Функции Azure для развертывания кода, который реализует нужную бизнес-логику, непосредственно на устройствах IoT Edge. В этом руководстве описывается создание и развертывание Функции Azure, которая фильтрует данные датчиков на виртуальном устройстве IoT Edge, созданном по инструкциям в руководствах по развертыванию Azure IoT Edge на виртуальном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Из этого руководства вы узнаете, как выполнять такие задачи:     

> [!div class="checklist"]
> * Использование Visual Studio Code для создания функции Azure
> * Использование VS Code и Docker для создания образа Docker и его публикация в реестре. 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Функция Azure, которую вы создадите в этом руководстве, фильтрует сформированные устройством данные о температуре и отправляет сообщения в вышестоящий Центр Интернета вещей Azure, если температура превышает заданный порог. 

## <a name="prerequisites"></a>предварительным требованиям

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или предыдущим руководством.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). Для этого руководства достаточно выпуска Community Edition (CE) для вашей платформы. 
* [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.
2. Укажите имя реестра, выберите подписку, выберите группу ресурсов и задайте номер SKU **Базовый**. 
3. Нажмите кнопку **Создать**.
4. После создания реестра контейнеров перейдите к нему и выберите **Ключи доступа**. 
5. **Включите** параметр **Пользователь-администратор**.
6. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения вам понадобятся позже при работе с этим руководством. 

## <a name="create-a-function-project"></a>Создание проекта функции
Следующий процесс позволяет создать модуль IoT Edge с использованием Visual Studio Code и расширения Azure IoT Edge.
1. Откройте Visual Studio Code.
2. Чтобы открыть интегрированный терминал VS Code, выберите **Вид** > **Интегрированный терминал**.
3. Чтобы установить (или обновить) шаблон **AzureIoTEdgeFunction** в dotnet, во встроенном терминале выполните следующую команду:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Создайте проект для нового модуля. Приведенная ниже команда создает папку проекта **FilterFunction** в указанном репозитории контейнеров. Если вы используете реестр контейнеров, второй параметр должен быть в формате `<your container registry name>.azurecr.io`. В текущей рабочей папке введите следующую команду:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Выберите **Файл** > **Открыть папку**, перейдите в папку **FilterFunction** и откройте проект в VS Code.
4. В обозревателе VS Code разверните папку **EdgeHubTrigger-Csharp**, а затем откройте файл **run.csx**.
5. Замените содержимое файла на код, приведенный ниже.

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

11. Сохраните файл.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала VS Code. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Чтобы найти имя пользователя, пароль и сервер входа для использования в этой команде, перейдите на [портал Azure] (https://portal.azure.com). В разделе **Все ресурсы** щелкните плитку вашего реестра контейнеров Azure, чтобы открыть его свойства, а затем щелкните **Ключи доступа**. Скопируйте значения полей **Имя пользователя**, **Пароль** и **Сервер входа**. 

2. Откройте **module.json**. Кроме того, вы можете обновить `"version"`, например, для **"1.0"**. А также отображающееся имя репозитория, которое вы ввели в параметре `-r` для `dotnet new aziotedgefunction`.

3. Сохраните файл **module.json**.

4. В обозревателе VS Code щелкните правой кнопкой мыши файл **module.json** и выберите действие **Build and Push IoT Edge module Docker image** (Создать и отправить образ Docker для модуля IoT Edge). Во всплывающем раскрывающемся списке в верхней части окна VS Code выберите платформу контейнера: **amd64** для контейнера Linux или **windows-amd64** для контейнера Windows. VS Code поместит в контейнер код функции и передаст его в указанный вами реестр контейнеров.

5. Полный адрес образа контейнеров с тегом можно получить через интегрированный терминал VS Code. Дополнительные сведения об определении сборки и отправки см. в файле `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Добавление учетных данных реестра на устройство Edge
Добавьте учетные данные для своего реестра в среду выполнения Edge на компьютере, где запущено устройство Edge. Таким образом среда выполнения сможет получить доступ к контейнеру. 

- В ОС Windows выполните следующую команду:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- В ОС Linux выполните следующую команду:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Запуск решения

1. Найдите нужный Центр Интернета вещей на **портале Azure**.
2. Щелкните **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
1. Щелкните **Set Modules** (Настроить модули). 
2. Если вы уже развернули модуль **tempSensor** на этом устройстве, он может быть заполнен автоматически. Если же модуль не установлен, выполните следующие действия, чтобы добавить его.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Оставьте без изменений другие параметры и нажмите кнопку **Сохранить**.
1. Добавьте модуль **filterFunction**.
    1. Снова выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `filterFunction`.
    3. В поле **URI изображения** введите адрес образа, например `<your container registry address>/filterfunction:0.0.1-amd64`. Полный адрес образа можно найти в предыдущем разделе.
    74. Выберите команду **Сохранить**.
2. Нажмите кнопку **Далее**.
3. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Первый маршрут передает сообщения с датчика температуры модулю фильтра через конечную точку input1. Второй маршрут передает сообщения из модуля фильтра в Центр Интернета вещей. В этом маршруте `$upstream` является специальным пунктом назначения, который говорит концентратору Edge отправлять сообщения в Центр Интернета вещей. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Нажмите кнопку **Далее**.
5. На шаге **Review Template** (Проверка шаблона) щелкните **Отправить**. 
6. Вернитесь на страницу сведений об устройстве IoT Edge и щелкните **Обновить**. Вы должны увидеть новый модуль **filtermodule**, работающий вместе с модулем **tempSensor** и **средой выполнения IoT Edge**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отслеживать сообщения между устройством и облаком, отправленные с вашего устройства IoT Edge в Центр Интернета вещей:
1. Настройте расширение Azure IoT Toolkit со строкой подключения для вашего Центра Интернета вещей: 
    1. На портале Azure перейдите в Центр Интернета вещей и выберите **Политики общего доступа**. 
    2. Выберите **iothubowner** и скопируйте значение из поля **Строка подключения — первичный ключ**.
    3. В проводнике VS Code щелкните **IOT HUB DEVICES** (Устройства Центра Интернета вещей), а затем выберите **...**. 
    4. Выберите **Set IoT Hub Connection String** (Установка строки подключения Центра Интернета вещей) и введите во сплывающем окне строку подключения Центра Интернета вещей. 

2. Чтобы отслеживать данные, поступающие в Центр Интернета вещей, выберите **Вид** > **Палитра команд** и щелкните **IoT: Start monitoring D2C message** (Интернет вещей: начать мониторинг сообщений D2C). 
3. Чтобы перестать отслеживать данные, используйте команду **IoT: Stop monitoring D2C message** (Центр Интернета вещей: остановить мониторинг сообщений D2C) в палитре команд. 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Чтобы продолжить изучение функций Azure IoT Edge, перейдите к статье об использовании устройства IoT Edge в качестве шлюза. 

> [!div class="nextstepaction"]
> [Create an IoT Edge gateway device](how-to-create-transparent-gateway.md) (Создание устройства шлюза IoT Edge для обработки данных с других устройств Интернета вещей (предварительная версия))

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
