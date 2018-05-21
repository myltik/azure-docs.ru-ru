---
title: Подготовка устройств Windows к удаленному мониторингу с помощью C в Azure | Документация Майкрософт
description: В статье описывается, как подключить устройство к предварительно настроенному акселератору решения для удаленного мониторинга с помощью приложения на C в среде Windows.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 51ed87fb0f0c6d526249adaae95d87afaafd8812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Подключение устройства к акселератору решения для удаленного мониторинга в Windows

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить физическое устройство к акселератору решений для удаленного мониторинга.

## <a name="create-a-c-client-solution-on-windows"></a>Создание клиентского решения на C в Windows

Как и для большинства внедряемых приложений, работающих на устройствах с ограниченными ресурсами, клиентский код для приложения на устройстве пишется на языке C. В этом руководстве вы создадите приложение на компьютере под управлением Windows.

### <a name="create-the-starter-project"></a>Создание начального проекта

Создайте начальный проект в Visual Studio 2017 и добавьте клиентские пакеты NuGet для устройства Центра Интернета вещей:

1. В Visual Studio создайте консольное приложение C, используя шаблон **консольного приложения Windows** в Visual C++. Присвойте проекту имя **RMDevice**.

    ![Создание консольного приложения Windows в Visual C++](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. В **обозревателе решений** удалите файлы `stdafx.h`, `targetver.h` и `stdafx.cpp`.

1. В **обозревателе решений** переименуйте файл `RMDevice.cpp` в `RMDevice.c`.

    ![Переименованный файл RMDevice.c в обозревателе решений](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. В **обозревателе решений** щелкните проект **RMDevice** правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**. Щелкните **Обзор**, а затем найдите и установите следующие пакеты NuGet:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport.

    ![Диспетчер пакетов NuGet с установленными пакетами Microsoft.Azure.IoTHub](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. В **обозревателе решений** щелкните проект **RMDevice** правой кнопкой мыши, а затем выберите пункт **Свойства**, чтобы открыть диалоговое окно **Страницы свойств** проекта. Дополнительные сведения см. в статье, посвященной [настройке свойств проекта Visual C++](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Выберите папку **C/C++** и щелкните страницу свойств **Предварительно скомпилированные заголовки**.

1. Задайте для параметра **Предварительно скомпилированные заголовки** значение **Не использовать предварительно скомпилированные заголовки**. Нажмите кнопку **Применить**.

    ![Страница свойств проекта, на которой показано, что в проекте не используются предварительно скомпилированные заголовки](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Выберите папку **Компоновщик** и щелкните страницу свойств **Входные данные**.

1. В свойство **Дополнительные зависимости** добавьте `crypt32.lib`. Чтобы сохранить значения свойств проекта, нажмите кнопку **ОК**, а затем еще раз **ОК**.

    ![Страница свойств проекта с выбранной папкой "Компоновщик" и добавленным файлом crypt32.lib](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Добавление библиотеки JSON Parson

Добавьте библиотеку JSON Parson в проект **RMDevice** и добавьте обязательные инструкции `#include`:

1. В подходящей папке на компьютере клонируйте репозиторий GitHub для Parson, используя следующую команду:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Скопируйте файлы `parson.h` и `parson.c` из локальной копии репозитория Parson в папку проекта **RMDevice**.

1. В Visual Studio щелкните проект **RMDevice** правой кнопкой мыши, а затем последовательно выберите **Добавить** и **Существующий элемент**.

1. В диалоговом окне **Добавление существующего элемента** выберите файлы `parson.h` и `parson.c` в папке проекта **RMDevice**. Нажмите кнопку **Добавить**, чтобы добавить эти файлы в проект.

    ![Файлы parson.h и parson.c в обозревателе решений](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. В Visual Studio откройте файл `RMDevice.c`. Замените имеющиеся инструкции `#include` в этом окне следующим кодом.

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > Теперь вы можете выполнить сборку решения, чтобы убедиться, что в проекте настроены правильные зависимости.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Добавьте код для вызова функции **remote\_monitoring\_run**, а затем создайте и запустите приложение устройства.

1. Чтобы вызвать функцию **remote\_monitoring\_run**, замените функцию **main** следующим кодом:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Щелкните **Сборка**, а затем **Собрать решение**, чтобы выполнить сборку приложения устройства.

1. В **обозревателе решений** щелкните проект **RMDevice** правой кнопкой мыши, выберите пункт **Отладка**, а затем щелкните **Запустить новый экземпляр**, чтобы запустить пример. В консоли появляются сообщения в следующих случаях:

    * приложение отправляет пример данных телеметрии в акселератор решения;
    * приложение получает требуемые значения свойств, заданные на панели мониторинга решения;
    * приложение отвечает на методы, вызываемые из панели мониторинга решения.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
