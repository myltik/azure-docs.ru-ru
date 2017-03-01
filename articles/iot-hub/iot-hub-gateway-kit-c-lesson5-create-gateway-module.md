---
title: "Создание первого модуля шлюза Azure IoT | Документация Майкрософт"
description: "Создайте модуль и добавьте его в пример приложения для настройки поведения модуля."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 6b12d39ce8c6062df5fb78de654bf22183b6ecd9
ms.openlocfilehash: 42c22192bc3cccdea97f7d8dac575fa7cbc9a6c6
ms.lasthandoff: 02/21/2017


---
# <a name="lesson-5-create-your-first-azure-iot-gateway-module"></a>Урок 5. Создание первого модуля шлюза Azure IoT

## <a name="what-you-will-do"></a>Выполняемая задача

- Компиляция и запуск примера приложения hello_world на устройстве Intel NUC.
- Создание модуля и его компиляция на Intel NUC.
- Добавление нового модуля в пример приложения hello_world и запуск примера на Intel NUC. Новый модуль выводит на экран сообщение "hello_world" с меткой времени.

## <a name="what-you-will-learn"></a>Новые знания

- Как скомпилировать и запустить пример приложения на Intel NUC.
- Как создать модуль.
- Как добавить модуль в пример приложения.

## <a name="what-you-need"></a>Необходимые элементы

Пакет SDK для шлюза Azure IoT, установленный на главном компьютере.

## <a name="folder-structure"></a>Структура папок

В уроке 5 вложенная папка с примером кода, который вы клонировали в уроке 1, содержит папку `module` и папку `sample`.

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- Папка `module/my_module` содержит исходный код и скрипт для создания модуля.
- Папка `sample` содержит исходный код и скрипт для создания примера приложения.

## <a name="compile-and-run-the-helloworld-sample-app-on-intel-nuc"></a>Компиляция и запуск примера приложения hello_world на Intel NUC

Пример `hello_world` создает шлюз на основе файла `hello_world.json`, который задает два предопределенных модуля, связанных с приложением. Шлюз записывает сообщение "hello world" в файл каждые 5 секунд. В этом разделе вы скомпилируете и запустите приложение `hello_world` с его модулем по умолчанию.

Чтобы скомпилировать и запустить приложение `hello_world`, на главном компьютере выполните следующие действия.

1. Инициализируйте файлы конфигурации, выполнив приведенные ниже команды.

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. Добавьте в файл конфигурации шлюза MAC-адрес устройства Intel NUC. Пропустите этот шаг, если вы прошли урок по [настройке и запуску примера приложения BLE][config_ble].

   1. Откройте файл конфигурации шлюза, выполнив следующую команду:

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. Обновите MAC-адрес шлюза во время [настройки Intel NUC в качестве шлюза IoT][setup_nuc], а затем сохраните файл.

1. Скомпилируйте пример исходного кода, выполнив следующую команду:

   ```bash
   gulp compile
   ```

   Команда передает пример исходного кода на Intel NUC и запускает `build.sh` для его компиляции.

1. Запустите приложение `hello_world` на Intel NUC, выполнив следующую команду:

   ```bash
   gulp run
   ```

   Команда запускает `../Tools/run-hello-world.js`, указанный в `config.json`, для запуска примера приложения на Intel NUC.

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

## <a name="create-a-new-module-and-compile-it-on-intel-nuc"></a>Создание нового модуля и его компиляция на Intel NUC

С помощью приведенных ниже действий вы сможете создать новый модуль и скомпилировать его на устройстве Intel NUC. При получении сообщений модуль выводит их на экран с меткой времени. В этом разделе вы создадите первый настроенный модуль шлюза.

Любой модуль SDK для шлюза Azure IoT должен реализовать следующие интерфейсы:

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

При необходимости можно реализовать следующий интерфейс:

   ```C
   pfModule_Start Module_Start
   ```

На схеме ниже показаны переходы между основными состояниями модуля. Прямоугольниками обозначены методы, с помощью которых выполняются операции, когда модуль перемещается между состояниями. Овалами показаны основные состояния, в которых может находиться модуль.

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

Теперь создадим модуль на основе шаблона:

1. Откройте папку шаблонов, выполнив следующую команду:

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` служит в качестве шаблона, который упрощает создание модуля. Шаблон объявляет интерфейсы. Вам остается только добавить логику в функцию `MyModule_Receive`.
   - `build.sh` — это скрипт сборки для компиляции модуля на устройстве Intel NUC.
1. Откройте файл `src/my_module.c` и включите два файла заголовков:

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. Добавьте в функцию `MyModule_Receive` следующий код:

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. Укажите в файле `config.json` папку `workspace` на главном компьютере и путь развертывания на устройстве Intel NUC. Во время компиляции файлы в папке `workspace` будут переданы в путь развертывания.

   1. Откройте файл `config.json`, выполнив следующую команду:

      ```bash
      code config.json
      ```

   1. Дополните `config.json` следующей конфигурацией:

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. Скомпилируйте модуль, выполнив следующую команду:

   ```bash
   gulp compile
   ```

   Команда передает исходный код на устройство Intel NUC и выполняет `build.sh` для компиляции модуля.

## <a name="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc"></a>Добавление модуля в пример приложения hello_world и запуск приложения на Intel NUC

Для этого выполните следующие действия.

1. Выведите список всех доступных двоичных файлов модуля (SO-файлов) на устройстве Intel NUC, выполнив следующую команду:

   ```bash
   gulp modules --list
   ```

   Укажите путь к двоичным файлам скомпилированного кода `my_module`, как показано ниже:

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   Если изменить стандартное имя пользователя для входа в `config-gateway.json`, путь к двоичным файлам будет начинаться с `home/<your username>`, а не `root`.

1. Добавьте `my_module` в пример приложения `hello_world`:

   1. Откройте файл `hello_world.json`, выполнив следующую команду:

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. Добавьте в раздел `modules` следующий код:

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      Значение `module.path` должно быть следующим: `/root/gateway_sample/module/my_module/build/libmy_module.so`. Код объявляет `my_module` для связывания со шлюзом, а также расположение двоичного файла модуля, указанного в `module.path`.
   1. Добавьте в раздел `links` следующий код:

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      Этот код указывает, что сообщения передаются из модуля `hello_world` в `my_module`.

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. Запустите пример приложения `hello_world`, выполнив следующую команду:

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   Параметр `--config` принудительно запускает скрипт `run-hello-world.js` с помощью файла `hello_world.json`.

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

Поздравляем! Теперь вы можете увидеть поведение этого нового модуля: он просто выводит на экран сообщения "hello world" с меткой времени, чем отличается от исходного модуля "hello_world".

## <a name="next-steps"></a>Дальнейшие действия

Вы создали новый модуль, добавили его в пример приложения hello_world и запустили пример приложения с новым модулем в шлюзе. Дополнительные сведения о модулях шлюза Azure IoT и другие примеры модулей см. здесь: [https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules).

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

