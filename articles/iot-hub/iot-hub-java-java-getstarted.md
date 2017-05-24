---
title: "Приступая к работе с Центром Интернета вещей Azure (Java) | Документация Майкрософт"
description: "Как отправлять сообщения, отправляемые с устройства в облако, с устройства в Центр Интернета вещей Azure, используя пакеты SDK для Центра Интернета вещей Azure для Java. Вы создадите приложение имитации устройства для отправки сообщений, а также приложение службы для регистрации устройства в реестре удостоверений и чтения сообщений, отправляемых с устройства в облако, из Центра Интернета вещей."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 51a4be879b053a3bdf0c9662f3bbd5e3ed7f6d4d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-java"></a>Подключение виртуального устройства к Центру Интернета вещей с помощью Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

В конце этого руководства у вас будет три консольных приложения Java:

* **create-device-identity** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к приложению виртуального устройства;
* **read-d2c-messages** — отображает данные телеметрии, отправляемые приложением виртуального устройства;
* **simulated-device** — подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и отправляет сообщения телеметрии с частотой один раз в секунду по протоколу MQTT.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о средствах, которые можно использовать при создании приложений для мобильных устройств и разработке серверной части решения.
> 
> 

Для работы с этим учебником требуется:

* Java SE 8. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Java для работы с этим руководством в ОС Windows или Linux.
* Maven 3.  <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить [Maven][lnk-maven] для работы с этим руководством в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Наконец, запишите значение поля **Первичный ключ**. После этого щелкните **Конечные точки** и встроенную конечную точку **События**. В колонке **Свойства** запишите адрес значений полей **Event Hub-compatible name** (Имя, совместимое с концентратором событий) и **Event Hub-compatible endpoint** (Конечная точка, совместимая с концентратором событий). Эти три значения понадобятся при создании приложения **read-d2c-messages**.

![Колонка "Сообщения" Центра Интернета вещей на портале Azure][6]

Теперь Центр Интернета вещей создан. Вы создали все необходимое для работы с этим руководством: имя узла, строку подключения и первичный ключ Центра Интернета вещей, а также имя и конечную точку, совместимые с концентратором событий.

## <a name="create-a-device-identity"></a>Создание удостоверения устройства
В этом разделе объясняется, как написать консольное приложение Java, которое создает удостоверение устройства в реестре удостоверений в Центре Интернета вещей. Устройство может подключиться к Центру Интернета вещей, только если в реестре удостоверений есть соответствующая запись. Дополнительные сведения см. в разделе, посвященном **реестру удостоверений**, в [руководстве разработчика по Центру Интернета вещей Azure][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которых выполняется идентификация во время отправки сообщений из устройства в облако для Центра Интернета вещей.

1. Создайте пустую папку с именем iot-java-get-started. В папке iot-java-get-started создайте проект Maven с именем **create-device-identity**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к папке create-device-identity.
3. Откройте в текстовом редакторе файл pom.xml из папки create-device-identity и добавьте приведенные ниже зависимости в узел **dependency** . Эта зависимость позволяет использовать в приложении пакет iot-service-client:
   
    ```
    </dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.2.18</version>
    </dependency>
    ```
    
    > [!NOTE]
    > Наличие последней версии пакета **iot-service-client** можно проверить с помощью [поиска Maven][lnk-maven-service-search].

4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл create-device-identity\src\main\java\com\mycompany\app\App.jav.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{yourhubconnectionstring}** значениями, записанными ранее.
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Измените подпись метода **main** , чтобы включить исключения, указанные ниже.
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Добавьте в текст метода **main** код, приведенный ниже. Этот код создает устройство с именем *javadevice* в реестре удостоверений Центра Интернета вещей, если оно еще не создано. Затем он отобразит идентификатор устройства и ключ, которые понадобятся вам позже:
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device ID: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Сохраните и закройте файл App.java.
11. Чтобы создать приложение **create-device-identity** с помощью Maven, выполните в командной строке в папке create-device-identity следующую команду:
    
    ```
    mvn clean package -DskipTests
    ```
12. Чтобы выполнить приложение **create-device-identity** с помощью Maven, выполните в командной строке в папке create-device-identity следующую команду.
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Запишите значения полей **Идентификатор устройства** и **Device key** (Ключ устройства). Эти значения понадобятся вам позже, когда вы будете создавать приложение, которое подключается к Центру Интернета вещей от имени устройства.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только идентификаторы устройств, необходимые для безопасного доступа к Центру Интернета вещей. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок включения или выключения, который позволяет вам отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков Центра Интернета вещей][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Получение сообщений с устройства в облако
В этом разделе вы создадите консольное приложение Java, которое считывает сообщения, передаваемые с устройства в облако из Центра Интернета вещей. Центр Интернета вещей предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, передаваемых с устройства в облако][lnk-process-d2c-tutorial], показано, как обрабатывать такие сообщения в больших количествах. В руководстве по [началу работы с концентраторами событий][lnk-eventhubs-tutorial] приведены дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применимо к конечным точкам Центра Интернета вещей, совместимым с концентраторами событий.

