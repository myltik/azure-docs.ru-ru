---
title: "Устранение неполадок устройства SensorTag и шлюза Azure IoT | Документация Майкрософт"
description: "Страница со сведениями об устранении неполадок для шлюза Intel NUC"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "неполадки Интернета вещей, проблемы Интернета вещей"
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: f24a620a18d7756b24f8f5f63d8b31fca63ade25
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>Устранение неполадок

## <a name="hardware-issues"></a>Проблемы с оборудованием

### <a name="ti-sensortag-cannot-be-connected"></a>Не удается подключить TI SensorTag

Чтобы устранить проблемы с подключением SensorTag, воспользуйтесь [приложением SensorTag](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Проблема с Intel NUC

Сведения об устранении проблем при загрузке в Intel NUC см. [на этой странице](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Сведения об устранении проблем с операционной системой в Intel NUC см. [на этой странице](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Сведения об устранении других проблем см. в статье [Blink Codes and Beep Codes for Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html) (Коды включения индикатора и звуковые сигналы для Intel NUC).

## <a name="nodejs-package-issues"></a>Проблемы с пакетами Node.js

### <a name="no-response-during-gulp-tasks"></a>Нет ответа при выполнении задач Gulp

Если при выполнении задач Gulp возникли проблемы, можно добавить параметр `--verbose` для отладки. Попробуйте завершить текущие задачи Gulp, используя клавиши `Ctrl + C`, а затем выполните следующую команду в окне консоли, чтобы просмотреть сообщения отладки. В выходных данных консоли должны отобразиться подробные сообщения об ошибках.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Проблемы с обнаружением устройств

Справку по устранению распространенных проблем с помощью команды `discover-sensortag` см. на [вики-странице](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>проблемы с NPM

Попробуйте обновить пакет NPM, выполнив следующую команду:

```bash
npm install -g npm
```

Если проблема не исчезла, оставьте комментарии в конце этой статьи или задайте вопрос о проблеме в GitHub в нашем [примере репозитория](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Удаленная отладка
> Приведенные ниже инструкции предназначены для отладки сценариев Node.js, используемых в этом руководстве.
### <a name="run-the-sample-application-in-debug-mode"></a>Запуск примера приложения в режиме отладки

Запустите пример приложения в режиме отладки, выполнив следующую команду:

```bash
gulp run --debug
```

Когда модуль отладки будет готов, вы должны увидеть `Debugger listening on port 5858` в выходных данных консоли.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Настройка Visual Studio Code для подключения к удаленному устройству

1. Откройте панель **Отладка** слева.
2. Нажмите зеленую кнопку **Начать отладку** (клавиша F5). В Visual Studio Code откроется файл `launch.json`.
3. Обновите файл `launch.json` с использованием следующего содержимого. Замените `[device hostname or IP address]` на IP-адрес или имя узла самого устройства.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Конфигурация удаленной отладки](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Присоединение к удаленному приложению

Нажмите зеленую кнопку **Начать отладку** (клавиша F5), чтобы начать отладку.

Дополнительные сведения об отладчике см. в статье [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) (JavaScript в VSCode).

![Отладка примера BLE](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Проблемы с интерфейсом командной строки Azure

Интерфейс командной строки Azure (Azure CLI) — это сборка предварительной версии. Найти решения можно в [руководстве по установке предварительной версии](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

При появлении ошибок в средстве сообщите о [проблеме](https://github.com/Azure/azure-cli/issues) в разделе **Проблемы** репозитория GitHub.

Справку по устранению распространенных проблем см. в [файле сведений](https://github.com/Azure/azure-cli/blob/master/README.rst).

При появлении сообщения "Не удалось найти версию, которая удовлетворяет требованиям" выполните следующую команду, чтобы обновить pip до последней версии.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Проблемы с установкой Python

### <a name="legacy-installation-issues-macos"></a>Проблемы с устаревшими установками (macOS)

При установке pip возникает ошибка разрешения, если есть устаревшие пакеты, которые устанавливаются с использованием разрешений **su**. Такая ситуация возникает из-за того, что предыдущая установка Python с использованием brew (macOS) удалена не полностью. Некоторые пакеты pip из предыдущей установки созданы с помощью разрешений корневой папки, что приводит к ошибке разрешения. Чтобы решить эту проблему, необходимо удалить эти пакеты, установленные с помощью разрешений корневой папки. Для этого необходимо сделать следующее:

1. Перейдите на сайт `/usr/local/lib/python2.7/site-packages`.
2. Выведите список пакетов, созданных с использованием разрешений корневой папки: `ls -l | grep root`.
3. Удалите пакеты из шага 2: `sudo rm -rf {package name}`.
4. Переустановите Python.

## <a name="azure-iot-hub-issues"></a>Проблемы с Центром Интернета вещей Azure

Если Центр Интернета вещей Azure успешно подготовлен с помощью Azure CLI и требуется инструмент для управления устройствами, подключенными к вашему Центру Интернета вещей, воспользуйтесь указанными ниже инструментами.

### <a name="device-explorer"></a>Обозреватель устройств

[Обозреватель устройств](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) работает на локальном компьютере Windows и подключается к Центру Интернета вещей в Azure. Он взаимодействует со следующими [конечными точками Центра Интернета вещей](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- управление удостоверениями устройств для подготовки устройств, зарегистрированных в Центре Интернета вещей, и управления ими;
- получение сообщений с устройства в облако, что позволяет отслеживать сообщения, отправляемые с устройства в Центр Интернета вещей;
- отправка сообщений из облака на устройство, что позволяет отправлять сообщения на устройства из вашего Центра Интернета вещей.

Настройте свою строку подключения Центра Интернета вещей в этом инструменте, чтобы использовать все его возможности.

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) — это пример мультиплатформенного инструмента интерфейса командной строки для управления клиентами устройств. Инструмент можно использовать для управления устройствами в реестре удостоверений, мониторинга сообщений, отправляемых с устройства в облако, и отправки команд, передаваемых из облака на устройство.

Чтобы установить последнюю версию (предварительную версию) инструмента iothub-explorer, выполните следующую команду:

```bash
npm install -g iothub-explorer@latest
```

Дополнительную справку о всех командах iothub-explorer и их параметрах можно получить, выполнив следующую команду:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Портал Azure

Все возможности интерфейса командной строки помогают создавать все ресурсы Azure и управлять ими. Для подготовки, отладки ресурсов Azure и управления ими можно также воспользоваться [порталом Azure](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/).

## <a name="azure-storage-issues"></a>Проблемы с хранилищем Azure

[Обозреватель служб хранилища Microsoft Azure (предварительная версия)](http://storageexplorer.com/) — это автономное приложение от корпорации Майкрософт, которое можно использовать для работы с данными из службы хранилища Azure на платформе Windows, macOS и Linux. Используя этот инструмент, можно подключаться к таблице и просматривать данные в ней. Его можно использовать для устранения неполадок в вашей службе хранилища Azure.

