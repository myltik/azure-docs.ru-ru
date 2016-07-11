<properties
	pageTitle="Отправка сообщений из облака на устройства с помощью центра IoT | Microsoft Azure"
	description="Следуйте инструкциям этого руководства, чтобы научиться отправлять сообщения из облака на устройства в центре IoT Azure с помощью Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="dobett"/>

# Руководство. Как отправлять сообщения из облака на устройства с помощью центра IoT и Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Введение

Центр Azure IoT — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В учебнике [Приступая к работе с центром IoT] показано, как создать центр IoT, подготовить в нем удостоверение устройства и написать код виртуального устройства, которое отправляет сообщения с устройства в облако.

Это руководство является логическим продолжением статьи [Приступая к работе с центром Azure IoT с использованием Java]. В нем показано, как отправить сообщение из облака на одно устройство, а также запросить подтверждение доставки (*отзыв*) из центра IoT и получить его от серверной части облачного приложения.

Дополнительные сведения о сообщениях, отправляемых с облака на устройство, можно найти в [Руководстве разработчика по центру IoT][IoT Hub Developer Guide - C2D].

После выполнения инструкций этого руководства у вас будут работать два консольных приложения Java.

* **simulated-device**, измененная версия приложения, созданного на основе руководства [Приступая к работе с центром Azure IoT с использованием Java]. Это приложение подключается к центру IoT и получает сообщения из облака на устройство.
* **send-c2d-messages**, которое отправляет сообщение из облака в виртуальное устройство с помощью центра IoT и затем получает подтверждение его доставки.

> [AZURE.NOTE] Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые инструкции по связыванию устройства с кодом из этого руководства, а также по подключению к центру Azure IoT см. в [центре разработчика для Azure IoT].

Для работы с этим руководством требуется:

+ Java SE 8. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Java для целей этого руководства в ОС Windows или Linux.

+ Maven 3. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Maven для целей этого руководства в ОС Windows или Linux.

+ Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. на странице [Бесплатная пробная версия Azure][lnk-free-trial].)

## Получение сообщений на виртуальном устройстве

В этом разделе вы измените приложение виртуального устройства, созданное в разделе [Приступая к работе с центром IoT], для получения сообщений из облака в устройство от центра IoT.

1. Откройте в текстовом редакторе файл simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Добавьте следующий класс **MessageCallback** как вложенный класс внутри класса **App** . Метод **execute** вызывается, когда устройство получает сообщение из центра IoT. В этом примере устройство всегда уведомляет центр IoT о завершении отправки сообщения:

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

3. Измените метод **main**, чтобы создать экземпляр **MessageCallback** и вызвать метод **setMessageCallback** перед открытием клиента:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Если в качестве транспорта используется HTTP/1 вместо AMQP, экземпляр **DeviceClient** редко проверяет наличие сообщений от центра IoT (реже чем каждые 25 минут). Дополнительную информацию о различиях между поддержкой AMQP и HTTP/1, а также регулировании центра IoT см. в [руководстве разработчика для центра IoT][IoT Hub Developer Guide - C2D].

## Отправка сообщения, передаваемого из облака на устройство, из серверной части приложений

В этом разделе вам предстоит создать консольное приложение Java, которое отправляет сообщения, передаваемые из облака на устройство, в приложение виртуального устройства. Вам потребуется идентификатор устройства, добавленного при выполнении инструкций руководства [Приступая к работе с центром Azure IoT с использованием Java], и строка подключения к центру IoT.

1. Создайте новый проект Maven с именем **send-c2d-messages** с помощью следующей команды в командной строке. Обратите внимание: это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке send-c2d-messages.

3. Откройте в текстовом редакторе файл pom.xml из папки send-c2d-messages и добавьте зависимость, приведенную ниже, в узел **dependencies**. Это позволит вам использовать в приложении пакет **iothub-java-service-client** для обмена данными с центром IoT.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Добавьте в файл следующие инструкции **import**:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{yourhubconnectionstring}** и **{yourdeviceid}** значениями, записанными ранее:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
    
8. Замените метод **main** следующим кодом, который подключается к центру IoT, отправляет сообщение на устройство, а затем ждет подтверждения о том, что устройство получило и обработало это сообщение:

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

    > [AZURE.NOTE] Для упрощения в этом учебнике не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Transient Fault Handling] (Обработка временного сбоя).

## Дальнейшие действия

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство. Изучение функций и сценариев центра IoT можно продолжить с помощью следующих учебников:

- [Обработка сообщений, отправляемых с устройств в облако] — описывается, как надежно обрабатывать данные телеметрии и интерактивные сообщения, поступающие от устройств.
- [Передача файлов с устройств] — описывается модель использования сообщений, отправляемых из облака на устройства, для упрощения передачи файлов с устройств.

Дополнительная информация о центре IoT приведена в следующих статьях:

* [Обзор центра IoT]
* [Руководство разработчика по центру IoT]
* [Руководство по центру IoT]
* [Поддерживаемые платформы устройств и языки]
* [Центр разработчика IoT Azure]

<!-- Links -->

[Приступая к работе с центром Azure IoT с использованием Java]: iot-hub-java-java-getstarted.md
[Приступая к работе с центром IoT]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Обработка сообщений, отправляемых с устройств в облако]: iot-hub-csharp-csharp-process-d2c.md
[Передача файлов с устройств]: iot-hub-csharp-csharp-file-upload.md
[Обзор центра IoT]: iot-hub-what-is-iot-hub.md
[Руководство по центру IoT]: iot-hub-guidance.md
[Руководство разработчика по центру IoT]: iot-hub-devguide.md
[Поддерживаемые платформы устройств и языки]: iot-hub-supported-devices.md
[Центр разработчика IoT Azure]: http://www.azure.com/develop/iot
[центре разработчика для Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0629_2016-->