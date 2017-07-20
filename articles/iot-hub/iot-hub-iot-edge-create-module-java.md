---
title: "Создание модуля Azure IoT Edge с помощью Java | Документация Майкрософт"
description: "В этом руководстве показано, как с помощью последних пакетов Maven для Azure IoT Edge написать модуль преобразователя данных BLE."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 682feb4d889ecd881abe1a70d36e0a5a4df3d910
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Создание модуля Azure IoT Edge с помощью Java

В этом руководстве показано, как с помощью Java создать модуль для Azure IoT Edge.

В этом руководстве приводятся пошаговые инструкции по настройке среды и написанию модуля преобразователя данных [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) с помощью последних пакетов Maven для Azure IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

В этом разделе настраивается среда для разработки модуля IoT Edge. Данные настройки действительны для следующих операционных систем: *64-разрядная версия Windows* и *64-разрядная версия Linux (Ubuntu/Debian 8)*.

Требуется следующее ПО:

* [клиент Git](https://git-scm.com/downloads);
* [**64-разрядный** пакет JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html);
* [Maven](https://maven.apache.org/install.html).

Откройте окно терминала командной строки и клонируйте следующий репозиторий:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Общая архитектура

Платформа Azure IoT Edge интенсивно применяет [архитектуру фон Неймана](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Это означает, что вся архитектура Azure IoT Edge является системой, которая обрабатывает входные и создает выходные данные. А каждый отдельный модуль также является небольшой подсистемой ввода-вывода. В этом руководстве описываются следующее два модуля:

1. Модуль, который получает имитацию сигнала [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) и преобразует его в сообщение в формате [JSON](https://en.wikipedia.org/wiki/JSON).
2. Модуль, который выводит полученное сообщение [JSON](https://en.wikipedia.org/wiki/JSON).

На следующем рисунке показан типичный сквозной поток данных для этого проекта:

![Поток данных между тремя модулями](media/iot-hub-iot-edge-create-module/dataflow.png "Входные данные: имитация модуля BLE; Процессор: модуль преобразователя; Выходные данные: модуль принтера")

## <a name="understanding-the-code"></a>Основные сведения о коде

### <a name="maven-project-structure"></a>Структура проекта Maven

Поскольку пакеты Azure IoT Edge основаны на Maven, необходимо создать типичную структуру проекта Maven, которая содержит файл `pom.xml`.

Файл POM наследуется из пакета `com.microsoft.azure.gateway.gateway-module-base`, который объявляет все зависимости, необходимые для проекта модуля. К ним относятся исполняемые двоичные файлы, путь к файлу конфигурации шлюза и режим выполнения. Это значительно экономит время, избавляя нас от необходимости писать и переписывать снова и снова сотни строк кода.

Необходимо обновить файл pom.xml, объявив необходимые зависимости и подключаемые модули, а также имя файла конфигурации, который будет использоваться нашим модулем, как показано в следующем фрагменте кода.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Базовое представление о модуле Azure IoT Edge

Модуль Azure IoT Edge можно рассматривать как обработчик данных, задачей которого является получение входных данных, их обработка и генерирование выходных данных.

Входными данными могут быть данные, получаемые от оборудования (например, от детектора движения), сообщения от других модулей и любые другие данные (например, случайное число, периодически генерируемое таймером).

Выходные данные похожи на входные. Они могут быть триггером каких-либо действий оборудования (например, мигание светодиодного индикатора), сообщениями для других модулей или чем-то другим (например, вывод данных в консоль).

Модули взаимодействуют друг с другом с помощью класса `com.microsoft.azure.gateway.messaging.Message`. **Содержимым** `Message` является массив байтов, который может представлять любой необходимый тип данных. В `Message` также доступны **Свойства**, которые являются просто сопоставлением строк. **Свойства** можно сравнить с заголовками в HTTP-запросе или метаданными файла.

Чтобы разработать модуль Azure IoT Edge в Java, необходимо создать класс модуля, который наследуется из `com.microsoft.azure.gateway.core.GatewayModule`, и реализовать необходимые абстрактные методы `receive()` и `destroy()`. На этом этапе также можно реализовать дополнительные методы `start()` и `create()`. В следующем фрагменте кода показано, как приступить к разработке модуля Azure IoT Edge.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Модуль преобразователя

| Входные данные                    | Процессор                              | Выходные данные                 | Исходный файл            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Сообщение с данными температуры | Синтаксический анализ и создание сообщения JSON | Структурирование сообщения JSON | `ConverterModule.java` |

Этот модуль является типичным модулем Azure IoT Edge. Он принимает сообщения с данными температуры от других модулей (аппаратного модуля или, как в нашем случае, имитации модуля BLE). Затем он нормализует эти сообщения в структурированное сообщение JSON, добавляя идентификатор сообщения, задавая значение свойства (например, необходимо ли активировать предупреждение о температуре) и т. д.

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Модуль принтера

| Входные данные                          | Процессор | Выходные данные                     | Исходный файл          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Любое сообщение от других модулей | Недоступно       | Запись сообщения в консоль | `PrinterModule.java` |

Это простой и не нуждающийся в объяснении модуль, который выводит полученные сообщения в окне терминала.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Конфигурация Azure IoT Edge

Последним шагом перед запуском модулей является настройка Azure IoT Edge и установление подключений между модулями.

Сначала необходимо объявить загрузчик Java (так как Azure IoT Edge поддерживает загрузчики для различных языков), на который можно будет ссылаться по атрибуту `name` в последующих разделах.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

После объявления загрузчиков нам также необходимо объявить модули. По аналогии с объявлением загрузчиков на них также можно сослаться по атрибуту `name`. При объявлении модуля необходимо указать загрузчик, который он должен использовать (тот, который мы определили ранее), и точку входа (это должно быть нормализованное имя класса нашего модуля) для каждого модуля. Модуль `simulated_device` является собственным модулем, который входит в базовый пакет среды выполнения Azure IoT Edge. В JSON-файл всегда необходимо включать аргументы `args`, даже если они имеют значение `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

На последнем этапе настройки устанавливаются подключения. Каждое подключение выражается с помощью `source` и `sink`. Оба этих параметра должны ссылаться на предварительно определенный модуль. Выходное сообщение модуля `source` будет перенаправлено в качестве входных данных в модуль `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Запуск модулей

Используйте `mvn package` для выполнения сборки всех компонентов в папку `target/`. Для чистоты сборки также рекомендуется использовать `mvn clean package`.

С помощью `mvn exec:exec` запустите Azure IoT Edge. Данные температуры и все свойства должны теперь выводиться на консоль с определенной частотой.

Чтобы завершить работу приложения, нажмите клавишу `<Enter>`.

> [!IMPORTANT]
> Не рекомендуется использовать сочетание клавиш Ctrl + C для завершения работы приложения шлюза IoT Edge. Это действие может вызвать аварийное завершение процесса.


