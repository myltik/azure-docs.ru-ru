---
title: "Подключение устройства с помощью C в Windows | Документация Майкрософт"
description: "Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C в среде Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: d222bcbd64f288d4091acb0ecd2922b9ceee57e5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Создание примера решения на C в Windows
Описанные ниже действия показывают, как создать клиентское приложение, которое взаимодействует с предварительно настроенным решением для удаленного мониторинга. Это приложение на языке C, созданное и запущенное на устройстве Windows.

Создайте начальный проект в Visual Studio 2015 или Visual Studio 2017 и добавьте клиентские пакеты NuGet для устройства центра IoT:

1. В Visual Studio создайте консольное приложение C, используя шаблон **консольного приложения Win32** в Visual C++. Присвойте проекту имя **RMDevice**.
2. На странице **Параметры приложения** в **мастере приложений Win32** необходимо установить флажок **Консольное приложение** и снять флажки **Предкомпилированный заголовок** и **Жизненный цикл разработки безопасного ПО (SDL)**.
3. В **обозревателе решений**удалите файлы stdafx.h, targetver.h и stdafx.cpp.
4. В **обозревателе решений**переименуйте файл RMDevice.cpp в RMDevice.c.
5. В **обозревателе решений** щелкните проект **RMDevice** правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**. Щелкните **Обзор**, а затем найдите и установите следующие пакеты NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport.
6. В **обозревателе решений** щелкните правой кнопкой мыши проект **RMDevice**, а затем щелкните **Свойства**, чтобы открыть диалоговое окно **Страницы свойств** проекта. Дополнительные сведения см. в статье [Setting Visual C++ Project Properties][lnk-c-project-properties] (Задание свойств проекта Visual C++). 
7. Выберите папку **Компоновщик**, затем щелкните страницу свойств **Входные данные**.
8. В свойство **Дополнительные зависимости** добавьте **crypt32.lib**. Нажмите кнопку **ОК**, а затем еще раз **ОК**, чтобы сохранить значения свойств проекта.

Добавьте библиотеку JSON Parson в проект **RMDevice** и добавьте обязательные инструкции `#include`:

1. В подходящей папке на компьютере клонируйте репозиторий GitHub для Parson, используя следующую команду:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Скопируйте файлы parson.h и parson.c из локальной копии репозитория Parson в папку проекта **RMDevice**.

1. В Visual Studio щелкните правой кнопкой мыши проект **RMDevice**, а затем выберите **Добавить** > **Существующий элемент**.

1. В диалоговом окне **Добавление существующего элемента** выберите файлы parson.h и parson.c в папке проекта **RMDevice**. Нажмите кнопку **Добавить**, чтобы добавить эти файлы в проект.

1. В Visual Studio откройте файл RMDevice.c. Замените имеющиеся инструкции `#include` в этом окне следующим кодом.
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Теперь вы можете создать проект, чтобы убедиться, что в нем настроены правильные зависимости.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Добавьте код для вызова функции **remote\_monitoring\_run**, а затем создайте и запустите приложение устройства.

1. Замените функцию **main** следующим кодом для вызова функции **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Щелкните **Построить**, а затем **Построить решение**, чтобы выполнить сборку приложения устройства.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **RMDevice**, нажмите кнопку **Отладка**, а затем щелкните **Запустить новый экземпляр**, чтобы запустить пример. В консоли отображаются сообщения, когда приложение отправляет пример данных телеметрии в предварительно настроенное решение, получает наборы значений требуемых свойств в панели мониторинга решения и отвечает на методы, вызываемые из панели мониторинга решения.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
