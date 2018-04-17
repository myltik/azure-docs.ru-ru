---
title: Обновление встроенного ПО устройства c помощью Центра Интернета вещей Azure (Java/Java) | Документация Майкрософт
description: Запуск обновления встроенного ПО устройства с помощью функции управления устройствами в Центре Интернета вещей. Используйте пакет Java SDK для устройств Azure IoT, чтобы реализовать приложение виртуального устройства и приложение службы, которое активирует обновление встроенного ПО.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: ff8fc5cc1623c570f79a8acfa0afbcf95450c1d2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Использование функции управления устройствами для запуска обновления встроенного ПО устройства (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

В руководстве по [началу управления устройством][lnk-dm-getstarted] вы узнали, как использовать [двойник устройства][lnk-devtwin] и [прямые методы][lnk-c2dmethod] для удаленной перезагрузки устройства. В этом руководстве используются те же примитивы Центра Интернета вещей, а также содержатся рекомендации по комплексному обновлению имитации встроенного ПО.  Этот шаблон используется в реализации обновления встроенного ПО для [примера реализации устройства Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание консольного приложения Java, вызывающего прямой метод **firmwareUpdate** в приложении виртуального устройства с помощью Центра Интернета вещей.
* Создание консольного приложения Java, которое имитирует устройство и реализует прямой метод **firmwareUpdate**. Этот метод запускает многоэтапный процесс, который сначала ожидает скачивания образа встроенного ПО, а затем скачивает его и применяет. В ходе выполнения каждого этапа обновления устройство использует сообщаемые свойства для продолжения процесса.

Завершив работу с этим руководством, вы создадите два консольных приложения Java:

**firmware-update** вызывает прямой метод на виртуальном устройстве, отображает ответ и периодически отображает обновления сообщаемых свойств;

**simulated-device** подключается к концентратору IoT с созданным ранее идентификатором устройства, получает вызов прямого метода firmwareUpdate и запускает симуляцию обновления встроенного ПО.

Для работы с этим учебником требуется:

* Последняя версия [пакета SDK для Java SE 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Активация удаленного обновления встроенного ПО на устройстве с помощью прямого метода
В этом разделе вы создадите консольное приложение Java, которое инициирует удаленное обновление встроенного ПО на устройстве. Приложение использует прямой метод для запуска обновления, а также применяет запросы двойников устройства для периодического получения состояния обновления активного встроенного ПО.

1. Создайте пустую папку с именем fw-get-started.

1. В папке fw-get-started создайте проект Maven с именем **firmware-update**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке firmware-update.

1. Откройте в текстовом редакторе файл pom.xml из папки firmware-update и добавьте приведенную ниже зависимость в узел **dependencies**. Эта зависимость позволит вам использовать в приложении пакет iot-service-client для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. Сохраните и закройте файл pom.xml.

1. Откройте в текстовом редакторе исходный файл firmware-update\src\main\java\com\mycompany\app\App.java.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Добавьте в класс **App** . Замените **{youriothubconnectionstring}** строкой подключения к вашему Центру Интернета вещей, которую вы записали, выполняя инструкции в разделе *Создание Центра Интернета вещей*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Для реализации метода, который считывает сообщаемые свойства с двойника устройства, добавьте следующий код в класс **App**:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Измените сигнатуру метода **main**, чтобы создавать следующие исключения:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Добавьте следующий код в метод **main** для вызова прямого метода firmwareUpdate на имитированном устройстве:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Добавьте следующий код в метод **main** для опроса сообщаемых свойств с имитированного устройства:

    ```java
    ShowReportedProperties();
    ```

1. Чтобы иметь возможность остановить приложение, добавьте следующий код в метод **main**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Сохраните и закройте файл firmware-update\src\main\java\com\mycompany\app\App.java.

1. Выполните сборку серверного приложения **firmware-update** и исправьте ошибки, если они будут обнаружены. В командной строке перейдите к папке firmware-update и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Создание виртуального устройства для обработки прямых вызовов
В этом разделе вы создадите на Java консольное приложение виртуального устройства, которое умеет принимать прямой метод firmwareUpdate. Это приложение будет запускать многоэтапный процесс, имитирующий обновление встроенного ПО и передающий состояние процесса в свойстве reportedProperties.

1. В папке fw-get-started создайте проект Maven с именем **simulated-device**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке simulated-device.

1. Откройте в текстовом редакторе файл pom.xml из папки firmware-update и добавьте приведенную ниже зависимость в узел **dependencies**. Эта зависимость позволит вам использовать в приложении пакет iot-service-client для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
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
    import java.util.HashMap;
    ```

1. Добавьте в класс **App** . Замените **{yourdeviceconnectionstring}** строкой подключения устройства, записанной в разделе *Создание удостоверения устройства*.

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Чтобы реализовать поддержку прямого метода, добавьте в класс **App** следующие вложенные классы:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
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

1. Чтобы реализовать поддержку двойника устройства, добавьте в класс **App** следующие вложенные классы:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Чтобы реализовать процесс обновления встроенного ПО, добавьте в класс **App** указанный ниже вложенный класс:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Измените сигнатуру метода **main**, чтобы создавать следующие исключения:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Чтобы инициировать подпрограммы прямых методов и двойника устройства, добавьте следующий код в метод **main**:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Чтобы иметь возможность остановить приложение, добавьте следующий код в конец метода **main**:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Сохраните и закройте файл simulated-device\src\main\java\com\mycompany\app\App.java.

1. Создайте приложение **simulated-device** и исправьте все ошибки. В командной строке перейдите к папке simulated-device и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **simulated-device** выполните следующую команду, чтобы начать прослушивать вызовы прямого метода для обновления встроенного ПО.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. В командной строке в папке **firmware-update** выполните следующую команду, чтобы запустить обновление встроенного ПО и опрос двойников устройств на имитированном устройстве из Центра Интернета вещей.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. В консоли вы увидите ответы виртуального устройства на вызов прямого метода.

    ![Успешное обновление встроенного ПО][img-fwupdate]

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы использовали прямой метод для активации удаленного обновления встроенного ПО на устройстве. Также вы использовали переданные свойства для отслеживания хода обновления встроенного ПО.

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device