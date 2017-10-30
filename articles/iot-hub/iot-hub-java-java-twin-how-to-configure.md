---
title: "Использование свойств двойников устройств Центра Интернета вещей (Java) | Документация Майкрософт"
description: "Настройка устройств с помощью двойников устройств Центра Интернета вещей Azure. Используйте пакет SDK для устройств Azure IoT для Java, чтобы реализовать приложение имитации устройства, и пакет SDK для служб Azure IoT для Java, чтобы реализовать приложение-службу, которое изменит конфигурацию устройства с помощью двойника устройства."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Настройка устройств с помощью требуемых свойств

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Завершив работу с этим руководством, вы создадите два консольных приложения Java:

* **simulated-device** — это приложение имитации устройства, которое ожидает обновления требуемой конфигурации и сообщает о состоянии обновления имитации конфигурации.
* **set-configuration** — это внутреннее приложение, которое задает требуемую конфигурацию на устройстве и выполняет запрос к процессу обновления конфигурации.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.
> 
> 

Для работы с этим учебником требуется:

* Последняя версия [пакета SDK для Java SE 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Если вы выполнили указания руководства [Начало работы с двойниками устройств][lnk-twin-tutorial], то у вас уже есть Центр Интернета вещей и удостоверение устройства **myDeviceId**. В этом случае можно перейти к разделу [Создание приложения имитации устройства][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Создание приложения имитации устройства
В этом разделе вы создадите консольное приложение Java, которое подключается к центру как **myDeviceId**, ожидает обновления требуемой конфигурации и сообщает об обновлениях в ходе обновления имитации конфигурации.

1. Создайте пустую папку с именем dt-get-started.

1. В папке dt-get-started создайте проект Maven с именем **simulated-device**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке simulated-device.

1. Откройте в текстовом редакторе файл pom.xml из папки simulated-device и добавьте приведенную ниже зависимость в узел **dependency**. Эта зависимость позволит вам использовать в приложении пакет iot-service-client для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
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
    import java.util.Scanner;
    ```

1. Добавьте в класс **App** . Замените **{yourdeviceconnectionstring}** строкой подключения устройства, записанной в разделе *Создание удостоверения устройства*.

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Чтобы реализовать обработчик обратного вызова для событий состояния двойника устройства (для целей отладки), добавьте следующий вложенный класс в класс **App**:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Добавьте приведенный ниже вложенный класс в класс **App** .

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > Класс TelemetryConfig расширяет [класс Device][lnk-java-device-class] для получения доступа к обратным вызовам нужных свойств.

1. Измените сигнатуру метода **main**, чтобы создавать следующие исключения:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Добавьте следующий код в метод **main** для создания экземпляра **DeviceClient** и **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Добавьте следующий код в метод **main** для запуска служб двойника устройства:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Добавьте следующий код в метод **main** для завершения работы симулятора устройства при необходимости:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Сохраните и закройте файл simulated-device\src\main\java\com\mycompany\app\App.java.

1. Создайте внутреннее приложение **simulated-device** и исправьте все ошибки. В командной строке перейдите к папке simulated-device и выполните следующую команду:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > В этом руководстве не имитируется поведение при одновременном обновлении конфигураций. В некоторых случаях обновление конфигурации может пройти в соответствии с изменениями целевой конфигурации, в других может потребоваться поместить изменения в очередь, а в остальных они могут быть отклонены с ошибкой. Следует учитывать желаемое поведение для конкретной конфигурации и добавить соответствующую логику перед ее изменением.
   > 
   > 

## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение Java, которое обновляет *требуемые свойства* двойника устройства, связанного с **myDeviceId**, с использованием нового объекта конфигурации телеметрии. Затем оно выполняет запрос к двойникам устройств, которые хранятся в Центре Интернета вещей, и показывает разницу между требуемой и сообщаемой конфигурацией устройства.

1. В папке dt-get-started создайте проект Maven с именем **set-configuration**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите в папку set-configuration.

1. Откройте в текстовом редакторе файл pom.xml из папки trigger-reboot и добавьте зависимости, приведенные ниже, в узел **dependency**. Эта зависимость позволит вам использовать в приложении пакет iot-service-client для обмена данными с Центром Интернета вещей:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. Откройте в текстовом редакторе исходный файл set-configuration\src\main\java\com\mycompany\app\App.java.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Добавьте в класс **App** . Замените **{youriothubconnectionstring}** строкой подключения к вашему Центру Интернета вещей, которую вы записали, выполняя инструкции в разделе *Создание Центра Интернета вещей*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Для запроса и обновления двойников устройств на имитации устройства добавьте следующий код в метод **main**:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Сохраните и закройте файл set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Выполните сборку внутреннего приложения **set-configuration** и исправьте все ошибки. В командной строке перейдите в папку set-configuration и выполните следующую команду:

    `mvn clean package -DskipTests`
   
    Этот код получает двойник устройства для **myDeviceId**, а затем обновляет его требуемые свойства, используя новый объект конфигурации телеметрии.
    Затем он каждые 10 секунд запрашивает данные двойников устройств, которые хранятся в Центре Интернета вещей, и выводит требуемые и сообщаемые конфигурации телеметрии. Чтобы узнать, как создавать эффективные отчеты на всех устройствах, см. статью [Справочник по языку запросов для двойников и заданий][lnk-query].
   
   > [!IMPORTANT]
   > Это приложение выполняет запрос к Центру Интернета вещей каждые 10 секунд в демонстрационных целях до тех пор, пока устройство не будет обновлено. Используйте запросы для создания представляемых пользователям отчетов на многих устройствах, а не для обнаружения изменений. Если вашему решению требуется отправка уведомлений о событиях устройства в реальном времени, используйте [уведомления двойников][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Результаты запроса выводятся после операции сообщения устройства с задержкой (не более минуты). Таким образом обеспечивается работа инфраструктуры запросов в очень широком масштабе.  Для получения согласованных представлений одного двойника устройства используйте метод **getDeviceTwin** в классе **DeviceTwin**.
   > 
   > 

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке simulated-device выполните следующую команду, чтобы начать прослушивать вызовы двойника устройства из Центра Интернета вещей:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. В командной строке в папке set-configuration выполните следующую команду для запроса и обновления двойников устройств на имитации устройства из Центра Интернета вещей:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Ответ имитируемого устройства на вызов прямого метода перезагрузки:

    ![Приложение имитации устройства Центра Интернета вещей на языке Java отвечает на вызов двойника устройства][img-deviceconfigured]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы установили требуемую конфигурацию в качестве *требуемых свойств* из серверной части решения и написали код приложения для устройства, которое обнаруживает изменения и имитирует многоэтапный процесс обновления, о состоянии которого сообщается в качестве сообщаемых свойств.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (учебник [Приступая к работе с Центром Интернета вещей Azure (Node)][lnk-iothub-getstarted]);
* запланировать или выполнить операции на больших наборах устройств (см. учебник [Планирование и трансляция заданий][lnk-schedule-jobs];
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (учебник [Use direct methods][lnk-methods-tutorial] (Использование прямых методов)).

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
