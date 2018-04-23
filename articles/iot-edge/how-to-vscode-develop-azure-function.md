---
title: Использование Visual Studio Code для разработки и развертывания решения "Функции Azure" в Azure IoT Edge | Документация Майкрософт
description: Разработка и развертывание функций Azure на C# с помощью Azure IoT Edge в VS Code без переключения контекста
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 47d420b4b283b390f67719233c4bea59495a589a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Использование Visual Studio Code для разработки и развертывания решения "Функции Azure" в Azure IoT Edge

В этой статье содержатся подробные инструкции по использованию [Visual Studio Code](https://code.visualstudio.com/) в качестве основного инструмента разработки и развертывания решения "Функции Azure" в IoT Edge. 

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows или Linux. Устройством Azure IoT Edge может быть другое физическое устройство или имитация устройства, запущенная на компьютере разработки.

Перед началом работы с этим руководством убедитесь, что вы ознакомились со следующим руководством.
- Развертывание Azure IoT Edge на имитированном устройстве в ОС [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) или [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Развертывание решения "Функции Azure"](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Ниже приведен контрольный список элементов, которые должны быть у вас после выполнения инструкций в предыдущих руководствах.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/).
- [Сценарий управления IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl).
- Шаблон AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`).
- Активный Центр Интернета вещей с устройством IoT Edge (как минимум).

Кроме того, рекомендуем установить модуль [поддержки Docker для VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для оптимального управления образами и контейнерами модуля.

> [!NOTE]
> В настоящее время решение "Функции Azure" в IoT Edge поддерживает только язык C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Развертывание решения "Функции Azure" в VS Code
В руководстве по [развертыванию решения "Функции Azure"](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function) вы обновляете, создаете и публикуете образы модулей функций в VS Code, а затем выполняете развертывание решения "Функции Azure" на портале Azure. В этом разделе описывается, как использовать VS Code для развертывания и мониторинга решения "Функции Azure".

### <a name="start-a-local-docker-registry"></a>Запуск локального реестра Docker
Для выполнения инструкций в этой статье можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом разделе используется [локальный реестр Docker](https://docs.docker.com/registry/deploying/), который легче использовать для тестирования на этапе ранней разработки.
Во **встроенном терминале** (CTRL+`) VS Code выполните следующие команды для запуска локального реестра.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> В приведенном выше примере показаны конфигурации реестра, которые подходят только для тестирования. Готовый к выпуску реестр должен быть защищен протоколом TLS и, в идеале, использовать механизм контроля доступа. Для развертывания готовых для рабочей среды модулей IoT Edge рекомендуем использовать [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или [центр Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

### <a name="create-a-function-project"></a>Создание проекта функции
На следующих этапах показано, как создать модуль IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge. Если вы выполнили задачи этого раздела в предыдущем руководстве, здесь его можно пропустить.

1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.
2. Чтобы установить (или обновить) шаблон **AzureIoTEdgeFunction** в dotnet, во встроенном терминале выполните следующую команду:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Создайте проект для нового модуля. Приведенная ниже команда создает папку проекта **FilterFunction** в текущей рабочей папке.

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Выберите **Файл > Открыть папку**, перейдите в папку **FilterFunction** и откройте проект в VS Code.
5. Перейдите в папку **FilterFunction** и щелкните **Выбрать папку**, чтобы открыть проект в VS Code.
6. В обозревателе VS Code разверните папку **EdgeHubTrigger-Csharp**, а затем откройте файл **run.csx**.
7. Замените содержимое файла на код, приведенный ниже.

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
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
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

8. Сохраните файл.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Создание образа Docker и его публикация в реестре

1. В обозревателе VS Code разверните папку **Docker**. Затем разверните папку для платформы своего контейнера: **linux-x64** или **windows-nano**.
2. Щелкните правой кнопкой мыши файл **Dockerfile** и выберите **Build IoT Edge module Docker image** (Создать образ Docker модуля IoT Edge). 

    ![Создание образа Docker](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. Перейдите в папку проекта **FilterFunction** и щелкните **Select Folder as EXE_DIR** (Выбрать папку в качестве каталога выполнения). 
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filterfunction:latest`. Если развертывание выполняется в локальном реестре, необходимо использовать `localhost:5000/filterfunction:latest`.
5. Отправьте образ в репозиторий Docker. Запустите команду **Edge: Push IoT Edge module Docker image** (Edge: передать образ Docker для модуля IoT Edge) и укажите URL-адрес образа во всплывающем текстовом поле в верхней части окна VS Code. Используйте тот же URL-адрес образа, что и на предыдущем шаге.
    ![Передать образ Docker](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>Развертывание функции в IoT Edge

1. Откройте файл `deployment.json`, замените раздел **modules** следующим содержимым:
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
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Замените раздел **routes** следующим содержимым:
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этой статьей потребуется два маршрута. Первый маршрут передает сообщения от датчика температуры в функцию фильтра через конечную точку input1, которая настроена с помощью обработчика FilterMessages. Второй маршрут передает сообщения из функции фильтра в Центр Интернета вещей. В этом маршруте вышестоящий источник является специальным пунктом назначения, который говорит центру Edge отправлять сообщения в Центр Интернета вещей.

3. Сохраните этот файл.
4. В палитре команд выберите **Edge: Create deployment for Edge device** (Edge: создать развертывание для устройства Edge). Затем выберите идентификатор устройства IoT Edge для создания развертывания. Или щелкните идентификатор устройства правой кнопкой мыши в списке устройств и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge).

    ![Создать развертывание](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. Выберите обновленный файл `deployment.json`. В окне вывода можно увидеть соответствующие выходные данные для развертывания.
6. Запустите среду выполнения Edge в палитре команд. **Edge: запустить Edge**
7. Вы увидите в обозревателе Docker, что среда выполнения IoT Edge запускается с имитируемым датчиком и функцией фильтра.

    ![Решение выполняется](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Щелкните идентификатор устройства Edge правой кнопкой мыши, и вы сможете отслеживать сообщения D2C в VS Code.

    ![Мониторинг сообщений](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>Дополнительная информация

[Использование Visual Studio Code для отладки решения "Функции Azure" с помощью Azure IoT Edge](how-to-vscode-debug-azure-function.md)
