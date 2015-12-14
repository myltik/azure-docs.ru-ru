<properties
	pageTitle="Создание клиента Java центра IoT | Microsoft Azure"
	description="Следуйте этому учебнику, чтобы создать клиент Java центра IoT, использующий пакет SDK устройства Microsoft Azure IoT для Java * для взаимодействия с центром IoT Azure."
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
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Создание клиентского приложения центра IoT Azure с использованием Java

В этой статье описывается, как создать клиентское приложение, которое использует [пакет SDK устройства Microsoft Azure IoT для Java][lnk-java-sdk] для взаимодействия с центром Azure IoT. В руководстве показано, как создать и построить проект с помощью средства [Maven][apache-maven]. Вы можете следовать этим инструкциям на компьютере под управлением Windows или Linux.

Документацию по API Java можно найти [здесь][lnk-java-api-docs].

## Установка

В разделе [Подготовка среды разработки][devbox-setup] вы найдете сведения о предварительных условиях и настройке среды разработки на Windows или Linux.

> [AZURE.NOTE]Важно выполнить шаги, описанные в разделе [Подготовка среды разработки][devbox-setup], перед началом этого учебника, чтобы установить необходимые компоненты и добавить необходимые JAR-файлы в локальный репозиторий Maven.

## Создание проекта

1. В вашем средстве командной строки выполните следующую команду, чтобы создать новый пустой проект Maven в подходящем расположении на компьютере разработчика:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]Это одна длинная команда. Убедитесь, что скопировали всю команду, если хотите вставить ее в средство командной строки.

    Эта команда создает папку проекта с именем *iot-device* и стандартной структурой проекта Maven. Дополнительные сведения см. в разделе [Maven за 5 минут][maven-five-minutes] на веб-сайте Apache.

2. Откройте в текстовом редакторе файл **pom.xml** в папке iot-device.

3. Добавьте следующий новый раздел **dependency** после существующего для включения необходимых клиентских библиотек:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. Добавьте следующий раздел **build** после раздела **dependencies**, чтобы окончательный JAR-файл включал зависимости, и манифест идентифицировал класс **main**.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Сохраните файл **pom.xml**.

## Создание приложения

1. Откройте в текстовом редакторе файл **App.java** в папке iot-device/src/main/java/com/mycompany/app.

2. Добавьте следующие инструкции **import**, которые включают библиотеки устройства IoT, после инструкции **package**:

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
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. Добавьте следующий класс **EventCallback** как вложенный класс внутри класса **App** . Метод **execute** в классе **EventCallback** вызывается, когда устройство получает подтверждение от центра IoT в ответ на сообщение устройства в облако.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. Добавьте следующий класс **MessageCallback** как вложенный класс внутри класса **App** . Метод **execute** в классе **MessageCallback** позволяет отправить ответное сообщение в центр IoT в ответ на полученное устройством сообщение из облака.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. Замените существующий метод **main** следующим кодом, который:

  - Создает экземпляр **DeviceClient**.
  - Инициализирует обратный вызов для сообщения от облака устройству.
  - Открывает **DeviceClient**, позволяющий отправлять сообщения с устройства в облако и получать сообщения, отправленные из облака на устройство.
  - Отправляет десять образцов сообщений в центр IoT.

    Замените `<your device connection string>` строкой подключения действительного устройств. Можно подготовить устройство и получить его строку подключения с помощью средства [DeviceExplorer][lnk-device-explorer] или средства [IoT Hub Explorer][lnk-iothub-explorer].

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. Чтобы скомпилировать код и упаковать его в JAR-файл, выполните следующую команду в командной строке в папке проекта **iot-device**:

    ```
    mvn package
    ```

7. Чтобы запустить приложение, выполните следующую команду в командной строке в папке проекта **iot-device**:

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. Можно использовать средство [DeviceExplorer][lnk-device-explorer] для наблюдения за сообщениями устройства в облако, получаемыми вашим центром IoT, и для отправки сообщений из облака на устройство из вашего центра IoT.

## Гранулярность ведения журнала изменений

Чтобы изменить степень гранулярности ведения журнала, добавьте следующую строку в ваш файл `config.properties`:

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]Описание различных уровней ведения журнала можно найти [здесь](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html).

Затем установите свойство JVM `java.util.logging.config.file={Path to your config.properties file}`.

Чтобы вести журнал кадров AMQP, установите переменную среды `PN_TRACE_FRM=1` в командной среде.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1203_2015-->