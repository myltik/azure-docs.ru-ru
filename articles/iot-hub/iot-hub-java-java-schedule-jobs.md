---
title: Планирование заданий с помощью Центра Интернета вещей Azure (Java) | Документация Майкрософт
description: Планирование заданий в Центре Интернета вещей Azure для вызова прямого метода или изменения требуемых свойств на нескольких устройствах. Вы создадите приложения имитации устройства с помощью пакета SDK для устройств Azure IoT для Java, а также приложение-службу для выполнения заданий с помощью пакета SDK для служб Azure IoT для Java.
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
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: b8b0742054b0348ded39b6357d00f6eac3449f99
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="schedule-and-broadcast-jobs-java"></a>Планирование и трансляция заданий (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Центр Интернета вещей Azure позволяет планировать и отслеживать задания по обновлению для миллионов устройств. Что можно сделать с помощью заданий?

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

Задание выступает в роли оболочки для одного из этих действий и отслеживает его выполнение для определенного набора устройств. Запрос на двойнике устройства определяет набор устройств, для которых будет выполнено задание. Например, внутреннее приложение может использовать задание для вызова прямого метода перезапуска на 10 000 устройств. Вы можете определить набор устройств с помощью запроса на двойнике устройства и запланировать момент времени в будущем для выполнения задания. Задание отслеживает ход выполнения по мере того, как каждое из устройств получает вызов и выполняет прямой метод перезагрузки.

Дополнительные сведения об этих возможностях см. в указанных ниже статьях.

* Информация о двойниках устройств и их свойствах представлена в статье [Get started with device twins (Java)](iot-hub-java-java-twin-getstarted.md) (Приступая к работе с двойниками устройств).
* Прямые методы: [Общие сведения о прямых методах и информация о вызове этих методов из Центра Интернета вещей](iot-hub-devguide-direct-methods.md) и [Использование прямых методов (Java)](iot-hub-java-java-direct-methods.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание приложения для устройств, которое реализует прямой метод c именем **lockDoor**. Приложение для устройств также получает запросы на изменение требуемых свойств от внутреннего приложения.
* Создание внутреннего приложения, которое создает задание для вызова прямого метода **lockDoor** на нескольких устройствах. Еще одно задание отправляет обновления требуемых свойств на несколько устройств.

По завершении работы с этим руководством у вас будут готовы консольное приложение устройства и консольное приложение серверной части.

**simulated-device** подключается к Центру Интернета вещей, выполняет прямой метод **lockDoor** и обрабатывает изменения требуемых свойств.

**schedule-jobs** использует задания для вызова прямого метода **lockDoor** и обновления на нескольких устройствах требуемых свойств, установленных на двойнике устройства.

> [!NOTE]
> Статья [Общие сведения о пакетах SDK для Azure IoT и их использование](iot-hub-devguide-sdks.md) содержит сведения о разных пакетах SDK для Интернета вещей Azure, с помощью которых можно создать приложения для устройств и внутренние приложения.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником необходимы указанные ниже компоненты.

* Последняя версия [пакета SDK для Java SE 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Для добавления устройства в Центр Интернета вещей можно также использовать [расширение Интернета вещей для Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе приведена процедура создания консольного приложения Java, которое с помощью заданий выполняет следующие действия.

* Вызов прямого метода **lockDoor** на нескольких устройствах.
* Отправка изменений требуемых свойств на несколько устройств.

Создание приложения.

1. На компьютере разработки создайте пустую папку с именем `iot-java-schedule-jobs`.

1. В папке `iot-java-schedule-jobs` создайте проект Maven с именем **schedule-jobs**, выполнив следующую команду в командной строке. Обратите внимание, что это одна длинная команда.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. В командной строке перейдите к папке `schedule-jobs`.

1. Откройте в текстовом редакторе файл `pom.xml` из папки `schedule-jobs` и добавьте зависимости, приведенные ниже, в узел **dependencies**. Эта зависимость позволит вам использовать в приложении пакет **iot-service-client** для обмена данными с Центром Интернета вещей:

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

1. Откройте в текстовом редакторе файл `schedule-jobs\src\main\java\com\mycompany\app\App.java`.

1. Добавьте в файл следующие инструкции **import** .

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

1. Добавьте в класс **App** . Замените `{youriothubconnectionstring}` строкой подключения к вашему центру Интернета вещей, которую вы записали, выполняя инструкции в разделе *Создание центра Интернета вещей*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Добавьте следующий метод в класс **App**, чтобы запланировать задание для изменения требуемых свойств **Building** и **Floor** в двойнике устройства:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

1. Чтобы запланировать задание для вызова метода **lockDoor**, добавьте следующий метод в класс **App**:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

1. Чтобы отслеживать задания, добавьте приведенный ниже метод в класс **App**.

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

1. Чтобы получить сведения о выполнении запущенных заданий, добавьте следующий метод:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

1. Обновите подпись метода **main**, добавив следующее предложение `throws`:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Чтобы два задания запускались и отслеживались последовательно, добавьте следующий код в метод **main**:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

1. Сохраните и закройте файл `schedule-jobs\src\main\java\com\mycompany\app\App.java`.

1. Создайте приложение **schedule-jobs** и исправьте обнаруженные ошибки. В командной строке перейдите к папке `schedule-jobs` и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение Java, которое обрабатывает требуемые свойства, отправленные из Центр Интернета вещей реализует непосредственный вызов методов.

1. В папке `iot-java-schedule-jobs` создайте проект Maven с именем **simulated-device**, выполнив в командной строке следующую команду. Обратите внимание, что это одна длинная команда.

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    При создании экземпляра объекта **DeviceClient** в этом примере приложения используется переменная **protocol**.

1. Чтобы выводить оповещения двойника устройства в консоль, добавьте следующий вложенный класс в класс **App**:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Чтобы выводить оповещения прямого метода в консоль, добавьте следующий вложенный класс в класс **App**:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Чтобы обрабатывать вызовы прямого метода от Центра Интернета вещей, добавьте следующий вложенный класс в класс **App**:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

1. Обновите подпись метода **main**, добавив следующее предложение `throws`:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Добавьте в конец метода **main** следующий код, чтобы:
    * Создать клиент устройства для взаимодействия с Центром Интернета вещей.
    * Создать объект **Device** для хранения свойств двойника устройства.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

1. Добавьте следующий код в метод **main** для запуска клиентских служб на устройстве:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

1. Чтобы ожидать нажатия пользователем клавиши **Ввод** перед завершением работы, добавьте следующий код в конец метода **main**:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Сохраните и закройте файл `simulated-device\src\main\java\com\mycompany\app\App.java`.

1. Создайте приложение **simulated-device** и исправьте все ошибки. В командной строке перейдите к папке `simulated-device` и выполните следующую команду:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску консоли приложений.

1. В командной строке, находясь в папке `simulated-device`, выполните следующую команду, которая запускает приложение устройства для прослушивания изменений требуемых свойств и ожидания вызовов прямого метода:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Запуск клиента на устройстве](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. В командной строке в папке `schedule-jobs` выполните следующую команду, чтобы запустить приложение службы **schedule-jobs** для выполнения двух заданий. Первое задание устанавливает значения требуемых свойств, а второе вызывает прямой метод:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java-приложение службы Центра Интернета вещей создает два задания](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. Приложения для устройств обрабатывает изменения требуемого свойства и вызов прямого метода:

    ![Клиент устройства реагирует на изменения](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы создали серверное приложения для запуска двух заданий. Первое приложение устанавливает значения требуемых свойств, а второе вызывает прямой метод.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (руководство [Подключение устройства к Центру Интернета вещей с помощью Java](iot-hub-java-java-getstarted.md));
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство [Использование прямых методов (Java)](iot-hub-java-java-direct-methods.md)).
