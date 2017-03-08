---
title: "Обработка сообщений Центра Интернета вещей Azure, отправляемых с устройства в облако, с использованием маршрутов (.NET) | Документация Майкрософт"
description: "Обработка сообщений Центра Интернета вещей, отправляемых с устройства в облако, с использованием правил маршрутизации и пользовательских конечных точек для перенаправления сообщений в другие внутренние службы."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 88b75c2b222ee153c935898dbece0c366c7f198d


---
# <a name="process-iot-hub-device-to-cloud-messages-using-routes-net"></a>Обработка сообщений Центра Интернета вещей, отправляемых с устройства в облако, с использованием маршрутов (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Введение
Центр IoT Azure —это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью решения. В других руководствах ([Приступая к работе с Центром Интернета вещей Azure (Java)] и [Учебник: как обрабатывать сообщения, отправляемые с устройства центра IoT в облако, с помощью Java][lnk-c2d]) описаны базовые функции Центра Интернета вещей по обмену сообщениями между облаком и устройством в обоих направлениях.

Эта статья основана на руководстве [Приступая к работе с Центром Интернета вещей Azure (Java)]. Из статьи вы узнаете, как можно использовать правила маршрутизации для перенаправления сообщений, отправляемых с устройства в облако. Это простой способ, реализуемый через настройки системы. Руководство демонстрирует, как выделять для дополнительной обработки сообщения, которые требуют немедленных действий от серверной части решения. Например, устройство может отправить аварийный сигнал, который активирует вставку билета в системе CRM. При этом обычные сообщения от точек данных просто поступают в модуль аналитики. Например, телеметрическое сообщение о температуре с устройства, которое должно быть сохранено для последующего анализа, является сообщением точки данных.

В конце этого руководства запускаются три консольных приложения для .NET:

* **SimulatedDevice**, измененная версия приложения, созданного в руководстве [Приступая к работе с Центром Интернета вещей Azure (Java)]. Приложение отправляет сообщения точек данных с устройства в облако каждую секунду и интерактивные сообщения с устройства в облако каждые 10 секунд. Это приложение использует протокол AMQP для обмена данными с Центром Интернета вещей.
* **ReadDeviceToCloudMessages** — отображает некритические данные телеметрии, отправленные приложением имитации устройства.
* **ReadCriticalQueue** — извлекает важные сообщения, отправленные приложением виртуального устройства, из очереди служебной шины, присоединенной к Центру Интернета вещей.

> [!NOTE]
> Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и JavaScript). Чтобы узнать, как заменить имитацию устройства в этом руководстве физическим устройством, а также подключить устройства к Центру Интернета вещей Azure, см. [Центр разработчиков для Azure IoT].
> 
> 

Для работы с этим учебником требуется:

* Microsoft Visual Studio 2015.
* Активная учетная запись Azure. <br/>Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/free/) всего за несколько минут.

Кроме того, у вас должны быть базовые знания о [службе хранилища Azure] и [служебной шине Azure].

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>Отправка интерактивных сообщений из приложения для имитации устройства
В этом разделе вы измените приложение виртуального устройства, созданное в руководстве [Приступая к работе с Центром Интернета вещей Azure (Java)], чтобы оно умело отправлять сообщения, требующие немедленной обработки.

В Visual Studio в проекте **SimulatedDevice** замените метод `SendDeviceToCloudMessagesAsync` следующим кодом:

```
private static async void SendDeviceToCloudMessagesAsync()
    {
        double avgWindSpeed = 10; // m/s
        Random rand = new Random();

        while (true)
        {
            double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

            var telemetryDataPoint = new
            {
                deviceId = "myFirstDevice",
                windSpeed = currentWindSpeed
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            string levelValue;

            if (rand.NextDouble() > 0.7)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else
            {
                levelValue = "normal";
            }
            
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("level", levelValue);
            
            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
```

Этот метод случайным образом добавляет свойство `"level": "critical"` в сообщения, отправляемые устройством. Так мы имитируем сообщение, которое требует немедленных действий со стороны серверной части решения. Приложение устройства передает эту информацию в свойствах сообщения, а не в тексте сообщения, чтобы Центр Интернета вещей смог правильно передать сообщение получателю.

> [!NOTE]
> Свойства сообщений можно использовать для маршрутизации сообщений в разных ситуациях, включая обработку холодного пути. В нашем примере используется горячий путь.

> [!NOTE]
> Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политику повтора (например, экспоненциальную задержку), как предлагается в статье MSDN [Transient Fault Handling](Обработка временного сбоя).

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Добавление очереди в Центре Интернета вещей и маршрутизация сообщений
В этом разделе выполняются следующие действия:

