---
title: "Использование кода Visual Studio для разработки и развертывания Azure функции Azure IoT краю | Документы Microsoft"
description: "Разрабатывать и развертывать функции Azure C# с Azure IoT край VS Code без переключения контекста"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9637986d10a0e89568b2f79ede3d7b7468bb99a7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Использование кода Visual Studio для разработки и развертывания функций Azure по краю IoT Azure

В этой статье приведены подробные инструкции по использованию [кода Visual Studio](https://code.visualstudio.com/) как основное средство разработки для разработки и развертывания функций Azure IoT границы. 

## <a name="prerequisites"></a>Технические условия
Предполагается, что вы используете компьютер или виртуальную машину с компьютера разработчика Windows или Linux. IoT пограничного устройства может быть другое физическое устройство или устройство пограничного IoT, вы можете имитировать на компьютере разработки.

Убедитесь, что у вас есть завершенного следующие учебники перед началом в этом руководстве.
- Развертывание на имитированное устройство в Azure IoT Edge [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) или [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Развертывание решения "Функции Azure"](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Ниже приведен контрольный список, который показывает элементы должны иметь после завершения предыдущей учебники.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [Пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Сценарий IoT края элемента управления](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Шаблон AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Центр IoT active с по крайней мере IoT периферийным устройством.

Также рекомендуется установить [Docker поддержка VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для лучшего управления образов и контейнеры.

> [!NOTE]
> В настоящее время функции Azure на границе IoT поддерживает только C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Развертывание Azure IoT функций в коде VS
В учебнике [развертывание функций Azure](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), обновлением построения и публикация изображений для вашего модуля функции в коде VS и затем посетите портал Azure для развертывания функций Azure. В этом разделе Знакомство с использованием VS Code для развертывания и мониторинга функций Azure.

### <a name="start-a-local-docker-registry"></a>Запуск реестра локального docker
Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом разделе используется [локального реестра Docker](https://docs.docker.com/registry/deploying/), который проще для тестирования во время разработки раннее.
В VS Code **интеграции терминалов**(Ctrl + "), выполнить следующие команды для запуска в реестре локального компьютера.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Предыдущий пример показывает конфигурации реестра, которые подходят только для тестирования. Готовый реестра должен быть защищен протоколом TLS и в идеале следует использовать механизм управления доступом. Рекомендуется использовать [реестра контейнера Azure](https://docs.microsoft.com/azure/container-registry/) или [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) размещать модули IoT край рабочей среде.

### <a name="create-a-function-project"></a>Создание проекта функции
На следующих этапах показано, как создать модуль IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge. После выполнения в этом разделе в предыдущем учебнике можно безопасно пропустить в этом разделе.

1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.
2. Установите (или обновлять) **AzureIoTEdgeFunction** шаблона в dotnet, выполните следующую команду в окне терминала интеграции:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Создайте проект для нового модуля. Следующая команда создает папку проекта, **FilterFunction**, в текущей рабочей папке:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Выберите **файл > Открыть папку**, перейдите к **FilterFunction** папку и откройте проект в VS Code.
5. Перейдите к **FilterFunction** папку и нажмите кнопку **Выбор папки** для открытия проекта в VS Code.
6. В обозревателе VS Code разверните **EdgeHubTrigger Csharp** папку, затем откройте **run.csx** файла.
7. Замените содержимое файла следующим кодом:

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
3. Перейдите к **FilterFunction** папку проекта и нажмите кнопку **Выбор папки как EXE_DIR**. 
4. В верхней части окна VS Code введите имя образа в текстовом поле всплывающего окна. Например, `<your container registry address>/filterfunction:latest`. Если развертывается в локальном реестре, он должен быть `localhost:5000/filterfunction:latest`.
5. Отправьте образ в репозиторий Docker. Используйте **Edge: образа Docker модуля Push IoT Edge** команд и введите URL-адрес изображения в поле всплывающие подсказки в верхней части окна VS Code. Используйте же URL-адрес изображения использовался выше шаг.

### <a name="deploy-your-function-to-iot-edge"></a>Развертывание функции IoT краю

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

2. Замените **маршруты** с разделом содержимого:
   ```json
   {
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   }
   ```
   > [!NOTE]
   > Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры функция filter через конечную точку «input1», который является конечной точкой, настроенной с обработчиком FilterMessages. Второй маршрут передает сообщения из функцию фильтров к центру IOT. В этом маршруте вышестоящего специальные назначения, который сообщает концентратора Edge для отправки сообщений в центр IoT.

3. Сохраните этот файл.
4. Выберите в палитру команд **Edge: Создание развертывания для периферийным устройством**. Выберите ИД устройства IoT Edge для создания развертывания. Или щелкните правой кнопкой мыши идентификатор устройства в списке устройств и выберите **Создание развертывания для периферийным устройством**.
5. Выберите `deployment.json` вам о новостях. В окне вывода появится соответствующие выходные данные для развертывания.
6. Запуск вашей среды выполнения Edge в палитру команд. **Граница: Начальному краю**
7. Вы увидите вашей выполнения IoT Edge для запуска в обозревателе Docker с помощью имитации функции датчика и фильтр.
8. Щелкните правой кнопкой мыши на край ИД устройства, и можно отслеживать сообщения D2C в VS Code.


## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике создать функцию Azure IoT границы и развернут в IoT периферийным устройством в VS Code. Можно перейти к любой из следующих учебников, чтобы узнать о других сценариях при разработке Azure IoT край VS Code.

> [!div class="nextstepaction"]
> [Отладка функций Azure в VS Code](how-to-vscode-debug-azure-function.md)
