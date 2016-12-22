---
title: "Приступая к работе с пакетом SDK для шлюза Центра Интернета вещей | Документация Майкрософт"
description: "Пошаговое руководство по работе с пакетом SDK для шлюза IoT Azure под управлением Windows иллюстрирует основные понятия, которые нужно учитывать при работе с пакетом SDK для шлюза IoT Azure."
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
ms.sourcegitcommit: 2d8b98fbd5345edd5dc6891df12f05eccd8e7ca8
ms.openlocfilehash: 6f2fe4fd3442d97955519348416b35fe6f9075d1


---
# <a name="azure-iot-gateway-sdk---get-started-using-windows"></a>Пакет SDK для шлюза Интернета вещей Azure. Начало работы с Windows
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Сборка примера
Перед началом работы необходимо [настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Windows.

1. Откройте **командную строку разработчика для VS2015** .
2. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk** .
3. Запустите сценарий **tools\\build.cmd**. Этот сценарий создает файл решения Visual Studio, собирает решение и проводит тесты. Решение Visual Studio находится в папке **build** локальной копии репозитория **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Запуск примера
1. Сценарий **build.cmd** создает папку с именем **build** в локальной копии репозитория. Эта папка содержит два модуля, которые используются в данном примере.
   
    Сценарий сборки размещает **logger.dll** в папке **build\\modules\\logger\\Debug** и **hello_world.dll** в папке **build\\modules\\hello_world\\Debug**. Используйте эти пути для настройки значения **module path**, как указано в приведенном ниже файле параметров JSON.
2. Процесс hello_world_sample принимает путь к JSON-файлу конфигурации как аргумент в командной строке. Следующий пример JSON-файла предоставлен как часть репозитория с путем **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json**. Он будет работать как есть, если вы не измените сценарий сборки для размещения модулей или примеров исполняемых файлов в нестандартных расположениях. 

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



<!--HONumber=Nov16_HO5-->


