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
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Приступая к работе с архитектурой Edge Интернета вещей в Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Запуск примера

Скрипт **build.cmd** создает выходные данные в папке **build** локальной копии репозитория **iot-edge**. Эти выходные данные содержат большое количество файлов, но в данном примере акцент сделан на трех:
- два модуля IoT Edge: **build\\modules\\logger\\Debug\\logger.dll** и **build\\modules\\hello_world\\Debug\\hello\_world.dll**; 
- исполняемый файл: **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**. Этот процесс принимает файл конфигурации JSON в качестве аргумента командной строки.

Четвертый файл, используемый в этом примере, не находится в папке сборки, но был включен при клонировании репозитория iot-edge:
- Файл конфигурации JSON: **samples\\hello\_world\\src\\hello\_world\_win.json**. Этот файл содержит пути к двум модулям. Он также объявляет расположение, в которое logger.dll записывает выходные данные. Значение по умолчанию — **log.txt** в текущем рабочем каталоге. 

   >[!NOTE]
   >При перемещении примеров модулей или добавлении собственных модулей для тестирования, обновите значения **module.path** в файле конфигурации для сопоставления. Пути к модулям зависят от каталога, в котором находится файл **hello\_world\_sample.exe**. 

Чтобы запустить пример приложения, выполните следующее.

1. Перейдите в папку **build** в корневой папке вашей локальной копии репозитория **iot-edge**.

1. Выполните следующую команду:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. Следующие выходные данные означают, что пример был запущен успешно:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Нажмите клавишу **ВВОД** для остановки процесса.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
