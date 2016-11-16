---
title: "Начало работы с Центром Интернета вещей Azure. Язык Java | Документация Майкрософт"
description: "Начальное руководство по работе с центром IoT Azure для языка Java. Используя центр IoT Azure, язык Java и пакеты SDK для &quot;Интернета вещей&quot; Microsoft Azure, создайте собственное решение IoT."
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
ms.date: 08/11/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7913033d0812d6f6c2ff9a413862cb63c1e5a59e


---
# <a name="get-started-with-azure-iot-hub-for-java"></a>Приступая к работе с центром Azure IoT с использованием Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

В конце этого руководства у вас будет три консольных приложения Java:

* **create-device-identity**— создает удостоверение устройства и соответствующий ключ безопасности для подключения к виртуальному устройству;
* **read-d2c-messages**— отображает данные телеметрии, отправляемые виртуальным устройством;
* **simulated-device** — подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и отправляет сообщения телеметрии с частотой один раз в секунду по протоколу AMQPS.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK Центра Интернета вещей) содержит сведения о разных пакетах SDK, с помощью которых можно создать приложения, запускаемые на устройствах и серверной части решения.
> 
> 

Для работы с этим учебником требуется:

* Java SE 8. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Java для работы с этим руководством в ОС Windows или Linux.
* Maven 3.  <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Maven для работы с этим руководством в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную пробную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Наконец, запишите значение поля **Первичный ключ**, а затем щелкните **Сообщения**. В колонке **Сообщения** запишите значения полей **Имя, совместимое с концентратором событий** и **Конечная точка, совместимая с концентратором событий**. Эти три значения необходимы при создании приложения **read-d2c-messages** .

![Колонка "Сообщения" Центра Интернета вещей на портале Azure][6]

Вы создали Центр Интернета вещей, и у вас есть все необходимое для работы с этим руководством: имя узла центра, строка подключения к центру, первичный ключ центра, а также имя и конечная точка, совместимые с концентратором событий.

## <a name="create-a-device-identity"></a>Создание удостоверения устройства
В этом разделе вы напишете консольное приложение Java, которое создает удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к центру IoT, только если в реестре удостоверений устройств есть соответствующая запись. Дополнительные сведения см. в разделе **Реестр удостоверений устройств** [руководства для разработчиков Центра Интернета вещей][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которых выполняется идентификация во время отправки сообщений из устройства в облако для центра IoT.

1. Создайте новую пустую папку с именем iot-java-get-started. В папке iot-java-get-started создайте проект Maven с именем **create-device-identity** , выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к новой папке create-device-identity.
3. Откройте в текстовом редакторе файл pom.xml из папки create-device-identity и добавьте приведенные ниже зависимости в узел **dependency** . Это позволяет использовать пакет iothub-service-sdk в приложении.
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл create-device-identity\src\main\java\com\mycompany\app\App.jav.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;
   
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
9. Добавьте в текст метода **main** код, приведенный ниже. Этот код создает устройство с именем *javadevice* в реестре удостоверений центра IoT, если оно еще не создано. Затем он отобразит идентификатор устройства и ключ, которые понадобятся вам позже.
   
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
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Сохраните и закройте файл App.java.
11. Чтобы создать приложение **create-device-identity** с помощью Maven, выполните в командной строке в папке create-device-identity следующую команду.
    
    ```
    mvn clean package -DskipTests
    ```
12. Чтобы запустить приложение **create-device-identity** с помощью Maven, выполните в командной строке в папке create-device-identity следующую команду.
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Запишите значения полей **Идентификатор устройства** и **Ключ устройства**. Эти данные понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

> [!NOTE]
> В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок включения или выключения, который позволяет вам отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков Центра Интернета вещей][lnk-devguide-identity].
> 
> 

## <a name="receive-devicetocloud-messages"></a>Получение сообщений с устройства в облако
В этом разделе вы создадите консольное приложение Java, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр Интернета вещей предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial], показано, как обрабатывать такие сообщения в больших количествах. В руководстве по [началу работы с концентраторами событий][lnk-eventhubs-tutorial] приведены дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам Центра Интернета вещей, совместимым с концентраторами событий.

