<properties
	pageTitle="Начало работы с центром IoT Azure. Язык Java | Microsoft Azure"
	description="Начальное руководство по работе с центром IoT Azure для языка Java. Используя центр IoT Azure, язык Java и пакеты SDK для Интернета вещей Microsoft Azure, создайте собственное решение IoT."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/06/2016"
     ms.author="dobett"/>

# Приступая к работе с центром Azure IoT с использованием Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Введение

Центр IoT Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств Интернета вещей (IoT) и серверной частью решения. Одной из крупнейших задач, стоящих перед проектами IoT, является надежное и безопасное подключение устройств к серверной части решения. Чтобы устранить эту проблему, центр IoT предоставляет следующие возможности:

- глобальный надежный двусторонний обмен сообщениями между облаком и устройством;
- безопасную связь — используются учетные данные безопасности, уникальные для каждого устройства, и контроль доступа;
- простое подключение устройств, так как используются библиотеки устройств для большинства популярных языков и платформ.

В этом учебнике описаны следующие процедуры.

- Создание центра IoT с помощью портала Azure.
- Создание удостоверения устройства в центре IoT.
- Создание имитации устройства, отправляющего данные телеметрии в серверную часть облачного решения.

В конце этого руководства у вас будет три консольных приложения Java:

* **create-device-identity** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к виртуальному устройству;
* **read-d2c-messages** — отображает данные телеметрии, отправляемые виртуальным устройством;
* **simulated-device**— подключается к центру IoT с созданным ранее удостоверением устройства и отправляет сообщения с частотой один раз в секунду с использованием протокола AMQPS.

> [AZURE.NOTE] Статья [Пакеты SDK для центра IoT][lnk-hub-sdks] содержит сведения о разных пакетах SDK, которые можно использовать для создания приложений, которые будут запущены на устройствах и серверной части решения.

Для работы с этим руководством требуется:

+ Java SE 8. <br/> В статье о [подготовке среды разработки][lnk-dev-setup] описывается, как установить Java для целей этого руководства в ОС Windows или Linux.

+ Maven 3. <br/> В статье о [подготовке среды разработки][lnk-dev-setup] описывается, как установить Maven для целей этого руководства в ОС Windows или Linux.

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Наконец, в колонке "Центр IoT" щелкните **Параметры**, а затем в колонке **Параметры** щелкните **Обмен сообщениями**. Когда откроется колонка **Обмен сообщениями**, запишите значения полей **Имя, совместимое с концентраторами событий** и **Конечная точка, совместимая с концентраторами событий**. Эти значения понадобятся при создании приложения **read-d2c-messages**.

![][6]

Вы создали центр IoT, и у вас есть все необходимое для выполнения оставшейся части этого руководства: имя узла центра IoT, строка подключения к центру IoT, имя и конечная точка, совместимые с концентраторами событий.

## Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Java, которое создает новое удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к центру IoT, только если в реестре удостоверений устройств есть соответствующая запись. Более подробную информацию см. в разделе **Реестр удостоверений устройств** [руководства разработчика по центру IoT][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которого выполняется идентификация устройства во время отправки сообщений с устройства в облако для центра IoT.

1. Создайте новую пустую папку с именем iot-java-get-started. В папке iot-java-get-started создайте проект Maven с именем **create-device-identity**, используя следующую команду в командной строке. Обратите внимание: это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке create-device-identity.

