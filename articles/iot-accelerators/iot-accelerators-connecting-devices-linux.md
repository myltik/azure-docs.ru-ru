---
title: Подготовка устройств Linux к удаленному мониторингу с помощью C в Azure | Документация Майкрософт
description: В статье описывается, как подключить устройство к предварительно настроенному акселератору решения для удаленного мониторинга с помощью приложения на C в среде Linux.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9ccf2b185c5aa67b41aab650989ad0326aba0215
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Подключение устройства к акселератору решения для удаленного мониторинга в Linux

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить физическое устройство к акселератору решений для удаленного мониторинга.

## <a name="create-a-c-client-project-on-linux"></a>Создание клиентского проекта C в Linux

Как и для большинства внедряемых приложений, работающих на устройствах с ограниченными ресурсами, клиентский код для приложения на устройстве пишется на языке C. В этом руководстве вы создадите приложение на компьютере под управлением Ubuntu (Linux).

Для выполнения этих действий необходимо устройство под управлением Ubuntu версии 15.04 или более поздней версии. Прежде чем продолжить, установите необходимые пакеты на устройство Ubuntu, используя следующую команду.

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Установка клиентских библиотек на устройство

Клиентские библиотеки Центра Интернета вещей Azure доступны в виде пакета, который можно установить на устройство Ubuntu с помощью команды **apt-get**. Выполните следующие действия, чтобы установить пакет, содержащий файлы клиентских библиотек Центра Интернета вещей и заголовков, на компьютер Ubuntu.

1. В оболочке добавьте репозиторий azureiot на компьютер:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Установите пакет azure-iot-sdk-c-dev.

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Установка средства синтаксического анализа JSON Parson

Клиентские библиотеки Центра Интернета вещей используют средство синтаксического анализа JSON Parson для анализа полезных данных сообщения. В подходящей папке на компьютере клонируйте репозиторий GitHub для Parson, используя следующую команду:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Подготовка проекта

На компьютере Ubuntu создайте папку с именем `remote_monitoring`. В папке `remote_monitoring`:

- Создайте четыре файла: `main.c`, `remote_monitoring.c`, `remote_monitoring.h` и `CMakeLists.txt`.
- Создайте папку с именем `parson`.

Скопируйте файлы `parson.c` и `parson.h` из локальной копии репозитория Parson в папку `remote_monitoring/parson`.

Откройте файл `remote_monitoring.c` в текстовом редакторе. Добавьте следующие операторы `#include` :

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

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Добавление кода для запуска приложения

Откройте файл `remote_monitoring.h` в текстовом редакторе. Добавьте следующий код:

```c
void remote_monitoring_run(void);
```

Откройте файл `main.c` в текстовом редакторе. Добавьте следующий код:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Ниже приведены указания по использованию *CMake* для создания клиентского приложения.

1. Откройте в текстовом редакторе файл **CMakeLists.txt** из папки `remote_monitoring`.

1. Добавьте следующие операторы, чтобы определить способ сборки клиентского приложения.

    ```cmake
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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

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

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

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

1. В папке `remote_monitoring` создайте папку для хранения файлов *make*, созданных с помощью CMake. Затем запустите команды **cmake** и **make** следующим образом:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Запустите клиентское приложение и отправьте данные телеметрии в Центр Интернета вещей.

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
