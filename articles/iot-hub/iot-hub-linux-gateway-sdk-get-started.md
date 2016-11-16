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
ms.date: 08/25/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23176a9251a90a985a5d2fbce23ceeb9d0925234


---
# <a name="azure-iot-gateway-sdk-beta-get-started-using-linux"></a>Пакет SDK для шлюза IoT (бета-версия): приступая к работе с Linux
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
   
    Скрипт сборки размещает **liblogger_hl.so** в папке **build/modules/logger/** и **libhello_world_hl.so** в папке **build/modules/hello_world/**. Используйте эти пути для настройки значения **module path** , как указано в приведенном ниже файле параметров JSON.
2. Файл **hello_world_lin.json** в папке **samples/hello_world/src** — это пример файла параметров JSON, который можно использовать для запуска примера. В представленном ниже примере параметров JSON предполагается, что пример запускается из корневой папки локальной копии репозитория **azure-iot-gateway-sdk** .
3. Для модуля **logger_hl** в разделе **args** укажите в качестве значения **filename** имя и путь файла, который будет содержать данные журнала.
   
    Вот пример параметров JSON для Linux, который записывается в файл **log.txt** в папке, из которой запускается этот пример.
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "loading args": {
            "module path" : "./build/modules/logger/liblogger_hl.so"
          },
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "loading args": {
            "module path" : "./build/modules/hello_world/libhello_world_hl.so"
          },
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. Перейдите к папке **azure-iot-gateway-sdk** в оболочке.
5. Выполните следующую команду:
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO2-->


