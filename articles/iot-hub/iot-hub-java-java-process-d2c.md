---
title: "Обработка сообщений Центра Интернета вещей Azure, отправляемых с устройства в облако (Java) | Документация Майкрософт"
description: "Обработка сообщений Центра Интернета вещей, отправляемых с устройства в облако, с использованием правил маршрутизации и пользовательских конечных точек для перенаправления сообщений в другие внутренние службы."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 616bca96eaff12fa1929605f3480098bd8b867c2


---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Обработка сообщений Центра Интернета вещей, отправляемых с устройства в облако (Java)
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Введение
Центр IoT Azure —это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью решения. В других руководствах ([Приступая к работе с Центром Интернета вещей Azure (Java)] и [Учебник: как обрабатывать сообщения, отправляемые с устройства центра IoT в облако, с помощью Java][lnk-c2d]) описаны базовые функции Центра Интернета вещей по обмену сообщениями между облаком и устройством в обоих направлениях.

В этой статье используется код, представленный в руководстве [Приступая к работе с Центром Интернета вещей Azure (Java)]. Из нее вы узнаете, как можно легко обрабатывать сообщения, отправляемые с устройства в облако, используя маршрутизацию сообщений. В руководстве продемонстрировано, как обрабатывать сообщения, которые требуют немедленных действий от серверной части решения. Например, устройство может отправить аварийный сигнал, который активирует вставку билета в системе CRM. При этом обычные сообщения от точек данных просто поступают в модуль аналитики. Например, телеметрическое сообщение о температуре с устройства, которое должно быть сохранено для последующего анализа, является сообщением точки данных.

В конце этого учебника у вас будет три консольных приложения Java:

* **simulated-device**, измененная версия приложения, созданного в руководстве [Приступая к работе с Центром Интернета вещей Azure (Java)]. Приложение отправляет сообщения точек данных с устройства в облако один раз в секунду и интерактивные сообщения с устройства в облако каждые 10 секунд. Это приложение использует протокол AMQP для обмена данными с Центром Интернета вещей.
* **read-d2c-messages** отображает данные телеметрии, отправляемые приложением виртуального устройства;
* **read-critical-queue** извлекает важные сообщения из очереди служебной шины, присоединенной к Центру Интернета вещей.

> [!NOTE]
> Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и JavaScript). Указания по замене виртуального устройства в этом учебнике физическим устройством, а также по подключению устройств к Центру Интернета вещей Azure в целом см. в [Центре разработчиков для Azure IoT].
> 
> 

Для работы с этим учебником требуется:

* Полная рабочая версия приведена в учебнике [Приступая к работе с Центром Интернета вещей Azure (Java)] .
* Java SE 8. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Java для работы с этим руководством в ОС Windows или Linux.
* Maven 3.  <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Maven для работы с этим руководством в ОС Windows или Linux.
* Активная учетная запись Azure. <br/>Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/free/) всего за несколько минут.

Кроме того, у вас должны быть базовые знания о [службе хранилища Azure] и [служебной шине Azure].

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>Отправка интерактивных сообщений из приложения для имитации устройства
В этом разделе вы измените приложение виртуального устройства, созданное в руководстве [Приступая к работе с Центром Интернета вещей Azure (Java)], чтобы оно умело отправлять сообщения, требующие немедленной обработки.

