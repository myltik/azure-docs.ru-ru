---
title: "Моделирование устройств с помощью решения удаленного мониторинга в Azure | Документация Майкрософт"
description: "В этом руководстве показано, как использовать симулятор устройств с предварительно настроенным решением удаленного мониторинга."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 7550748c496f4e5c671ab49f9b139d2d4926d497
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Тестирование решения с помощью виртуальных устройств

Этого учебника показано, как настроить микрослужбу симуляторе устройства в удаленный мониторинга предварительно настроенных решений. Чтобы продемонстрировать возможности симулятора устройств, здесь рассматривается два сценария с приложением Интернета вещей компании Contoso.

В первом сценарии компания Contoso планирует протестировать новое интеллектуальное устройство управления освещением. Чтобы сделать это, необходимо создать виртуальное устройство со следующими характеристиками:

*Свойства*

| ИМЯ                     | Значения                      |
| ------------------------ | --------------------------- |
| Цвет                    | Белый, красный, синий            |
| Яркость               | 0–100                    |
| Ожидаемое время работы | Обратный отсчет, начиная с 10 000 часов |

*Телеметрия*

Ниже представлены данные лампочку отчеты в облако в виде потока данных:

| ИМЯ   | Значения      |
| ------ | ----------- |
| Status | on, off |
| в сети | true, false |

> [!NOTE]
> Значение телеметрии **в сети** является обязательным для всех типов имитации.

*Методы*

В таблице ниже приведены действия, поддерживаемые новым устройством.

| ИМЯ        |
| ----------- |
| Включение   |
| Выключение  |

*Начальное состояние*

В таблице ниже представлены начальные показатели устройства.

| ИМЯ                     | Значения |
| ------------------------ | -------|
| Начальный цвет            | Белый  |
| Начальная яркость       | 75     |
| Начальное ожидаемое время работы   | 10 000 |
| Начальное состояние передачи данных телеметрии | on   |

Во втором сценарии вы добавите новый тип телеметрии в имеющееся устройство **Chiller** компании Contoso.

В этом руководстве показано, как использовать симулятор устройств с предварительно настроенным решением удаленного мониторинга.

Из этого руководства вы узнаете, как выполнять такие задачи:

>[!div class="checklist"]
> * создавать тип устройства;
> * моделировать реакцию на событие пользовательского устройства;
> * добавлять новый тип устройства на панель мониторинга;
> * отправлять пользовательские данные телеметрии из имеющегося типа устройства.

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством нужен развернутый экземпляр решения для удаленного мониторинга в подписке Azure.

Если решение для удаленного мониторинга еще не развернуто, выполните инструкции из руководства [Развертывание предварительно настроенного решения для удаленного мониторинга](iot-suite-remote-monitoring-deploy.md).

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>Служба моделирования устройств

Служба моделирования устройств в предварительно настроенном решении позволяет вносить изменения в имеющиеся типы виртуальных устройств, а также создавать другие. С помощью пользовательских типов устройств можно тестировать реакцию на событие решения удаленного мониторинга перед подключением к нему физических устройств.

## <a name="create-a-simulated-device-type"></a>Создание типа виртуального устройства

Тип устройства в микрослужбе моделирования устройств проще всего создать, скопировав и изменив имеющийся тип. Ниже показано, как скопировать встроенное устройство **Chiller**, чтобы создать устройство **Lightbulb**.

1. Выполните следующую команду, чтобы клонировать репозиторий **device-simulation** GitHub на локальный компьютер:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Каждый тип устройства имеет JSON-файл модели и связанные скрипты. Они хранятся в папке `Services/data/devicemodels`. Скопируйте приведенные в таблице ниже файлы **Chiller**, чтобы на их основе создать файлы **Lightbulb**.

    | Источник                      | Место назначения                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Определение характеристик нового типа устройства

Характеристики типа устройства, например создаваемые им данные телеметрии и поддерживаемые методы, определены в файле `lightbulb-01.json`. Ниже показано, как изменить файл `lightbulb-01.json`, чтобы определить характеристики устройства **Lightbulb**.

1. В файле `lightbulb-01.json` измените метаданные устройства, как показано в следующем фрагменте кода:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. В файле `lightbulb-01.json` измените определение моделирования, как показано в следующем фрагменте кода:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. В файле `lightbulb-01.json` измените свойства типа устройства, как показано в следующем фрагменте кода:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. В файле `lightbulb-01.json` измените определения данных телеметрии типа устройства, как показано в следующем фрагменте кода:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. В файле `lightbulb-01.json` измените методы типа устройства, как показано в следующем фрагменте кода:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Сохраните файл `lightbulb-01.json`.

### <a name="simulate-custom-device-behavior"></a>моделировать реакцию на событие пользовательского устройства;

Реакция на событие моделирования типа устройства **Lightbulb** определена в файле `scripts/lightbulb-01-state.js`. Ниже показано, как изменить файл `scripts/lightbulb-01-state.js`, чтобы определить реакцию на событие устройства **Lightbulb**.

1. В файле `scripts/lightbulb-01-state.js` измените определение состояния, как показано в следующем фрагменте кода:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Замените функцию **vary** на функцию **flip**:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Измените функцию **main**, чтобы реализовать реакцию на событие, как показано в следующем фрагменте:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Сохраните файл `scripts/lightbulb-01-state.js`.

Метод **Switch On** устройства **Lightbulb** реализует файл `scripts/SwitchOn-method.js`. Ниже показано, как изменить файл `scripts/SwitchOn-method.js`.

