---
title: "Отправка сообщений из облака на устройство с помощью Центра Интернета вещей Azure (Java) | Документация Майкрософт"
description: "Сведения об отправке сообщений из облака на устройство из Центра Интернета вещей Azure с помощью пакетов SDK для Azure IoT для Java. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: bd3b92e044a28237706fb004fdc43cca056047ad


---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Введение
Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. В руководстве [Приступая к работе с Центром Интернета вещей Azure (Node)] показано, как создать Центр Интернета вещей, подготовить в нем удостоверение устройства и написать код приложения для имитации устройства, которое отправляет сообщения из устройства в облако.

Это руководство является логическим продолжением статьи [Приступая к работе с Центром Интернета вещей Azure (Node)]. В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.
* Получение на устройстве сообщений, передаваемых из облака на устройство.
* Запрос из серверной части решения подтверждения доставки (*отзыва*) для сообщений, отправленных на устройство из Центра Интернета вещей.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей][IoT Hub developer guide - C2D].

В конце этого руководства запускаются два консольных приложения для Java:

* **simulated-device**, измененная версия приложения, созданного на основе руководства [Приступая к работе с Центром Интернета вещей Azure (Node)]. Это приложение подключается к Центру Интернета вещей и получает сообщения из облака на устройство.
* **send-c2d-messages**, которое отправляет сообщение из облака на приложение для имитации устройства с помощью Центра Интернета вещей и затем получает подтверждение его доставки.

> [!NOTE]
> Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure].
> 
> 

Для работы с этим учебником требуется:

* Java SE 8. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Java для работы с этим руководством в ОС Windows или Linux.
* Maven 3.  <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Maven для работы с этим руководством в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства
В этом разделе вы измените приложение для имитации устройства, созданное в разделе [Приступая к работе с Центром Интернета вещей Azure (Node)], для получения от Центра Интернета вещей сообщений из облака на устройство.

1. Откройте в текстовом редакторе файл simulated-device\src\main\java\com\mycompany\app\App.java.
2. Добавьте следующий класс **MessageCallback** как вложенный класс внутри класса **App**. Метод **execute** вызывается, когда устройство получает сообщение из центра IoT. В этом примере устройство всегда уведомляет Центр Интернета вещей о завершении отправки сообщения:
   
    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
   
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Измените метод **main**, чтобы создать экземпляр **MessageCallback** и вызвать метод **setMessageCallback** перед открытием клиента.
   
    ```
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```
   
   > [!NOTE]
   > Если в качестве транспорта вместо MQTT или AMQP используется HTTP, то экземпляр **DeviceClient** редко проверяет наличие сообщений от Центра Интернета вещей (реже чем каждые 25 минут). Дополнительные сведения о различиях между поддержкой MQTT, AMQP и HTTP, а также регулировании Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство
В этом разделе вам предстоит создать консольное приложение Java, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Необходим идентификатор устройства, добавленного при изучении руководства [Приступая к работе с Центром Интернета вещей Azure (Node)]. Кроме того, нужна строка подключения для вашего экземпляра Центра Интернета вещей, которую можно найти на [портале Azure].

1. Создайте проект Maven **send-c2d-messages**, выполнив следующую команду в командной строке. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к новой папке send-c2d-messages.
3. Откройте в текстовом редакторе файл pom.xml из папки send-c2d-messages и добавьте зависимость, приведенную ниже, в узел **dependencies** . Добавление зависимости позволяет использовать в приложении пакет **iothub-java-service-client** для обмена данными со службой центра IoT.
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.10</version>
    </dependency>
    ```
4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл send-c2d-messages\src\main\java\com\mycompany\app\App.java.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{yourhubconnectionstring}** и **{yourdeviceid}** значениями, записанными ранее.
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
8. Замените метод **main** следующим кодом, который подключается к центру IoT, отправляет сообщение на устройство, а затем ждет подтверждения о том, что устройство получило и обработало это сообщение.
   
    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```
   
   > [!NOTE]
   > Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя].
   > 
   > 

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке simulated-device выполните приведенную ниже команду, чтобы начать отправку данных телеметрии в Центр Интернета вещей и прослушивание сообщений из облака на устройство, отправляемых из центра:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Запуск приложения виртуального устройства][img-simulated-device]
2. В командной строке в папке send-c2d-messages выполните следующую команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждения доставки:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Выполнение команды для отправки сообщения из облака на устройство][img-send-command]

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике вы научились отправлять и получать сообщения с облака на устройство. 

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по Azure IoT Suite].

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Приступая к работе с Центром Интернета вещей Azure (Node)]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[руководстве разработчика для Центра Интернета вещей]: iot-hub-devguide.md
[центре разработчиков для Интернета вещей Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[портале Azure]: https://portal.azure.com
[документации по Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/



<!--HONumber=Dec16_HO1-->


