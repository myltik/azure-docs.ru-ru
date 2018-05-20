---
title: Подготовка устройства Raspberry Pi к удаленному мониторингу с помощью C в Azure | Документация Майкрософт
description: В статье описывается, как подключить устройство Raspberry Pi к акселератору решения для удаленного мониторинга с помощью приложения на С.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9de7616ec7174f6c55888a659e9a12bca1e07f94
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Подключение устройства Raspberry Pi к акселератору решения для удаленного мониторинга с помощью С

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить физическое устройство к акселератору решений для удаленного мониторинга. Как и для большинства внедряемых приложений, работающих на устройствах с ограниченными ресурсами, код клиента для приложения на устройстве Raspberry Pi пишется на языке C. В этом руководстве вы создадите приложение на устройстве Raspberry Pi под управлением ОС Raspbian.

### <a name="required-hardware"></a>Необходимое оборудование

Настольный компьютер, чтобы иметь удаленный доступ к командной строке Raspberry Pi.

[Начальный набор Microsoft IoT для Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) или эквивалентные компоненты. В этом руководстве используются следующие компоненты из набора:

- Raspberry Pi 3;
- карта MicroSD (с NOOBS);
- кабель MiniUSB;
- кабель Ethernet;

### <a name="required-desktop-software"></a>Необходимое ПО для настольного компьютера

На настольном компьютере необходимо установить клиент SSH, чтобы иметь удаленный доступ к командной строке Raspberry Pi.

- Windows не предоставляет клиент SSH. Мы советуем использовать [PuTTY](http://www.putty.org/).
- Большинство дистрибутивов Linux и Mac OS содержат служебную программу командной строки SSH. Дополнительные сведения см. в статье [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Подключение по протоколу SSH с помощью Linux или Mac OS).

### <a name="required-raspberry-pi-software"></a>Необходимое ПО для Raspberry Pi

В этой статье предполагается, что вы установили последнюю версию [Raspbian ОС на устройстве Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

В следующих шагах объясняется, как подготовить устройство Raspberry Pi для создания приложения C, которое подключается к акселератору решения.

1. Подключитесь к Raspberry Pi с помощью **ssh**. Дополнительные сведения см. в разделе о [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) на [веб-сайте Raspberry Pi](https://www.raspberrypi.org/).

1. Чтобы обновить устройство Raspberry Pi, используйте следующую команду:

    ```sh
    sudo apt-get update
    ```

1. Чтобы добавить необходимые средства разработки и библиотеки на устройство Raspberry Pi, используйте следующую команду:

    ```sh
    sudo apt-get purge libssl-dev
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Используйте следующие команды для скачивания, сборки и установки клиентских библиотек Центра Интернета вещей на устройстве Raspberry Pi:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c/build_all/linux
    ./build.sh --no-make
    cd ../../cmake/iotsdk_linux
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Создание проекта

Выполните следующие шаги, установив подключение по протоколу **ssh** к устройству Raspberry Pi:

1. Создайте папку с именем `remote_monitoring` в домашней папке на Raspberry Pi. Перейдите к этой папке в оболочке:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Создайте четыре файла: **main.c**, **remote_monitoring.c**, **remote_monitoring.h** и **CMakeLists.txt** в папке `remote_monitoring`.

1. Откройте в текстовом редакторе файл **remote_monitoring.c**. На Raspberry Pi можно использовать текстовый редактор **nano** или **vi**. Добавьте следующие операторы `#include` :

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

Сохраните файл **remote_monitoring.c** и закройте редактор.

## <a name="add-code-to-run-the-app"></a>Добавление кода для запуска приложения

Откройте в текстовом редакторе файл **remote_monitoring.h**. Добавьте следующий код:

```c
void remote_monitoring_run(void);
```

Сохраните файл **remote_monitoring.h** и закройте редактор.

Откройте в текстовом редакторе файл **main.c** . Добавьте следующий код:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

Сохраните файл **main.c** и закройте редактор.

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
      serializer
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. Сохраните файл **CMakeLists.txt** и закройте редактор.

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
