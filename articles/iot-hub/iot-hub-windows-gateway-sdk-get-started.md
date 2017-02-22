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
ms.date: 11/16/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: e1cf5ed3f2434a9e98027afd0225207ad5d2f1b1
ms.openlocfilehash: 3d9997655e19ba800bf3462d5ebd3f7c7210271f


---
# <a name="get-started-with-the-azure-iot-gateway-sdk-windows"></a>Начало работы с пакетом SDK для шлюза Azure IoT (Windows)
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Сборка примера
Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Windows.

1. Откройте **командную строку разработчика для VS2015** .
2. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk** .
3. Запустите сценарий **tools\\build.cmd**. Этот сценарий создает файл решения Visual Studio выполняет сборку решения. Решение Visual Studio находится в папке **build** локальной копии репозитория **azure-iot-gateway-sdk**. В сценарии можно задать дополнительные параметры для выполнения сборки и модульного или сквозного тестирования. Это параметры **--run-unittests** и **--run-e2e-tests** соответственно. 

## <a name="how-to-run-the-sample"></a>Запуск примера
1. Сценарий **build.cmd** создает папку с именем **build** в локальной копии репозитория. Эта папка содержит два модуля, которые используются в данном примере.
   
    Сценарий сборки размещает **logger.dll** в папке **build\\modules\\logger\\Debug** и **hello_world.dll** в папке **build\\modules\\hello_world\\Debug**. Используйте эти пути для настройки значения **module path**, как указано в приведенном ниже файле параметров JSON.
2. Процесс hello_world_sample принимает путь к JSON-файлу конфигурации в качестве аргумента в командной строке. Следующий пример JSON-файла предоставлен как часть репозитория с путем **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json**. Он будет работать как есть, если вы не измените сценарий сборки для размещения модулей или примеров исполняемых файлов в нестандартных расположениях. 

   > [!NOTE]
   > Пути к модулям зависят от каталога, в котором находится файл hello_world_sample.exe. По умолчанию пример JSON-файла конфигурации предусматривает запись файла log.txt в текущем рабочем каталоге.
   
    ```
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
3. Перейдите в корневую папку локальной копии репозитория **azure-iot-gateway-sdk**.

4. Выполните следующую команду:
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Jan17_HO3-->


