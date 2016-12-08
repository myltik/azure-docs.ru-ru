---
title: "Приступая к работе с пакетом SDK для шлюза Центра Интернета вещей | Документация Майкрософт"
description: "В этом руководстве по работе с пакетом SDK для шлюза Центра Интернета вещей Azure под управлением Linux рассматриваются основные понятия, которые нужно знать при работе с этим пакетом SDK."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: a76320718f0cefa015728cb79df944e0d34bbf74
ms.openlocfilehash: cbb909adc2d29f9b80a4c97d06176fe74b64a75a


---
# <a name="azure-iot-gateway-sdk---get-started-using-linux"></a>Пакет SDK для шлюза Интернета вещей: начало работы в ОС Linux
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Сборка примера
Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Linux.

1. Откройте оболочку.
2. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk** .
3. Запустите сценарий **tools/build.sh** . В этом сценарии используется служебная программа **cmake**, которая создает папку **build** в корневой папке локальной копии репозитория **azure-iot-gateway-sdk** и генерирует файл makefile. Затем сценарий собирает решение и проводит тесты.

> [!NOTE]
> При каждом запуске сценарий **build.sh** удаляет и заново создает папку **build** в корневой папке локальной копии репозитория **azure-iot-gateway-sdk**.
> 
> 

## <a name="how-to-run-the-sample"></a>Запуск примера
1. Скрипт **build.sh** создает выходные данные в папке **build** локальной копии репозитория **azure-iot-gateway-sdk**. Он включает два модуля, которые используются в данном примере.
   
    Скрипт сборки размещает **liblogger.so** в папке **build/modules/logger/**, а **libhello_world.so** — в папке **build/modules/hello_world/**. Используйте эти пути для настройки значения **module path** , как указано в приведенном ниже файле параметров JSON.
2. Процесс hello_world_sample принимает путь к JSON-файлу конфигурации как аргумент в командной строке. Пример JSON-файла предоставлен как часть репозитория с путем **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** и скопирован ниже. Он будет работать как есть, если вы не измените скрипт сборки для размещения модулей или примеров исполняемых файлов в нестандартных расположениях.

   > [!NOTE]
   > Пути к модулям зависят от текущего рабочего каталога, в котором запускается исполняемый файл hello_world_sample, а не от каталога, где находится исполняемый файл. По умолчанию пример JSON-файла конфигурации предусматривает запись файла log.txt в текущем рабочем каталоге.
   
    ```
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
3. Перейдите к папке **azure-iot-gateway-sdk**.
4. Выполните следующую команду:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO4-->


