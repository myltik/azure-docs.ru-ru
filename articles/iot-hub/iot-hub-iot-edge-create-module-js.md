---
title: "Создание модуля Azure IoT Edge с помощью Node.js | Документация Майкрософт"
description: "В этом руководстве показано, как с помощью последних пакетов NPM для Azure IoT Edge и генератора Yeoman написать модуль преобразователя данных BLE."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 12e0702c31f36368fd95127abeede1a0d4ff2c87
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Создание модуля Azure IoT Edge с помощью Node.js

В этом руководстве показано, как с помощью JS создать модуль для Azure IoT Edge.

В этом руководстве приводятся пошаговые инструкции по настройке среды и написанию модуля преобразователя данных [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) с помощью последних пакетов NPM для Azure IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

В этом разделе настраивается среда для разработки модуля IoT Edge. Данные настройки действительны для следующих операционных систем: *64-разрядная версия Windows* и *64-разрядная версия Linux (Ubuntu 14+)*.

Требуется следующее ПО:
* [клиент Git](https://git-scm.com/downloads);
* [Node LTS](https://nodejs.org);
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Архитектура

Платформа Azure IoT Edge интенсивно применяет [архитектуру фон Неймана](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Это означает, что вся архитектура Azure IoT Edge является системой, которая обрабатывает входные и создает выходные данные. А каждый отдельный модуль также является небольшой подсистемой ввода-вывода. В этом руководстве описываются следующее два модуля:

1. Модуль, который получает имитацию сигнала [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) и преобразует его в сообщение в формате [JSON](https://en.wikipedia.org/wiki/JSON).
2. Модуль, который выводит полученное сообщение [JSON](https://en.wikipedia.org/wiki/JSON).

На следующем рисунке показан типичный сквозной поток данных для этого проекта:

![Поток данных между тремя модулями](media/iot-hub-iot-edge-create-module/dataflow.png "Входные данные: имитация модуля BLE; Процессор: модуль преобразователя; Выходные данные: модуль принтера")

## <a name="set-up-the-environment"></a>Настройка среды
Ниже показано, как с помощью JS быстро настроить среду, необходимую для написания первого модуля преобразователя BLE.

### <a name="create-module-project"></a>Создание проекта модуля
1. Откройте окно командной строки, выполните команду `yo az-iot-gw-module`.
2. Следуйте инструкциям на экране, чтобы завершить инициализацию модуля проекта.

### <a name="project-structure"></a>Структура проекта
Проект модуля состоит из следующих компонентов:

`modules` — настраиваемые исходные файлы модуля JS. Замените файлы `sensor.js` и `printer.js`, заданные по умолчанию, своими файлами модуля.

`app.js` — вводный файл для запуска экземпляра Edge.

`gw.config.json` — файл конфигурации для настройки модулей, загружаемых Edge.

`package.json` — сведения о метаданных для проекта модуля.

`README.md` — базовая документация для проекта модуля.


### <a name="package-file"></a>Файл пакета

Данный файл `package.json` объявляет все сведения о метаданных, необходимые для проекта модуля, такие как имя, версия, вводный файл, сценарии, среда выполнения и зависимости разработки.

В следующем фрагменте кода показано, как настроить пример проекта преобразователя BLE.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Вводный файл
Файл `app.js` определяет способ инициализации экземпляра Edge. Здесь не требуется вносить каких-либо изменений.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Интерфейс модуля
Модуль Azure IoT Edge можно рассматривать как обработчик данных, задачей которого является получение входных данных, их обработка и генерирование выходных данных.

Входными данными могут быть данные, получаемые от оборудования (например, от детектора движения), сообщения от других модулей и любые другие данные (например, случайное число, периодически генерируемое таймером).

Выходные данные похожи на входные. Они могут быть триггером каких-либо действий оборудования (например, мигание светодиодного индикатора), сообщениями для других модулей или чем-то другим (например, вывод данных в консоль).

Модули взаимодействуют друг с другом с помощью объекта `message`. **Содержимым** `message` является массив байтов, который может представлять любой необходимый тип данных. В `message` также доступны **Свойства**, которые являются просто сопоставлением строк. **Свойства** можно сравнить с заголовками в HTTP-запросе или метаданными файла.

Чтобы разработать модуль Azure IoT Edge в JS, необходимо создать объект модуля, который реализует необходимые методы `receive()`. На этом этапе также можно реализовать дополнительные методы `create()`, `start()` и `destroy()`. В следующем фрагменте кода показано формирование шаблонов объекта модуля JS.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Модуль преобразователя
| Входные данные                    | Процессор                              | Выходные данные                 | Исходный файл            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Сообщение с данными температуры | Синтаксический анализ и создание сообщения JSON | Структурирование сообщения JSON | `converter.js` |

Этот модуль является типичным модулем Azure IoT Edge. Он принимает сообщения с данными температуры от других модулей (аппаратного модуля или, как в нашем случае, имитации модуля BLE). Затем он нормализует эти сообщения в структурированное сообщение JSON, добавляя идентификатор сообщения, задавая значение свойства (например, необходимо ли активировать предупреждение о температуре) и т. д.

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Модуль принтера
| Входные данные                          | Процессор | Выходные данные                     | Исходный файл          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Любое сообщение от других модулей | Недоступно       | Запись сообщения в консоль | `printer.js` |

Это простой и не нуждающийся в объяснении модуль, который выводит полученные сообщения (свойство или содержимое) в окне терминала.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Конфигурация
Последним шагом перед запуском модулей является настройка Azure IoT Edge и установление подключений между модулями.

Сначала необходимо объявить загрузчик `node` (так как Azure IoT Edge поддерживает загрузчики для различных языков), на который можно будет ссылаться по атрибуту `name` в последующих разделах.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

После объявления загрузчиков нам также необходимо объявить модули. По аналогии с объявлением загрузчиков на них также можно сослаться по атрибуту `name`. При объявлении модуля необходимо указать загрузчик, который он должен использовать (тот, который мы определили ранее), и точку входа (это должно быть нормализованное имя класса нашего модуля) для каждого модуля. Модуль `simulated_device` является собственным модулем, который входит в базовый пакет среды выполнения Azure IoT Edge. Включите в JSON-файл аргументы `args`, даже если они имеют значение `null`.

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
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

На последнем этапе настройки устанавливаются подключения. Каждое подключение выражается с помощью `source` и `sink`. Оба этих параметра должны ссылаться на предварительно определенный модуль. Выходное сообщение модуля `source` перенаправляется в качестве входных данных в модуль `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Запуск модулей
1. `npm install`
2. `npm start`

Чтобы завершить работу приложения, нажмите клавишу `<Enter>`.

> [!IMPORTANT]
> Не рекомендуется использовать сочетание клавиш Ctrl + C для завершения работы приложения IoT Edge. Это действие может вызвать аварийное завершение процесса.