1. В файле `scripts/SwitchOn-method.js` измените определение состояния, как показано в следующем фрагменте кода:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Чтобы включить освещение, измените функцию **main** следующим образом:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Сохраните файл `scripts/SwitchOn-method.js`.

1. Скопируйте файл `scripts/SwitchOn-method.js` и назовите его `scripts/SwitchOff-method.js`.

1. Чтобы выключить освещение, в файле `scripts/SwitchOff-method.js` измените функцию **main** следующим образом:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Сохраните файл `scripts/SwitchOff-method.js`.

### <a name="test-the-lightbulb-device-type"></a>Тестирование типа устройства Lightbulb

Чтобы протестировать тип устройства **Lightbulb**, сначала нужно проверить реакцию на событие своего типа устройства, запустив локальную копию службы **device-simulation**. Проверив и завершив отладку нового типа устройства в локальной среде, вы можете перестроить контейнер и повторно развернуть службу **device-simulation** в Azure.

Сведения о проверке и отладке в локальной среде см. в статье о [службе моделирования устройств](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md).

Настройте проект, чтобы скопировать новые файлы устройства **Lightbulb** в выходной каталог:

* Если вы используете Visual Studio, убедитесь, что вы добавили четыре новых файлов лампочки, созданную в предыдущем разделе, чтобы **служб** проекта в решении. Затем с помощью **обозревателя решений** отметьте их для копирования в выходной каталог.

* При использовании кода Visual Studio откройте **Services.csproj** и добавьте четыре новых лампочки файла, созданного в предыдущем разделе. Примеры см. в записях существующего файла модели **Services.csproj**.

Сведения о проверке нового устройства в развернутом решении см. в следующих источниках:

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Развертывание контейнеров из пользовательской учетной записи docker-hub)
* [Update a deployed container via manual copy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy) (Изменение развернутого контейнера путем копирования вручную)

Экземпляры нового типа можно подготовить на странице **Устройства**:

![Просмотр списка доступных виртуальных устройств](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Вы можете просмотреть данные телеметрии из виртуального устройства:

![Просмотр данных телеметрии устройства Lightbulb](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Вы можете вызвать методы **SwitchOn** и **SwitchOff** на устройстве:

![Вызов методов устройства Lightbulb](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Сведения о создании образа Docker с новым типом устройства, который можно развернуть в Azure, см. в [этом разделе](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="add-a-new-telemetry-type"></a>Добавление нового типа данных телеметрии

В этом разделе описано, как изменить имеющийся тип виртуального устройства, чтобы реализовать поддержку нового типа данных телеметрии.

### <a name="locate-the-chiller-device-type-files"></a>Получение файлов типа устройства Chiller

Ниже показано, как найти файлы, определяющие встроенное устройство **Chiller**.

1. Выполните команду ниже, чтобы клонировать репозиторий **device-simulation** GitHub на локальный компьютер (если вы еще не сделали это).

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Каждый тип устройства имеет JSON-файл модели и связанные скрипты. Они хранятся в папке `data/devicemodels`. Тип виртуального устройства **Chiller** определен в следующих файлах:
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Указание нового типа данных телеметрии

Ниже показано, как добавить новый тип данных телеметрии (**Внутренняя температура**) в тип устройства **Chiller**.

1. Откройте файл `chiller-01.json` .

1. Измените значение **SchemaVersion** следующим образом:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. В разделе **InitialState** добавьте два следующих определения:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. В массиве **Telemetry** добавьте следующее определение:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Сохраните файл `chiller-01.json`.

1. Откройте файл `scripts/chiller-01-state.js` .

1. Добавьте в переменную **state** следующие поля:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Добавьте следующую строку в функцию **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Сохраните файл `scripts/chiller-01-state.js`.

### <a name="test-the-chiller-device-type"></a>Тестирование типа устройства Chiller

Чтобы протестировать измененный тип устройства **Chiller**, сначала нужно проверить реакцию на событие своего типа устройства, запустив локальную копию службы **device-simulation**. Проверив и завершив отладку измененного типа устройства в локальной среде, вы можете перестроить контейнер и повторно развернуть службу **device-simulation** в Azure.

При запуске службы **device-simulation** в локальной среде она отправляет данные телеметрии в решение для удаленного мониторинга. Экземпляры измененного типа можно подготовить на странице **Устройства**.

Сведения о проверке и отладке изменений в локальной среде см. в разделах [Running the service with Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) (Запуск службы с помощью Visual Studio) и [Build and Run from the command line](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line) (Создание и выполнение в окне командной строки).

Сведения о проверке нового устройства в развернутом решении см. в следующих источниках:

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Развертывание контейнеров из пользовательской учетной записи docker-hub)
* [Update a deployed container via manual copy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy) (Изменение развернутого контейнера путем копирования вручную)

Экземпляры измененного типа можно подготовить на странице **Устройства**:

![Добавление измененного устройства Chiller](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

На виртуальном устройстве можно просмотреть данные телеметрии **Внутренняя температура**.

Сведения о создании образа Docker с новым типом устройства, который можно развернуть в Azure, см. в [этом разделе](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы узнали, как:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * создавать тип устройства;
> * моделировать реакцию на событие пользовательского устройства;
> * добавлять новый тип устройства на панель мониторинга;
> * отправлять пользовательские данные телеметрии из имеющегося типа устройства.

Теперь, когда вы узнали, как использовать службу моделирования устройств, мы советуем ознакомиться с руководством [Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Node.js)](iot-suite-connecting-devices-node.md).

Дополнительные сведения для разработчиков о решении удаленного мониторинга см. в следующих источниках:

* [Справочник разработчика](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Руководство по устранению неполадок для разработчиков](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->