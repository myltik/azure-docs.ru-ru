---
title: Начало работы с функцией управления Центром Интернета вещей Azure (Java) | Документы Майкрософт
description: Использование функции управления устройствами в Центре Интернета вещей Azure для начала удаленной перезагрузки устройства. Используйте пакет SDK для устройств Azure IoT для Java, чтобы реализовать приложение имитации устройства, включающее прямой метод, и пакет SDK для служб Azure IoT для Java, чтобы реализовать приложение-службу, вызывающее прямой метод.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636194"
---
# <a name="get-started-with-device-management-java"></a>Начало работы с управлением устройствами (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.
* Создание приложения имитации устройства, которое реализует прямой метод для перезагрузки устройства. Прямые методы вызываются из облака.
* Создание приложения, вызывающего прямой метод перезагрузки в приложении имитации устройства с помощью Центра Интернета вещей. Это приложение затем отслеживает передаваемые свойства с устройства, ожидая завершения операции перезагрузки.

Завершив работу с этим руководством, вы создадите два консольных приложения Java:

**simulated-device**. Это приложение:

* подключается к вашему Центру Интернета вещей с помощью идентификатора устройства, созданного ранее;
* получает вызов прямого метода перезагрузки;
* имитирует физическую перезагрузку;
* возвращает время последней перезагрузки через переданное свойство.

**trigger-reboot**. Это приложение:

* вызывает прямой метод в приложении имитации устройства;
* отображает ответ на вызов прямого метода, отправленный имитируемым устройством;
* отображает обновленные переданные свойства.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, работающих на устройствах и в серверной части решения.

Для работы с этим учебником необходимы указанные ниже компоненты.

* Java SE 8. <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Java для работы с этим руководством в ОС Windows или Linux.
* Maven 3.  <br/> В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить [Maven][lnk-maven] для работы с этим руководством в ОС Windows или Linux.
* [Node.js версии 0.10.0 или более поздней](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода

В этом разделе приведена процедура создания консольного приложения Java, которое выполняет следующие действия:

1. вызывает прямой метод перезагрузки в приложении имитации устройства;
1. отображает ответ;
1. опрашивает переданные свойства, отправляемые с устройства, для определения завершения перезагрузки.

Это консольное приложение подключается к Центру Интернета вещей для вызова прямого метода и чтения переданных свойств.

1. Создайте пустую папку с именем dm-get-started.

1. В папке dm-get-started создайте проект Maven с именем **trigger-reboot**, выполнив в командной строке следующую команду. Ниже показана одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке trigger-reboot.

1. Откройте в текстовом редакторе файл pom.xml из папки trigger-reboot и добавьте зависимости, приведенные ниже, в узел **dependency**. Эта зависимость позволит вам использовать в приложении пакет iot-service-client для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-service-client** можно проверить с помощью [поиска Maven][lnk-maven-service-search].

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

1. Сохраните и закройте файл pom.xml.

1. Откройте в текстовом редакторе файл trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

1. Добавьте в класс **App** . Замените `{youriothubconnectionstring}` строкой подключения к вашему центру Интернета вещей, которую вы записали, выполняя инструкции в разделе *Создание центра Интернета вещей*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Для реализации потока, который считывает переданные свойства с двойника устройства каждые 10 секунд, добавьте следующий вложенный класс в класс **App**:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Измените сигнатуру **основного** метода, чтобы создавать следующие исключения:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. Добавьте следующий код в метод **main** для вызова прямого метода перезагрузки на имитируемом устройстве:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

1. Добавьте следующий код в метод **main** для запуска потока, опрашивающего переданные свойства на имитируемом устройстве:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Чтобы иметь возможность остановить приложение, добавьте следующий код в метод **main**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Сохраните и закройте файл trigger-reboot\src\main\java\com\mycompany\app\App.java.

1. Выполните сборку внутреннего приложения **trigger-reboot** и исправьте ошибки (при наличии). В командной строке перейдите к папке trigger-reboot и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе приведена процедура создания консольного приложения Java, которое имитирует устройство. Приложение прослушивает вызовы прямого метода перезагрузки из вашего Центра Интернета вещей и немедленно отвечает на такие вызовы. Затем приложение на некоторое время переходит в спящий режим, чтобы имитировать процесс перезагрузки, после чего использует переданное свойство для уведомления внутреннего приложения **trigger-reboot** о завершении перезагрузки.

1. В папке dm-get-started создайте проект Maven с именем **simulated-device**, выполнив в командной строке следующую команду. Ниже показана одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке simulated-device.

1. Откройте в текстовом редакторе файл pom.xml из папки simulated-device и добавьте приведенную ниже зависимость в узел **dependency**. Эта зависимость позволит вам использовать в приложении пакет iot-service-client для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Наличие последней версии пакета **iot-device-client** можно проверить с помощью [поиска Maven][lnk-maven-device-search].

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

1. Сохраните и закройте файл pom.xml.

1. Откройте в текстовом редакторе исходный файл simulated-device\src\main\java\com\mycompany\app\App.java.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

1. Добавьте в класс **App** . Замените `{yourdeviceconnectionstring}` строкой подключения устройства, записанной в разделе *Создание удостоверения устройства*:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Чтобы реализовать обработчик обратного вызова для событий состояния прямого метода, добавьте следующий вложенный класс в класс **App**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Чтобы реализовать обработчик обратного вызова для событий состояния двойника устройства, добавьте следующий вложенный класс в класс **App**:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Чтобы реализовать обработчик обратного вызова для событий свойства, добавьте следующий вложенный класс в класс **App**:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Для реализации потока для имитации перезагрузка устройства добавьте указанный ниже вложенный класс в класс **App**. Поток бездействует в течение пяти секунд, а затем устанавливает переданное свойство **lastReboot**:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Чтобы реализовать прямой метод на устройстве, добавьте указанный ниже вложенный класс в класс **App**. Когда приложение имитации получает вызов прямого метода **reboot**, оно возвращает вызывающему объекту подтверждение, а затем запускает поток для обработки перезагрузки:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Измените сигнатуру метода **main**, чтобы создавать следующие исключения:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Для создания экземпляра **DeviceClient** добавьте следующий код в метод **main**:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Чтобы запустить прослушивание вызовов прямого метода, добавьте следующий код в метод **main**:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Для завершения работы симулятора устройства добавьте следующий код в метод **main**:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Сохраните и закройте файл simulated-device\src\main\java\com\mycompany\app\App.java.

1. Создайте внутреннее приложение **simulated-device** и исправьте все ошибки. В командной строке перейдите к папке simulated-device и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке simulated-device выполните следующую команду, чтобы начать прослушивать вызовы метода перезагрузки из Центра Интернета вещей:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Приложение имитации устройства Центра Интернета вещей на языке Java для прослушивания вызовов прямого метода перезагрузки][1]

1. В командной строке в папке trigger-reboot выполните следующую команду, чтобы вызвать метод перезагрузки на имитируемом устройстве из Центра Интернета вещей:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Приложение службы Центра Интернета вещей на языке Java для вызова прямого метода перезагрузки][2]

1. Ответ имитируемого устройства на вызов прямого метода перезагрузки:

    ![Ответ приложения имитации устройства Центра Интернета вещей на языке Java на вызов прямого метода][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22