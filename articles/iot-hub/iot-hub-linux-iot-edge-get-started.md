---
title: "Начало работы с Azure IoT Edge (Linux) | Документация Майкрософт"
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
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: b02d79fcd9cd2a2ef0041aac4e85528263c8d58a
ms.contentlocale: ru-ru
ms.lasthandoff: 06/09/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Приступая к работе с архитектурой Edge Интернета вещей в Linux

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Запуск примера

Скрипт **build.sh** создает выходные данные в папке **build** локальной копии репозитория **iot-edge**. Выходные данные содержат два модуля Edge Интернета вещей, которые используются в данном примере.

Скрипт сборки размещает **liblogger.so** в папке **build/modules/logger/**, а **libhello\_world.so** — в папке **build/modules/hello_world/**. Используйте эти пути для настройки значения **module path**, как указано в приведенном ниже примере файла параметров JSON.

Процесс hello\_world\_sample принимает путь к JSON-файлу конфигурации в качестве аргумента командной строки. Приведенный ниже пример JSON-файла размещен в репозитории пакетов SDK по пути **samples/hello\_world/src/hello\_world\_lin.json**. Этот файл конфигурации работает так, как если бы вы не модифицировали сценарий сборки, чтобы разместить модули IoT Edge или образцы исполняемых файлов в местах, отличных от настроек по умолчанию.

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

1. Перейдите в папку **build** в корневой папке вашей локальной копии репозитория **iot-edge**.

1. Выполните следующую команду:

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json`
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]