1. Откройте в текстовом редакторе файл simulated-device\src\main\java\com\mycompany\app\App.java. Этот файл содержит код приложения **simulated-device** , созданного при изучении учебника [Приступая к работе с Центром Интернета вещей Azure (Java)] .
2. Замените класс **MessageSender** следующим кодом:
   
    ```
    private static class MessageSender implements Runnable {
        public volatile boolean stopThread = false;

        public void run()  {
            try {
                double avgWindSpeed = 10; // m/s
                Random rand = new Random();

                while (!stopThread) {
                    double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
                    TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                    telemetryDataPoint.deviceId = deviceId;
                    telemetryDataPoint.windSpeed = currentWindSpeed;

                    String msgStr = telemetryDataPoint.serialize();
                    if (new Random() > 0.7) {
                        Message msg = new Message("This is a critical message.");
                        msg.setProperty("level", "critical");
                    } else {
                        Message msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Этот метод случайным образом добавляет свойство `"level": "critical"` в сообщения, отправляемые имитацией устройства. Так мы имитируем сообщение, которое требует немедленных действий со стороны серверной части приложения. Приложение передает эту информацию в свойствах сообщения, а не в тексте сообщения, чтобы Центр Интернета вещей смог правильно передать сообщение получателю.
   
   > [!NOTE]
   > Свойства сообщений можно использовать для маршрутизации сообщений в разных ситуациях, включая обработку холодного пути. В нашем примере используется горячий путь.
   > 
   > 

2. Сохраните и закройте файл simulated-device\src\main\java\com\mycompany\app\App.java.
   
   > [!NOTE]
   > Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политику повтора (например, экспоненциальную задержку), как предлагается в статье MSDN [Transient Fault Handling](Обработка временного сбоя).
   > 
   > 

3. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду:
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Добавление очереди в Центре Интернета вещей и направление в нее сообщений
В этом разделе вы создадите очередь служебной шины, подключите ее к Центру Интернета вещей и укажете в нем настройки для отправки в эту очередь сообщений с определенным свойством. Дополнительные сведения о способах обработки сообщений в очередях служебной шины см. в руководстве [Начало работы с очередями служебной шины][Service Bus queue].

1. Создайте очередь служебной шины, как описано в статье [Начало работы с очередями служебной шины][Service Bus queue]. Запишите пространство имен и имя очереди.

2. На портале Azure откройте Центр Интернета вещей и щелкните **Конечные точки**.
    
    ![Конечные точки в Центре Интернета вещей][30]

3. В колонке **Конечные точки** щелкните вверху **Добавить**, чтобы добавить свою очередь в Центр Интернета вещей. Присвойте конечной точке имя **CriticalQueue** и выберите в раскрывающемся списке **Очередь служебной шины** пространство имен служебной шины, в котором находится ваша очередь, а затем имя этой очереди. По завершении нажмите кнопку **Сохранить** в нижней части окна.
    
    ![Добавление конечной точки][31]
    
4. Теперь щелкните **Маршруты** в Центре Интернета вещей. Щелкните **Добавить** в верхней части колонки, чтобы создать правило маршрутизации, которое направляет сообщения в только что добавленную очередь. Выберите источник данных **DeviceTelemetry**. Введите условие `level="critical"`, а затем выберите только что добавленную очередь в качестве пользовательской конечной точки этого правила маршрутизации. По завершении нажмите кнопку **Сохранить** в нижней части окна.
    
    ![Добавление правила][32]
    
    Убедитесь, что **включено** использование резервного маршрута. Это значение является конфигурацией по умолчанию для Центра Интернета вещей.
    
    ![Резервный маршрут][33]


## <a name="optional-read-from-the-queue-endpoint"></a>Чтение сообщений из конечной точки очереди
Сообщения можно также считывать из конечная точка очереди, следуя указаниям в статье [Как использовать очереди служебной шины][lnk-sb-queues-java]. Присвойте приложению имя **read-critical-queue**.

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску этих трех приложений.

1. Чтобы запустить приложение **read-d2c-messages**, в командной строке или оболочке перейдите к папке read-d2c и выполните следующую команду:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Запуск read-d2c-messages][readd2c]
2. Чтобы запустить приложение **read-critical-queue**, в командной строке или оболочке перейдите к папке read-critical-queue и выполните следующую команду:
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Запуск read-critical-messages][readqueue]

3. Чтобы запустить приложение для **имитации устройства**, в командной строке или оболочке перейдите к папке имитации устройства и выполните следующую команду.
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Запуск приложения simulated-device][simulateddevice]


## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы узнали, как надежно перенаправлять сообщения, отправляемые с устройства в облако, с использованием функции маршрутизации сообщений Центра Интернета вещей.


В статье [Учебник: как отправлять сообщения из облака на устройства с помощью центра IoT и .Net][lnk-c2d] описано, как отправлять сообщения на устройства из серверной части решения.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по Azure IoT Suite][lnk-suite].

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей].

Дополнительные сведения о маршрутизации сообщений в Центре Интернета вещей см. в статье [Отправка и получение сообщений в Центре Интернета вещей][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md
[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[службе хранилища Azure]: https://azure.microsoft.com/documentation/services/storage/
[служебной шине Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[руководстве разработчика для Центра Интернета вещей]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Приступая к работе с Центром Интернета вещей Azure (Java)]: iot-hub-java-java-getstarted.md
[Центре разработчиков для Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub



<!--HONumber=Jan17_HO5-->


