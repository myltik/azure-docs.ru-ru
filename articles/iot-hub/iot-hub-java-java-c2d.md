---
title: Отправка сообщений из облака на устройства с помощью центра IoT | Microsoft Docs
description: Следуйте инструкциям этого руководства, чтобы научиться отправлять сообщения из облака на устройства в центре IoT Azure с помощью Java.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: dobett

---
# Руководство. Как отправлять сообщения из облака на устройства с помощью центра IoT и Java
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Введение
Центр Azure IoT — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В учебнике [Приступая к работе с центром IoT] показано, как создать центр IoT, подготовить в нем удостоверение устройства и написать код виртуального устройства, которое отправляет сообщения с устройства в облако.

Это руководство является логическим продолжением статьи [Приступая к работе с центром Azure IoT с использованием Java]. В нем показано следующее:

* Отправка сообщения, передаваемого из облака на устройство, из облачной серверной части приложения на одно устройство через центр IoT.
* Получение на устройстве сообщений, передаваемых из облака на устройство.
* Запрос из облачной серверной части приложения подтверждения доставки (*отзыва*) для сообщений, отправленных на устройство из центра IoT.

Дополнительные сведения о сообщениях, отправляемых с облака на устройство, можно найти в [Руководстве разработчика по центру IoT][IoT Hub Developer Guide - C2D].

После выполнения инструкций этого руководства у вас будут работать два консольных приложения Java:

* **simulated-device**, измененная версия приложения, созданного на основе руководства [Приступая к работе с центром IoT]. Это приложение подключается к центру IoT и получает сообщения из облака на устройство.
* **send-c2d-messages**, которое отправляет сообщение из облака на имитацию устройства с помощью центра IoT и затем получает подтверждение его доставки.

> [!NOTE]
> Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые инструкции по связыванию устройства с кодом из этого учебника, а также по подключению к центру Azure IoT см. в [центре разработчика для Azure IoT].
> 
> 

Для работы с этим учебником требуется:

* Java SE 8. <br/> В статье [Подготовка среды разработки][lnk-dev-setup] описывается, как установить Java для целей этого руководства в ОС Windows или Linux.
* Maven 3. <br/> В статье [Подготовка среды разработки][lnk-dev-setup] описывается, как установить Maven для целей этого руководства в ОС Windows или Linux.
* Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. на странице [Бесплатная пробная версия Azure][lnk-free-trial].)

## Получение сообщений на виртуальном устройстве
В этом разделе вы измените приложение имитации устройства, созданное в разделе [Приступая к работе с центром IoT], для получения сообщений из облака на устройство от центра IoT.

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
3. Измените метод **main**, чтобы создать экземпляр **MessageCallback** и вызвать метод **setMessageCallback** перед открытием клиента.
   
    ```
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```
   
   > [!NOTE]
   > Если в качестве транспорта вместо AMQP используется HTTP/1, то экземпляр **DeviceClient** редко проверяет наличие сообщений от центра IoT (реже чем каждые 25 минут). Дополнительную информацию о различиях между поддержкой AMQP и HTTP/1, а также регулировании центра IoT см. в [руководстве разработчика для центра IoT][IoT Hub Developer Guide - C2D].
   > 
   > 

## Отправка сообщения из облака на устройство
В этом разделе вам предстоит создать консольное приложение Java, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Необходим идентификатор устройства, добавленного при изучении учебника [Начало работы с центром IoT]. Кроме того, нужна строка подключения для центра IoT, которую можно найти на [портале Azure].

1. Создайте проект Maven **send-c2d-messages**, выполнив следующую команду в командной строке. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к новой папке send-c2d-messages.
3. Откройте в текстовом редакторе файл pom.xml из папки send-c2d-messages и добавьте зависимость, приведенную ниже, в узел **dependencies**. Добавление зависимости позволяет использовать в приложении пакет **iothub-java-service-client** для обмена данными со службой центра IoT.
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```
4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.
6. Добавьте в файл следующие операторы **import**.
   
    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{yourhubconnectionstring}** и **{yourdeviceid}** значениями, записанными ранее.
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
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

## Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке simulated-device выполните приведенную ниже команду, чтобы начать отправку данных телеметрии в центр IoT и прослушивание сообщений из облака на устройство, отправляемых из центра.
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Запуск приложения виртуального устройства][img-simulated-device]
2. В командной строке в папке send-c2d-messages выполните следующую команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждения доставки.
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Выполнение команды для отправки сообщения из облака на устройство][img-send-command]

## Дальнейшие действия
В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется центр IoT, см. в [документации по Azure IoT Suite].

Дополнительные сведения о разработке решений с помощью центра IoT см. в [руководстве разработчика по центру IoT].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]: media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Начало работы с центром IoT]: iot-hub-java-java-getstarted.md
[Приступая к работе с центром Azure IoT с использованием Java]: iot-hub-java-java-getstarted.md
[Приступая к работе с центром IoT]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[руководстве разработчика по центру IoT]: iot-hub-devguide.md
[центре разработчика для Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[портале Azure]: https://portal.azure.com
[документации по Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0914_2016-->