---
title: Начало работы с двойниками устройств Центра Интернета вещей (Java) | Документация Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакет SDK для устройств Центра Интернета вещей Azure для Java, чтобы реализовать приложение устройства, и пакет SDK для служб Интернета вещей Azure для Java, чтобы реализовать приложение-службу, которое добавит теги и выполнит запрос к Центру Интернета вещей.
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
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 8611b55f6e4f33f8946d8c7c481163166d24049c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-device-twins-java"></a>Начало работы с двойниками устройств (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

В этом руководстве создаются два консольных приложения для Java:

* **add-tags-query** — внутреннее приложение Java, которое добавляет теги и выполняет запросы к двойникам устройств.
* **simulated-device** — это приложение устройства Java, которое подключается к Центру Интернета вещей и сообщает о его условии подключения c помощью сообщаемого свойства.

> [!NOTE]
> Статья [Общие сведения о пакетах SDK для Azure IoT и их использование](iot-hub-devguide-sdks.md) содержит сведения о разных пакетах SDK для Интернета вещей Azure, с помощью которых можно создать приложения для устройств и внутренние приложения.

Для работы с этим учебником необходимы указанные ниже компоненты.

* Последняя версия [пакета SDK для Java SE 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создадите приложение Java, которое добавляет метаданные расположения в виде тега в двойник устройства в Центре Интернета вещей, связанный с **myDeviceId**. Сначала приложение запрашивает в Центре Интернета вещей устройства, расположенные в США, а затем устройства, подключенные к сотовой сети.

1. На компьютере разработки создайте пустую папку с названием `iot-java-twin-getstarted`.

1. В папке `iot-java-twin-getstarted` создайте проект Maven **add-tags-query**, выполнив следующую команду в командной строке. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке `add-tags-query`.

1. Откройте в текстовом редакторе файл `pom.xml` из папки `add-tags-query` и добавьте зависимости, приведенные ниже, в узел **dependencies**. Эта зависимость позволит вам использовать в приложении пакет **iot-service-client** для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-service-client** можно проверить с помощью [поиска Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Добавьте следующий узел, **build**, после узла **dependencies**. Эта конфигурация дает указание Maven использовать Java версии 1.8 для создания приложения:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Сохраните и закройте файл `pom.xml`.

1. Откройте в текстовом редакторе файл `add-tags-query\src\main\java\com\mycompany\app\App.java`.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Добавьте в класс **App** . Замените `{youriothubconnectionstring}` строкой подключения к вашему центру Интернета вещей, которую вы записали, выполняя инструкции в разделе *Создание центра Интернета вещей*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Обновите подпись метода **main**, добавив следующее предложение `throws`:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Добавьте следующий код в метод **main** для создания объектов **DeviceTwin** и **DeviceTwinDevice**. Объект **DeviceTwin** обрабатывает взаимодействие с вашим Центром Интернета вещей. Объект **DeviceTwinDevice** представляет двойник устройства, его свойства и теги:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Добавьте следующий блок `try/catch` в метод **main**:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Чтобы обновить теги **region** и **plant** двойника устройства, добавьте следующий код в блок `try`:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

1. Чтобы отправить запрос к двойникам устройств в Центре Интернета вещей, добавьте следующий код в блок `try` после кода, добавленного на предыдущем шаге. Код выполняет два запроса. Каждый запрос возвращает не более 100 устройств:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

1. Сохраните и закройте файл `add-tags-query\src\main\java\com\mycompany\app\App.java`.

1. Создайте приложение **add-tags-query** и исправьте все ошибки. В командной строке перейдите к папке `add-tags-query` и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение Java, которое задает значение сообщаемого свойства, отправляемое в Центр Интернета вещей.

1. В папке `iot-java-twin-getstarted` создайте проект Maven с именем **simulated-device**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке `simulated-device`.

1. Откройте в текстовом редакторе файл `pom.xml` из папки `simulated-device` и добавьте зависимости, приведенные ниже, в узел **dependencies**. Эта зависимость позволит вам использовать в приложении пакет **iot-device-client** для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-device-client** можно проверить с помощью [поиска Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Добавьте следующий узел, **build**, после узла **dependencies**. Эта конфигурация дает указание Maven использовать Java версии 1.8 для создания приложения:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Сохраните и закройте файл `pom.xml`.

1. Откройте в текстовом редакторе файл `simulated-device\src\main\java\com\mycompany\app\App.java`.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Добавьте в класс **App** . Замените значение `{youriothubname}` именем Центра Интернета вещей, а `{yourdevicekey}` — значением ключа устройства, сформированным при работе с разделом *Создание удостоверения устройства*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    При создании экземпляра объекта **DeviceClient** в этом примере приложения используется переменная **protocol**. 

1. Добавьте в конец метода **main** следующий код, чтобы:
    * Создать клиент устройства для взаимодействия с Центром Интернета вещей.
    * Создать объект **Device** для хранения свойств двойника устройства.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

1. Добавьте следующий код в метод **main**, чтобы создать сообщаемое свойство **connectivityType** и отправить его в Центр Интернета вещей:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Добавьте в конец метода **main** следующий код: Во время ожидания нажатия клавиши **ВВОД** Центр Интернета вещей сообщает состояние операций двойника устройства:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Сохраните и закройте файл `simulated-device\src\main\java\com\mycompany\app\App.java`.

1. Создайте приложение **simulated-device** и исправьте все ошибки. В командной строке перейдите к папке `simulated-device` и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску консоли приложений.

1. В командной строке в папке `add-tags-query` выполните следующую команду, чтобы запустить приложение службы **add-tags-query**:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Обновление значений тегов и выполнение запросов устройств с помощью приложения службы Центра Интернета вещей](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Вы увидите теги **plant** и **region**, добавленные в двойник устройства. Первый запрос возвращает устройство, а второй — нет.

1. В командной строке в папке `simulated-device` выполните следующую команду, чтобы добавить в двойник устройства сообщаемое свойство **connectivityType**:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Клиент устройства добавляет сообщаемое свойство **connectivityType**](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. В командной строке в папке `add-tags-query` выполните следующую команду, чтобы повторно запустить приложение службы **add-tags-query**:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Обновление значений тегов и выполнение запросов устройств с помощью приложения службы Центра Интернета вещей](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Теперь устройство отправило свойство **connectivityType** в Центр Интернета вещей, и второй запрос возвращает устройство.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать сведения о двойнике устройства, используя похожий на SQL язык запросов Центра Интернета вещей.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (руководство [Подключение устройства к Центру Интернета вещей с помощью Java](iot-hub-java-java-getstarted.md));
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство [Использование прямых методов (Java)](iot-hub-java-java-direct-methods.md)).

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
