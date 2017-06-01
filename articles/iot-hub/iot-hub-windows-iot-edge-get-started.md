---
title: "Приступая к работе с Edge Интернета вещей Azure (Windows) | Документация Майкрософт"
description: "Узнайте, как создать шлюз Edge Интернета вещей Azure на компьютере Windows, а также ознакомьтесь с основными понятиями Edge Интернета вещей Azure, такими как модули и JSON-файлы конфигурации."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
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
ms.openlocfilehash: 117b949d4f8b84fe5ce43fc1dc38e3326bcfcfba
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Приступая к работе с архитектурой Edge Интернета вещей в Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Сборка примера

Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Windows.

1. Откройте командную строку разработчика для **VS 2015** или **VS 2017**.
1. Перейдите в корневую папку в локальной копии репозитория **iot-edge**.
1. Запустите сценарий **tools\\build.cmd**. Этот сценарий создает файл решения Visual Studio выполняет сборку решения. Решение Visual Studio находится в папке **build** локальной копии репозитория **iot-edge**. В сценарии можно задать дополнительные параметры для выполнения сборки и модульного или сквозного тестирования. Это параметры **--run-unittests** и **--run-e2e-tests** соответственно.

## <a name="how-to-run-the-sample"></a>Запуск примера

1. Сценарий **build.cmd** создает папку с именем **build** в локальной копии репозитория. Эта папка содержит два модуля Edge Интернета вещей, которые используются в этом примере.

    Сценарий сборки размещает **logger.dll** в папках **build\\modules\\logger\\Debug** и **hello\_world.dll** in the **build\\modules\\hello_world\\Debug**. Используйте эти пути для настройки значений **module path**, как указано в приведенном ниже файле параметров JSON.
1. Процесс hello\_world\_sample принимает путь к JSON-файлу конфигурации в качестве аргумента командной строки. Приведенный ниже пример JSON-файла размещен в репозитории пакетов SDK по пути **samples\\hello\_world\\src\\hello\_world\_win.json**. Этот файл конфигурации будет работать как есть, если вы не измените скрипт сборки для размещения модулей или примеров исполняемых файлов в нестандартных расположениях.

   > [!NOTE]
   > Пути к модулям зависят от каталога, в котором находится файл hello\_world\_sample.exe. По умолчанию пример JSON-файла конфигурации предусматривает запись файла log.txt в текущем рабочем каталоге.

    ```json
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```

1. Перейдите в корневую папку в локальной копии репозитория **iot-edge**.

1. Выполните следующую команду:

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

