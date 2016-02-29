## Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Java, которое создает новое удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к центру IoT, только если в реестре удостоверений устройств есть соответствующая запись. Более подробную информацию см. в разделе **Реестр удостоверений устройств** [руководства разработчика по центру IoT][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которого выполняется идентификация устройства во время отправки сообщений с устройства в облако для центра IoT.

1. Создайте новую пустую папку с именем iot-java-get-started. В папке iot-java-get-started создайте новый проект Maven с именем **create-device-identity**, используя следующую команду в командной строке. Обратите внимание: это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке create-device-identity.

3. Откройте в текстовом редакторе файл pom.xml из папки create-device-identity и добавьте следующие зависимости в узел **dependency**. Это позволяет использовать пакет iothub-service-sdk в приложении.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    ```
    
4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.jav.

6. Добавьте в файл следующие инструкции **import**.

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Добавьте следующие переменные уровня класса в класс **App**, заменив **{yourhubname}** и **{yourhubkey}** значениями, которые вы записали ранее.

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Измените подпись метода **main**, чтобы включить исключения, показанные ниже.

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Добавьте в текст метода **main** следующий код. Этот код создает устройство с именем *javadevice* в реестре идентификации центра IoT, если оно еще не создано. Затем он отобразит идентификатор устройства и ключ, которые понадобятся вам позже.

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

11. Чтобы создать приложение **create-device-identity** с помощью Maven, выполните следующую команду в командной строке в папке create-device-identity.

    ```
    mvn clean package -DskipTests
    ```

12. Чтобы запустить приложение **create-device-identity** с помощью Maven, выполните следующую команду в командной строке в папке create-device-identity.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Запишите **идентификатор устройства** и **ключ устройства**. Эти данные понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

> [AZURE.NOTE] В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок "Вкл./Выкл.", который позволяет отключить доступ для отдельного устройства. Если в вашем приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][lnk-devguide-identity].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Java, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако][lnk-processd2c-tutorial] показано, как обрабатывать сообщения, отправляемые с устройства в облако, с применением масштабирования. В руководстве [Приступая к работе с концентраторами событий][lnk-eventhubs-tutorial] приводятся дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам центра IoT, совместимым с концентраторами событий.

1. В папке iot-java-get-started, созданной в разделе *Создание удостоверения устройства*, создайте новый проект Maven с именем **read-d2c-messages**, используя следующую команду в командной строке. Обратите внимание: это одна длинная команда.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. В командной строке перейдите к новой папке read-d2c-messages.

3. Откройте в текстовом редакторе файл pom.xml из папки read-d2c-messages и добавьте следующие зависимости в узел **dependency**. Это дает возможность использовать пакет eventhubs-client в приложении для считывания данных с конечной точки, совместимой с концентраторами событий.

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Сохраните и закройте файл pom.xml.

5. Откройте в текстовом редакторе файл read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Добавьте в файл следующие инструкции **import**.

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Добавьте следующие переменные уровня класса в класс **App**.

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Добавьте следующий вложенный класс в класс **App**. Приложение создает два потока для выполнения **MessageReceiver**, чтобы читать сообщения из двух разделов в концентраторе событий.

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Добавьте следующий конструктор в класс **MessageReceiver**.

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Добавьте следующий метод **run** в метод **MessageReceiver**. Этот метод создает экземпляр **EventHubReceiver** для чтения из раздела концентратора событий. Он постоянно зациклен и выводит сведения о сообщении в консоль, если для параметра **stopThread** установлено значение true.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Этот метод использует фильтр во время создания получателя, чтобы получатель читал только сообщения, отправленные в центр IoT после запуска получателя. Это удобно в тестовой среде, где можно увидеть текущий набор сообщений, но в рабочей среде код должен обрабатывать все сообщения. Дополнительные сведения см. в руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако, с помощью центра IoT][lnk-processd2c-tutorial].

11. Измените подпись метода **main**, чтобы включить исключения, показанные ниже.

    ```
    public static void main( String[] args ) throws IOException
    ```

12. В метод **main** в классе **App** добавьте следующий код. Этот код создает экземпляр **EventHubClient**, чтобы подключиться к конечной точке, совместимой с концентраторами событий, в центре IoT. Затем он создает два потока для чтения из двух разделов. Замените значения **{youriothubkey}**, **{youreventhubcompatiblenamespace}** и **{youreventhubcompatiblename}** значениями, записанными ранее. Значение заполнителя **{youreventhubcompatiblenamespace}** поступает из **конечной точки, совместимой с концентраторами событий**, отображаясь в формате **xxxxnamespace.servicebus.windows.net**.

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] Предполагается, что вы создали центр IoT на уровне F1 (Бесплатный). Бесплатный центр IoT содержит два раздела с именами 0 и 1. Если вы создали центр IoT с помощью одной из ценовых категорий, измените код, чтобы создать **MessageReceiver** для каждого раздела.

13. Сохраните и закройте файл App.java.

14. Чтобы создать приложение **read-d2c-messages** с помощью Maven, выполните в командной строке в папке read-d2c-messages следующую команду.

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0218_2016-->