---
title: "Подключение устройства с помощью C в Linux | Документация Майкрософт"
description: "Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C в среде Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c844f9a84878f12fbdf7a5b4eaf7bbf19f4ce666
ms.openlocfilehash: 4a1615c4bea8c54d506c3252e2de42642bb55e46
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Linux)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Сборка и запуск примера клиента Linux на C
Описанные ниже действия показывают, как создать клиентское приложение, которое взаимодействует с предварительно настроенным решением для удаленного мониторинга. Это приложение на языке C, созданное и запущенное на устройстве Ubuntu Linux.

Для выполнения этих действий необходимо устройство под управлением Ubuntu версии 15.04 или 15.10. Прежде чем продолжить, установите необходимые пакеты на устройство Ubuntu, используя следующую команду.

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Установка клиентских библиотек на устройство
Клиентские библиотеки центра IoT Azure доступны в виде пакета, который можно установить на устройство Ubuntu с помощью команды **apt-get** . Выполните следующие действия, чтобы установить пакет, содержащий файлы клиентских библиотек Центра Интернета вещей и заголовков, на компьютер Ubuntu.

1. В оболочке добавьте репозиторий azureiot на компьютер:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Установите пакет azure-iot-sdk-c-dev.
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Установка средства синтаксического анализа JSON Parson
Клиентские библиотеки Центра Интернета вещей используют средство синтаксического анализа JSON Parson для анализа полезных данных сообщения. В подходящей папке на компьютере клонируйте репозиторий GitHub для Parson, используя следующую команду:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Подготовка проекта
На компьютере Ubuntu создайте папку **remote\_monitoring**. В папке **remote\_monitoring**:

- Создайте четыре файла: **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h** и **CMakeLists.txt**.
- Создайте папку с именем **parson**.

Скопируйте файлы **parson.c** и **parson.h** из локальной копии репозитория Parson в папку **remote\_monitoring/parson**.

Откройте в текстовом редакторе файл **remote\_monitoring.c**. Добавьте следующие операторы `#include` :
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Вызов функции remote\_monitoring\_run
Откройте в текстовом редакторе файл **remote_monitoring.h**. Добавьте следующий код:

```
void remote_monitoring_run(void);
```

Откройте в текстовом редакторе файл **main.c** . Добавьте следующий код:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Создание и запуск приложения
Ниже приведены указания по использованию *CMake* для создания клиентского приложения.

1. Откройте в текстовом редакторе файл **CMakeLists.txt** из папки **remote_monitoring**.

1. Добавьте следующие операторы, чтобы определить способ сборки клиентского приложения.
   
    ```
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER ".." "../parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h
    _files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```
1. В папке **remote_monitoring** создайте папку для хранения файлов *make*, которые создает CMake, а затем выполните команды **cmake** и **make**, как показано ниже.
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Запустите клиентское приложение и отправьте данные телеметрии в центр IoT.
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


