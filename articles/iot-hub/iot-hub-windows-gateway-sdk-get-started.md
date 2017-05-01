---
title: "Начало работы с пакетом SDK для шлюза Интернета вещей Azure (Windows) | Документация Майкрософт"
description: "Узнайте, как создать шлюз на компьютере c Windows, а также ознакомьтесь с основными понятиями пакета SDK для шлюза Azure IoT, такими как модули и JSON-файлы конфигурации."
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
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: f3cc32daac5059e816c885c88f4a7d36b6fc897e
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-windows"></a>Приступая к работе с пакетом SDK для шлюза Azure IoT (Windows)

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Сборка примера

Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Windows.

1. Откройте командную строку разработчика для **VS 2015** или **VS 2017**.
1. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk** .
1. Запустите сценарий **tools\\build.cmd**. Этот сценарий создает файл решения Visual Studio выполняет сборку решения. Решение Visual Studio находится в папке **build** локальной копии репозитория **azure-iot-gateway-sdk**. В сценарии можно задать дополнительные параметры для выполнения сборки и модульного или сквозного тестирования. Это параметры **--run-unittests** и **--run-e2e-tests** соответственно.

## <a name="how-to-run-the-sample"></a>Запуск примера

1. Сценарий **build.cmd** создает папку с именем **build** в локальной копии репозитория. Эта папка содержит два модуля, которые используются в данном примере.

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

1. Перейдите в корневую папку локальной копии репозитория **azure-iot-gateway-sdk**.

1. Выполните следующую команду:

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

