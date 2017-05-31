---
title: "Начало работы с Edge Интернета вещей Azure (Linux) | Документация Майкрософт"
description: "Узнайте, как создать шлюз на компьютере Linux, а также ознакомьтесь с основными понятиями Edge Интернета вещей Azure, такими как модули и JSON-файлы конфигурации."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 38fd72fda6ea5d03d72c950803b09dd0b9e34fe4
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Приступая к работе с архитектурой Edge Интернета вещей в Linux

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Сборка примера

Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Linux.

1. Откройте оболочку.
1. Перейдите в корневую папку в локальной копии репозитория **iot-edge**.
1. Запустите сценарий **tools/build.sh** . В этом сценарии используется служебная программа **cmake**, которая создает папку **build** в корневой папке локальной копии репозитория **iot-edge** и генерирует файл makefile. Затем скрипт создает решение, пропуская модульные и сквозные тесты. Добавьте параметр **--run-unittests**, если требуется создавать и выполнять модульные тесты. Добавьте параметр **--run-e2e-tests**, если требуется создавать и выполнять сквозные тесты.

> [!NOTE]
> При каждом запуске скрипт **build.sh** удаляет и заново создает папку **build** в корневой папке локальной копии репозитория **iot-edge**.

## <a name="how-to-run-the-sample"></a>Запуск примера

1. Скрипт **build.sh** создает выходные данные в папке **build** локальной копии репозитория **iot-edge**. Выходные данные содержат два модуля Edge Интернета вещей, которые используются в данном примере.

    Скрипт сборки размещает **liblogger.so** в папке **build/modules/logger/**, а **libhello\_world.so** — в папке **build/modules/hello_world/**. Используйте эти пути для настройки значения **module path**, как указано в приведенном ниже примере файла параметров JSON.
1. Процесс hello\_world\_sample принимает путь к JSON-файлу конфигурации в качестве аргумента командной строки. Приведенный ниже пример JSON-файла размещен в репозитории пакетов SDK по пути **samples/hello\_world/src/hello\_world\_lin.json**. Этот файл конфигурации будет работать как есть, если вы не измените скрипт сборки для размещения модулей Edge Интернета вещей или примеров исполняемых файлов в нестандартных расположениях.

   > [!NOTE]
   > Пути к модулям зависят от текущего рабочего каталога, в котором запускается исполняемый файл hello\_world\_sample, а не от каталога, где находится исполняемый файл. По умолчанию пример JSON-файла конфигурации предусматривает запись файла log.txt в текущем рабочем каталоге.

    ```json
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links":
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
1. Перейдите к папке **build**.
1. Выполните следующую команду:

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