> [!NOTE]
> Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQP.
> 
> 

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства* , создайте проект Maven с именем **read-d2c-messages** , выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к новой папке read-d2c-messages.
3. Откройте в текстовом редакторе файл pom.xml из папки read-d2c-messages и добавьте зависимости, приведенные ниже, в узел **dependency** . Это позволяет использовать пакет eventhubs-client в приложении для считывания данных с конечной точки, совместимой с концентраторами событий.
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```
4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
   
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```
7. Добавьте в класс **App** . Замените значения **{youriothubkey}**, **{youreventhubcompatibleendpoint}** и **{youreventhubcompatiblename}** значениями, записанными ранее.
   
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
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
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
   > Этот метод использует фильтр во время создания получателя, чтобы получатель читал только сообщения, отправленные в центр IoT после запуска получателя. Это удобно в тестовой среде, где можно увидеть текущий набор сообщений. В рабочей среде код должен обеспечивать обработку всех сообщений. Дополнительные сведения см. в руководстве [по обработке сообщений, отправляемых с устройства Центра Интернета вещей в облако][lnk-process-d2c-tutorial].
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
    > Предполагается, что вы создали центр IoT на уровне F1 (Бесплатный). Бесплатный центр IoT содержит два раздела с именами 0 и 1.
    > 
    > 
11. Сохраните и закройте файл App.java.
12. Чтобы создать приложение **read-d2c-messages** с помощью Maven, выполните в командной строке в папке read-d2c-messages следующую команду.
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создаете консольное приложение Java, которое имитирует устройство, отправляющее сообщения с устройства в облако в центре IoT.

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства* , создайте проект Maven с именем **simulated-device** , выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. В командной строке перейдите к новой папке simulated-device.
3. Откройте в текстовом редакторе файл pom.xml из папки simulated-device и добавьте приведенные ниже зависимости в узел **dependency** . Это даст вам возможность использовать в приложении пакет iothub-java-client для обмена данными с центром IoT и сериализации объектов Java в JSON.
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Сохраните и закройте файл pom.xml.
5. Откройте в текстовом редакторе файл simulated-device\src\main\java\com\mycompany\app\App.java.
6. Добавьте в файл следующие инструкции **import** .
   
    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{youriothubname}** именем Центра Интернета вещей, а **{yourdevicekey}** — значением ключа устройства, сформированным при работе с разделом *Создание удостоверения устройства*.
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    При создании экземпляра объекта **DeviceClient** в этом примере приложения используется переменная **protocol**. Для взаимодействия с Центром Интернета вещей можно использовать протокол HTTPS или AMQPS.
8. Чтобы указать данные телеметрии, которые устройство отправляет в Центр Интернета вещей, добавьте в класс **App** следующий вложенный класс **TelemetryDataPoint**.
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Чтобы отобразить состояние подтверждения, возвращаемое Центром Интернета вещей при обработке сообщения с виртуального устройства, добавьте в класс **App** приведенный ниже вложенный класс **EventCallback**. Этот метод также уведомляет основной поток в приложении после обработки сообщения.
   
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
10. Добавьте приведенный ниже вложенный класс **MessageSender** в класс **App**. Метод **run** в этом классе создает пример данных телеметрии для отправки в центр IoT и ожидает подтверждения перед отправкой следующего сообщения:
    
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
            Message msg = new Message(msgStr);
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
    
    Этот метод отправляет новое сообщение с устройства в облако через одну секунду после того, как центр IoT подтверждает получение предыдущего сообщения. Сообщение содержит объект сериализации JSON с deviceId и случайное число, что позволяет имитировать датчик скорости ветра.
11. Замените метод **main** следующим кодом, который создает поток для отправки сообщений с устройства в облако в центр IoT.
    
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
13. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду.
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].
> 
> 

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Чтобы начать мониторинг первой секции в центре IoT, в командной строке в папке read-d2c выполните следующую команду:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Клиентское приложение службы Центра Интернета вещей на языке Java для наблюдения за сообщениями, отправляемыми с устройства в облако][7]
2. В командной строке в папке simulated-device выполните следующую команду, чтобы начать отправку данных телеметрии в центр IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Клиентское приложение устройства Центра Интернета вещей на языке Java для отправки сообщений с устройства в облако][8]
3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в концентратор.
   
    ![Плитка "Использование" на портале Azure, отображающая количество сообщений, отправленных в Центр Интернета вещей][43]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве описана настройка нового центра IoT на портале Azure и создание удостоверения устройства в реестре удостоверений центра. Это удостоверение позволяет приложению сымитированного устройства отправлять в центр сообщения, передаваемые из устройства в облако. Кроме того, здесь описано создание приложения, которое отображает сообщения, полученные центром. 

Чтобы продолжить знакомство с центром IoT и изучить другие сценарии IoT, см. следующие ресурсы.

* [Connecting your device][lnk-connect-device] (Подключение устройства)
* [Приступая к работе с функцией управления устройствами Центра Интернета вещей Azure с использованием C# (предварительная версия)][lnk-device-management]
* [Начало работы с пакетом SDK для шлюза IoT][lnk-gateway-SDK]

В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial], показано, как расширить решение Центра Интернета вещей и обрабатывать такие сообщения в большом количестве.

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

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Nov16_HO2-->