3. Откройте в текстовом редакторе файл pom.xml из папки create-device-identity и добавьте следующие зависимости в узел **dependency**. Это позволяет использовать пакет iothub-service-sdk в приложении.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    ```
    
4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.jav.

6. Добавьте в файл следующие инструкции **import**:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{yourhubname}** и **{yourhubkey}** значениями, записанными ранее:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Измените подпись метода **main**, чтобы включить исключения, показанные ниже:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Добавьте в текст метода **main** следующий код. Этот код создает устройство с именем *javadevice* в реестре удостоверений центра IoT, если оно еще не создано. Затем он отобразит идентификатор устройства и ключ, которые понадобятся вам позже.

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

11. Чтобы создать приложение **create-device-identity** с помощью Maven, выполните следующую команду в командной строке в папке create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Чтобы запустить приложение **create-device-identity** с помощью Maven, выполните следующую команду в командной строке в папке create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Запишите **идентификатор устройства** и **ключ устройства**. Эти данные понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

> [AZURE.NOTE] В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок "Вкл./Выкл.", который позволяет отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][lnk-devguide-identity].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Java, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial] показано, как обрабатывать такие сообщения с применением масштабирования. В руководстве по [началу работы с концентраторами событий][lnk-eventhubs-tutorial] приведены дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам центра IoT, совместимым с концентраторами событий.

> [AZURE.NOTE] Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQPS.

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства*, создайте проект Maven с именем **read-d2c-messages**, используя следующую команду в командной строке. Обратите внимание: это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке read-d2c-messages.

3. Откройте в текстовом редакторе файл pom.xml из папки read-d2c-messages и добавьте следующие зависимости в узел **dependency**. Это позволяет использовать пакет eventhubs-client в приложении для считывания данных с конечной точки, совместимой с концентраторами событий.

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```

4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Добавьте в файл следующие инструкции **import**:

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

7. Добавьте следующие переменные уровня класса в класс **App**. Замените значения **{youriothubkey}**, **{youreventhubcompatiblenamespace}** и **{youreventhubcompatiblename}** значениями, записанными ранее. Значение заполнителя **{youreventhubcompatiblenamespace}** поступает из **конечной точки, совместимой с концентраторами событий**, отображаясь в формате **xyznamespace**. (Иначе говоря, удалите префикс ****sb://** и суффикс **.servicebus.windows.net** из значения конечной точки, совместимой с концентраторами событий и полученной от портала.)

    ```
    private static String namespaceName = "{youreventhubcompatiblenamespace}";
    private static String eventHubName = "{youreventhubcompatiblename}";
    private static String sasKeyName = "iothubowner";
    private static String sasKey = "{youriothubkey}";
    private static long now = System.currentTimeMillis();
    ```

8. Добавьте следующий метод **receiveMessages** в класс **App**. Этот метод создает экземпляр **EventHubClient** для подключения к конечной точке, совместимой с концентраторами событий, а затем асинхронно создает экземпляр **PartitionReceiver** для чтения из секции концентратора событий. Он выполняет непрерывную циклическую обработку, выводя сведения о сообщении, пока приложение не завершит работу.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
        client = EventHubClient.createFromConnectionString(connStr.toString()).get();
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
                Iterable<EventData> receivedEvents = receiver.receive().get();
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

    > [AZURE.NOTE] Этот метод использует фильтр во время создания получателя, чтобы получатель читал только сообщения, отправленные в центр IoT после запуска получателя. Это удобно в тестовой среде, так как вы можете увидеть текущий набор сообщений. В рабочей же среде код должен обрабатывать все сообщения. Дополнительные сведения см. в статье [Учебник: как обрабатывать сообщения, отправляемые с устройства в облако, с помощью центра IoT][lnk-process-d2c-tutorial].

9. Измените подпись метода **main**, чтобы включить исключения, показанные ниже.

    ```
    public static void main( String[] args ) throws IOException
    ```

10. В метод **main** в классе **App** добавьте следующий код. Этот код создает два экземпляра **EventHubClient** и **PartitionReceiver**, позволяя закрыть приложение, когда обработка сообщений будет завершена.

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

    > [AZURE.NOTE] Предполагается, что вы создали центр IoT на уровне F1 (Бесплатный). Бесплатный центр IoT содержит два раздела с именами 0 и 1.

11. Сохраните и закройте файл App.java.

12. Чтобы создать приложение **read-d2c-messages** с помощью Maven, выполните в командной строке в папке read-d2c-messages следующую команду:

    ```
    mvn clean package -DskipTests
    ```

## Создание приложения виртуального устройства

В этом разделе вам предстоит создать консольное приложение Java, которое имитирует устройство, отправляющее сообщения с устройства в облако в центре IoT.

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства*, создайте проект Maven с именем **simulated-device**, используя следующую команду в командной строке. Обратите внимание: это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке simulated-device.

3. Откройте в текстовом редакторе файл pom.xml из папки simulated-device и добавьте следующие зависимости в узел **dependency**. Это даст вам возможность использовать в приложении пакет iothub-java-client для обмена данными с центром IoT и сериализации объектов Java в JSON.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Добавьте в файл следующие инструкции **import**:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{youriothubname}** именем центра IoT, а **{yourdeviceid}** и **{yourdevicekey}** — значениями для устройства, сгенерированными в разделе *Создание удостоверения устройства*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    При создании экземпляра объекта **DeviceClient** в этом примере приложения используется переменная **protocol**. Для взаимодействия с центром IoT можно использовать протокол HTTPS или AMQPS.

8. Добавьте следующий вложенный класс **TelemetryDataPoint** в класс **App**, чтобы указать данные телеметрии, которые устройство отправляет в центр IoT:

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

9. Добавьте следующий вложенный класс **EventCallback** в класс **App**, чтобы отобразить состояние подтверждения, возвращаемое центром IoT при обработке сообщения с виртуального устройства. Этот метод также уведомляет основной поток в приложении после обработки сообщения.

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Добавьте следующий вложенный класс **MessageSender** в класс **App**. Метод **run** в этом классе создает пример данных телеметрии для отправки в центр IoT, ожидая подтверждения перед отправкой следующего сообщения:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Этот метод отправляет новое сообщение с устройства в облако через одну секунду после того, как центр IoT подтверждает получение предыдущего сообщения. Сообщение содержит объект сериализации JSON с deviceId и случайное число, что позволяет имитировать датчик скорости ветра.

11. Замените метод **main** следующим кодом, который создает поток для отправки сообщений, относящихся к типу "с устройства в облако", в центр IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Сохраните и закройте файл App.java.

13. Чтобы создать приложение **simulated-device** с помощью Maven, выполните в командной строке в папке simulated-device следующую команду:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].

