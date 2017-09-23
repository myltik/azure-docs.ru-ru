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
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 5db39bab8e31a8e7026b34e72b4614b0f6f57772
ms.contentlocale: ru-ru
ms.lasthandoff: 06/09/2017

---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Приступая к работе с архитектурой Edge Интернета вещей в Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>Запуск примера

Скрипт **build.cmd** создает выходные данные в папке **build** локальной копии репозитория **iot-edge**. Выходные данные содержат два модуля Edge Интернета вещей, которые используются в данном примере.

Сценарий сборки размещает **logger.dll** в папках **build\\modules\\logger\\Debug** и **hello\_world.dll** in the **build\\modules\\hello_world\\Debug**. Используйте эти пути для настройки значений **module path**, как указано в приведенном ниже файле параметров JSON.

Процесс hello\_world\_sample принимает путь к JSON-файлу конфигурации в качестве аргумента командной строки. Приведенный ниже пример JSON-файла размещен в репозитории пакетов SDK по пути **samples\\hello\_world\\src\\hello\_world\_win.json**. Этот файл конфигурации работает так, как если бы вы не модифицировали сценарий сборки, чтобы разместить модули IoT Edge или образцы исполняемых файлов в местах, отличных от настроек по умолчанию.

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

1. Перейдите в папку **build** в корневой папке вашей локальной копии репозитория **iot-edge**.

1. Выполните следующую команду:

    ```cmd
    samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

