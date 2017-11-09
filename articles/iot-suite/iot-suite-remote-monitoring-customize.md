---
title: "Настройка решения для удаленного мониторинга в Azure | Документация Майкрософт"
description: "Статья содержит сведения о способе доступа к исходному коду предварительно настроенного решения для удаленного мониторинга."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/07/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0e15193975b3142f604c2f3c0391b21d6fb1fac1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Настройка предварительно настроенного решения для удаленного мониторинга

Статья содержит сведения о способе доступа к исходному коду и настройке предварительно настроенного решения для удаленного мониторинга. Содержание статьи:

* Репозитории GitHub с исходным кодом и ресурсы для микрослужб, формирующих предварительно настроенное решение.
* Общие сценарии настройки, такие как добавление нового типа устройств.

## <a name="project-overview"></a>Обзор проекта

### <a name="implementations"></a>Варианты реализации решения

Решение для удаленного мониторинга можно внедрить с помощью .NET и Java. Оба варианта реализации обеспечивают аналогичную функциональность и используют одни и те же базовые службы Azure. Репозитории GitHub верхнего уровня можно найти в этих статьях:

* [решение .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet);
* [решение Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

### <a name="microservices"></a>Микрослужбы

Вы можете получить доступ к репозиториям GitHub для каждой отдельной микрослужбы, если вас интересуют определенные параметры решения. Каждая микрослужба реализует различные части функциональных возможностей решения. Чтобы узнать больше об общей архитектуре, см. статью [Пошаговое руководство по работе с настроенным решением для удаленного мониторинга](iot-suite-remote-monitoring-sample-walkthrough.md).

В этой таблице представлены общие сведения о доступности микрослужб для каждого языка в настоящее время:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Микрослужба      | Описание | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Веб-интерфейс            | Веб-приложение для решения удаленного мониторинга. Реализация пользовательского интерфейса с использованием платформы React.js. | [Недоступно (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [Недоступно (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Диспетчер Центра Интернета вещей   | Взаимодействие с Центром Интернета вещей.        | [Доступно](https://github.com/Azure/iothub-manager-java) | [Доступно](https://github.com/Azure/iothub-manager-dotnet)   |
| Аутентификация    |  Управление интеграцией с Azure Active Directory.  | Пока недоступно | [Доступно](https://github.com/Azure/pcs-auth-dotnet)   |
| Виртуальное устройство | Управление пулом виртуальных устройств. | Пока недоступно | [Доступно](https://github.com/Azure/device-simulation-dotnet)   |
| Телеметрия         | Предоставление доступа к телеметрии устройства в пользовательском интерфейсе. | [Доступно](https://github.com/Azure/device-telemetry-java) | [Доступно](https://github.com/Azure/device-telemetry-dotnet)   |
| Агент телеметрии   | Анализ потока телеметрии, хранение сообщений из Центра Интернета вещей Azure и создание предупреждений в соответствии с определенными правилами.  | [Доступно](https://github.com/Azure/telemetry-agent-java) | [Доступно](https://github.com/Azure/telemetry-agent-dotnet)   |
| Настройка пользовательского интерфейса         | Управление данными конфигурации из пользовательского интерфейса. | [Доступно](https://github.com/azure/pcs-ui-config-java) | [Доступно](https://github.com/azure/pcs-ui-config-dotnet)   |
| Адаптер хранения   |  Управление взаимодействием со службой хранения.   | [Доступно](https://github.com/azure/pcs-storage-adapter-java) | [Доступно](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Обратный прокси-сервер.     | Предоставление частных ресурсов управляемым образом через уникальную конечную точку. | Пока недоступно | [Доступно](https://github.com/Azure/reverse-proxy-dotnet)   |

Сейчас решение Java использует аутентификацию, симуляцию и обратный прокси-сервер микрослужб .NET. Эти микрослужбы будут заменены версиями Java, как только они станут доступными.

## <a name="presentation-and-visualization"></a>Визуализация и представление

В следующих разделах описываются параметры настройки уровня представления и визуализации в решении для удаленного мониторинга:

### <a name="change-the-logo-in-the-ui"></a>Изменение логотипа в пользовательском интерфейсе

При развертывании по умолчанию в пользовательском интерфейсе используется название и логотип компании Contoso. Для отображения имени и логотипа вашей компании нужно изменить следующие элементы пользовательского интерфейса:

1. Используйте следующую команду для клонирования репозитория веб-интерфейса:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Для изменения имени компании откройте файл `src/common/lang.js` в текстовом редакторе.

1. Найдите в файле следующую строку:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Замените `Contoso` именем компании. Например:

    ```js
    CONTOSO: 'YourCo',
    ```

1. Сохраните файл.

1. Для обновления логотипа добавьте новый файл с расширением SVG в папку `assets/icons`. Файл `assets/icons/Contoso.svg` является имеющимся логотипом.

1. Откройте файл `src/components/layout/leftNav/leftNav.js` в текстовом редакторе.

1. Найдите в файле следующую строку:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Замените `Contoso.svg` именем своего файла логотипа. Например:

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Найдите в файле следующую строку:

    ```js
    alt="ContosoIcon"
    ```

1. Замените имя репозитория `ContosoIcon` своим альтернативным текстом (`alt`). Например:

    ```js
    alt="YourCoIcon"
    ```

1. Сохраните файл.

1. Для проверки изменений запустите обновленный `webui` на локальном компьютере. Сведения о создании и запуске решения `webui`решение локально см. в разделе о [локальной сборке, локальном выполнении и запуске](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) в файле Readme из репозитория GitHub `webui`.

1. Сведения о развертывании изменений см. в [справочнике разработчика](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Настройка карты

Дополнительные сведения о компонентах карты в решении см. на странице GitHub о [настройке карты](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Другие возможности настройки

Для дополнительного изменения уровня представления и визуализации в решении для удаленного мониторинга можно изменить код. Соответствующие репозитории GitHub:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/);
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/);
* [веб-интерфейс для удаленного мониторинга Azure PCS](https://github.com/Azure/pcs-remote-monitoring-webui).

## <a name="device-connectivity-and-streaming"></a>Подключение устройств и потоковая передача

В следующих разделах описываются параметры настройки уровня подключения устройств и уровня потоковой передачи в решении для удаленного мониторинга. [Модели устройств](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) описывают типы устройств и телеметрию в решениях. Модели устройств используются как для виртуальных, так и для физических устройств.

Пример реализации физического устройства см. в статье [Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Node.js)](iot-suite-connecting-devices-node.md).

Если вы используете _физическое устройство_, укажите для клиентского приложения модель устройства, которая содержит метаданные устройства и спецификацию телеметрии.

В следующих разделах рассматривается использование моделей устройств с виртуальными устройствами:

### <a name="add-a-telemetry-type"></a>Добавление типа данных телеметрии

Типы устройств в демо-решениях Contoso определяют телеметрию, которую отправляет каждое из устройств. Чтобы указать дополнительные типы телеметрии, устройство может отправлять определения телеметрии в виде метаданных в решение. При использовании этого формата панель мониторинга динамически потребляет телеметрию вашего устройства и доступные методы, поэтому вам не нужно изменять пользовательский интерфейс. Кроме того, вы можете изменить определение типа устройства в решении.

Сведения о добавлении пользовательской телеметрии в микрослужбу _Симулятор устройства_ см. в статье [Test your solution with simulated devices](iot-suite-remote-monitoring-test.md) (Проверка решений с помощью виртуальних устройств).

### <a name="add-a-device-type"></a>Добавление типа устройств

Демо-решение Contoso определяет некоторые образцы типов устройств. Решение позволяет определять пользовательские типы устройств в соответствии с вашими требованиями к конкретным приложениям. Например, ваша компания может использовать технический шлюз в качестве основного устройства, подключенного к решению.

Для создания точного представления о вашем устройстве необходимо изменить приложение, выполняемое на устройстве, в соответствии с требованиями устройства.

Сведения о добавлении нового типа устройства в микрослужбу _Симулятор устройства_ см. в статье [Test your solution with simulated devices](iot-suite-remote-monitoring-test.md) (Проверка решений с помощью виртуальных устройств).

### <a name="define-custom-methods-for-simulated-devices"></a>Определение пользовательских методов для виртуальных устройств

Дополнительные сведения о том, как определить настраиваемые методы для виртуальных устройств в решении для удаленного мониторинга, см. в статье [Get list of device models that can be simulated](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) (Получение списка устройств, пригодных для симуляции) в репозитории GitHub.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Использование физических устройств

Для реализации методов и заданий на физических устройствах см. следующие статьи о Центре Интернета вещей:

* [Общие сведения о прямых методах и информация о вызове этих методов из Центра Интернета вещей](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Планирование заданий на нескольких устройствах](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Другие возможности настройки

Для дополнительного изменения уровня подключения устройства и потокового уровня в решении для удаленного мониторинга можно изменить код. Соответствующие репозитории GitHub:

* [телеметрия устройства (.NET)](https://github.com/Azure/device-telemetry-dotnet);
* [телеметрия устройства (Java)](https://github.com/Azure/device-telemetry-java);
* [агент телеметрии (.NET)](https://github.com/Azure/telemetry-agent-dotnet);
* [агент телеметрии (Java)](https://github.com/Azure/telemetry-agent-java).

## <a name="data-processing-and-analytics"></a>Обработка данных и аналитика

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Для изменения уровня обработки и анализа данных в решении для удаленного мониторинга можно изменить код. Соответствующие репозитории GitHub:

* [агент телеметрии (.NET)](https://github.com/Azure/telemetry-agent-dotnet);
* [агент телеметрии (Java)](https://github.com/Azure/telemetry-agent-java).

## <a name="infrastructure"></a>Инфраструктура

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Для изменения инфраструктуры в решении удаленного мониторинга можно изменить код. Соответствующие репозитории GitHub:

* [диспетчер Центра Интернета вещей (.NET)](https://github.com/Azure/iothub-manager-dotnet);
* [Диспетчер Центра Интернета вещей (Java)](https://github.com/Azure/iothub-manager-java);
* [адаптер хранения (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet);
* [адаптер хранения (Java)](https://github.com/Azure/pcs-storage-adapter-java).

## <a name="next-steps"></a>Дальнейшие действия

Из статьи вы узнали о ресурсах, которые могут помочь вам при настройке предварительно настроенного решения.

Более подробные сведения о предварительно настроенном решении для удаленного мониторинга см. в статье [Пошаговое руководство по работе с настроенным решением для удаленного мониторинга](iot-suite-remote-monitoring-sample-walkthrough.md).

Дополнительные сведения о решении для удаленного мониторинга см. в следующих статьях:

* [Справочник разработчика](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Руководство по устранению неполадок для разработчиков](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->