## Запуск приложений

Теперь все готово к запуску приложений.

1. Чтобы начать мониторинг первой секции в центре IoT, в командной строке в папке read-d2c выполните следующую команду:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="0"
    ```

    ![][7]

1. Чтобы начать мониторинг второй секции в центре IoT, в командной строке в папке read-d2c выполните следующую команду:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="1"
    ```

    ![][7]

2. В командной строке в папке simulated-device выполните следующую команду, чтобы начать отправку данных телеметрии в центр IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в центр.

    ![][43]

## Дальнейшие действия

В этом руководстве описана настройка нового центра IoT на портале и создание удостоверения устройства в реестре удостоверений центра. Это удостоверение устройства позволяет приложению виртуального устройства отправлять в центр сообщения, передаваемые из устройства в облако. Также описано создание приложения, которое отображает сообщения, полученные центром. Дальнейшее описание функций и сценариев центра IoT см. в следующих руководствах:

- [Отправка сообщений с облака на устройство в центре IoT][lnk-c2d-tutorial] — описывается, как отправлять сообщения на устройства и обрабатывать подтверждения о доставке от центра IoT.
- [Обработка сообщений, отправляемых с устройств в облако][lnk-process-d2c-tutorial] — описывается, как надежно обрабатывать данные телеметрии и интерактивные сообщения, поступающие от устройств.
- [Передача файлов с устройств][lnk-upload-tutorial] — описывается модель использования сообщений, отправляемых из облака на устройства, для упрощения передачи файлов с устройств.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->