> [!NOTE]
> Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQP.
> 
> 

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства*, создайте проект Maven с именем **read-d2c-messages**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к папке read-d2c-messages.
3. Откройте в текстовом редакторе файл pom.xml из папки read-d2c-messages и добавьте зависимости, приведенные ниже, в узел **dependency** . Эта зависимость позволяет использовать пакет eventhubs-client в приложении, чтобы считывать данные с конечной точки, совместимой с концентраторами событий.
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.13.0</version> 
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **azure-eventhubs** можно проверить с помощью [поиска Maven][lnk-maven-eventhubs-search].

4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```
7. Добавьте приведенную ниже переменную уровня класса в класс **App** . Замените значения **{youriothubkey}**, **{youreventhubcompatibleendpoint}** и **{youreventhubcompatiblename}** значениями, записанными ранее.
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Добавьте приведенный ниже метод **receiveMessages** в класс **App**. Этот метод создает экземпляр **EventHubClient**, чтобы подключиться к конечной точке, совместимой с концентраторами событий, а затем асинхронно создает экземпляр **PartitionReceiver** для чтения из секции концентратора событий. Он выполняет непрерывную циклическую обработку, выводя сведения о сообщении, пока приложение не завершит работу.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Этот метод использует фильтр во время создания получателя, чтобы получатель читал только сообщения, отправленные в Центр Интернета вещей после запуска получателя. Этот метод удобно использовать в тестовой среде для просмотра текущего набора сообщений. В рабочей среде код должен обеспечивать обработку всех сообщений. Дополнительные сведения см. в руководстве по [обработке сообщений Центра Интернета вещей, отправляемых с устройства в облако, с помощью .NET][lnk-process-d2c-tutorial].
   > 
   > 
9. Измените подпись метода **main** , чтобы включить исключения, указанные ниже.
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. В метод **main** в классе **App** добавьте приведенный ниже код. Этот код создает два экземпляра, **EventHubClient** и **PartitionReceiver**, позволяя закрыть приложение, когда обработка сообщений будет завершена.
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > Предполагается, что вы создали Центр Интернета вещей на уровне F1 (Бесплатный). Бесплатный Центр Интернета вещей содержит два раздела с именами "0" и "1".
    > 
    > 
11. Сохраните и закройте файл App.java.
12. Чтобы создать приложение **read-d2c-messages** с помощью Maven, выполните в командной строке в папке read-d2c-messages следующую команду:
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создаете консольное приложение Java, которое имитирует устройство, отправляющее сообщения с устройства в облако в Центре Интернета вещей.

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства*, создайте проект Maven с именем **simulated-device**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к папке simulated-device.
3. Откройте в текстовом редакторе файл pom.xml из папки simulated-device и добавьте приведенные ниже зависимости в узел **dependency** . Эта зависимость позволит вам использовать в приложении пакет iothub-java-client для обмена данными с Центром Интернета вещей и сериализации объектов Java в JSON.
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.1.24</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-device-client** можно проверить с помощью [поиска Maven][lnk-maven-device-search].

4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл simulated-device\src\main\java\com\mycompany\app\App.java.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import com.microsoft.azure.sdk.iot.device.DeviceClient;
    import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;
    import com.microsoft.azure.sdk.iot.device.Message;
    import com.microsoft.azure.sdk.iot.device.IotHubStatusCode;
    import com.microsoft.azure.sdk.iot.device.IotHubEventCallback;
    import com.microsoft.azure.sdk.iot.device.MessageCallback;
    import com.microsoft.azure.sdk.iot.device.IotHubMessageResult;
    import com.google.gson.Gson;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Добавьте в класс **App** . Замените значение **{youriothubname}** именем Центра Интернета вещей, а **{yourdevicekey}** — значением ключа устройства, сформированным при работе с разделом *Создание удостоверения устройства*.
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    При создании экземпляра объекта **DeviceClient** в этом примере приложения используется переменная **protocol**. Для взаимодействия с Центром Интернета вещей можно использовать протокол MQTT, AMQP или HTTP.
8. Чтобы указать данные телеметрии, которые устройство отправляет в Центр Интернета вещей, добавьте в класс **App** следующий вложенный класс **TelemetryDataPoint**.
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double temperature;
      public double humidity;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Чтобы отобразить состояние подтверждения, возвращаемое Центром Интернета вещей при обработке сообщения с виртуального устройства, добавьте в класс **App** приведенный ниже вложенный класс **EventCallback**. Этот метод также уведомляет основной поток в приложении о том, что сообщение обработано.
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. Добавьте приведенный ниже вложенный класс **MessageSender** в класс **App**. Метод **run** в этом классе создает пример данных телеметрии для отправки в Центр Интернета вещей и ожидает подтверждения перед отправкой следующего сообщения:
    
    ```
    private static class MessageSender implements Runnable {
    
      public void run()  {
        try {
          double minTemperature = 20;
          double minHumidity = 60;
          Random rand = new Random();
    
          while (true) {
            double currentTemperature = minTemperature + rand.nextDouble() * 15;
            double currentHumidity = minHumidity + rand.nextDouble() * 20;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.temperature = currentTemperature;
            telemetryDataPoint.humidity = currentHumidity;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            msg.setProperty("temperatureAlert", (currentTemperature > 30) ? "true" : "false");
            msg.setMessageId(java.util.UUID.randomUUID().toString()); 
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
    
    Этот метод отправляет новое сообщение с устройства в облако через одну секунду после того, как Центр Интернета вещей подтверждает получение предыдущего сообщения. Сообщение содержит объект сериализации JSON с идентификатором устройства и случайные числа, что позволяет имитировать датчик температуры и влажности.
11. Замените метод **main** следующим кодом, который создает поток для отправки сообщений с устройства в облако в Центре Интернета вещей.
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Сохраните и закройте файл App.java.
13. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.
> 
> 

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Чтобы начать мониторинг первой секции в Центре Интернета вещей, в командной строке в папке read-d2c выполните следующую команду:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Приложение службы Центра Интернета вещей на языке Java для мониторинга сообщений, отправляемых с устройства в облако][7]
2. В командной строке в папке simulated-device выполните следующую команду, чтобы начать отправку данных телеметрии в Центр Интернета вещей:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Приложение устройства Центра Интернета вещей на языке Java для отправки сообщений с устройства в облако][8]
3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в Центр Интернета вещей.
   
    ![Плитка "Использование" на портале Azure, отображающая количество сообщений, отправленных в Центр Интернета вещей][43]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Это удостоверение позволяет приложению виртуального устройства отправлять в Центр Интернета вещей сообщения, передаваемые из устройства в облако. Кроме того, мы создали приложение, которое отображает сообщения, полученные Центром Интернета вещей. 

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Подключение устройства к Azure IoT][lnk-connect-device]
* [How to get started with device management (Node)][lnk-device-management] (Начало работы с управлением устройствами (Node))
* [Explore Azure IoT Edge architecture on Linux][lnk-gateway-SDK] (Приступая к работе с архитектурой Azure IoT Edge в Linux)

Сведения о том, как расширить решение для Интернета вещей и обрабатывать сообщения, отправляемые с устройства в облако в большом количестве, см. [здесь][lnk-process-d2c-tutorial].

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22