* Создается очередь служебной шины.
* Эта очередь подключается к Центру Интернет вещей.
* Настраивается Центр Интернета вещей для отправки в эту очередь сообщений с определенным свойством.

Дополнительные сведения о способах обработки сообщений в очередях служебной шины см. в руководстве [Начало работы с очередями служебной шины][Service Bus queue].

1. Создайте очередь служебной шины, как описано в статье [Начало работы с очередями служебной шины][Service Bus queue]. Эта очередь должна относиться к той же подписке и тому же региону, что и Центр Интернета вещей. Запишите пространство имен и имя очереди.

2. На портале Azure откройте Центр Интернета вещей и щелкните **Конечные точки**.
    
    ![Конечные точки в Центре Интернета вещей][30]

3. В колонке **Конечные точки** щелкните вверху **Добавить**, чтобы добавить свою очередь в Центр Интернета вещей. Присвойте конечной точке имя **CriticalQueue** и выберите в раскрывающемся списке **Очередь служебной шины** пространство имен служебной шины, в котором находится ваша очередь, а затем имя этой очереди. По завершении нажмите кнопку **Сохранить** в нижней части окна.
    
    ![Добавление конечной точки][31]
    
4. Теперь щелкните **Маршруты** в Центре Интернета вещей. Щелкните **Добавить** в верхней части колонки, чтобы создать правило маршрутизации, которое направляет сообщения в только что добавленную очередь. Выберите источник данных **DeviceTelemetry**. Введите условие `level="critical"`, а затем выберите только что добавленную очередь в качестве пользовательской конечной точки этого правила маршрутизации. По завершении нажмите кнопку **Сохранить** в нижней части окна.
    
    ![Добавление правила][32]
    
    Убедитесь, что **включено** использование резервного маршрута. Это значение является конфигурацией по умолчанию для Центра Интернета вещей.
    
    ![Резервный маршрут][33]

## <a name="read-from-the-queue-endpoint"></a>Чтение сообщений из конечной точки очереди
В этом разделе вы организуете чтение сообщений из конечной точки очереди.

1. В текущем решении Visual Studio создайте проект Windows на языке Visual C#, используя шаблон проекта **Консольное приложение** . Присвойте проекту имя **ReadCriticalQueue**.

2. В обозревателе решений щелкните правой кнопкой мыши проект **ReadCriticalQueue** и выберите **Управление пакетами NuGet**. Откроется окно **Диспетчер пакетов NuGet**.

3. Найдите **WindowsAzure.ServiceBus**, щелкните **Установить** и примите условия использования. После этого будут выполнены скачивание, установка и добавление ссылки на служебную шину Azure со всеми ее зависимостями.

4. Добавьте следующие инструкции **using** в начало файла **Program.cs**:
   
    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Наконец, добавьте следующие строки в метод **Main** . Подставьте для очереди строку подключения с разрешениями на **прослушивание**:
   
    ```
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Настроить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем выберите **Запуск** в качестве действия для проектов **ReadDeviceToCloudMessages**, **SimulatedDevice** и **ReadCriticalQueue**.
2. Нажмите клавишу **F5** для запуска трех консольных приложений. Теперь приложение **ReadDeviceToCloudMessages** получает только некритические сообщения, отправленные из приложения **SimulatedDevice**, а приложение **ReadCriticalQueue** получает только важные сообщения.
   
   ![Три консольных приложения][50]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы узнали, как надежно перенаправлять сообщения, отправляемые с устройства в облако, с использованием функции маршрутизации сообщений Центра Интернета вещей.

В статье [Учебник: как отправлять сообщения из облака на устройства с помощью центра IoT и .Net][lnk-c2d] описано, как отправлять сообщения на устройства из серверной части решения.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по Azure IoT Suite][lnk-suite].

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей].

Дополнительные сведения о маршрутизации сообщений в Центре Интернета вещей см. в статье [Отправка и получение сообщений в Центре Интернета вещей][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[службе хранилища Azure]: https://azure.microsoft.com/documentation/services/storage/
[служебной шине Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[руководстве разработчика для Центра Интернета вещей]: iot-hub-devguide.md
[Приступая к работе с Центром Интернета вещей Azure (Java)]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Центр разработчиков для Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Event Hubs Programming Guide]: ../event-hubs/event-hubs-programming-guide.md
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Build multi-tier applications with Service Bus]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/



<!--HONumber=Jan17_HO5-->


