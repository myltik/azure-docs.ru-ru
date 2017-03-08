---
title: "Подключение Raspberry Pi (C) к Интернету вещей Azure. Устранение неполадок | Документация Майкрософт"
description: "Страница со сведениями об устранении неполадок в работе Raspberry Pi Node.js"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "неполадки Интернета вещей, проблемы Интернета вещей"
ms.assetid: 22cf50dc-8206-42a2-a1fc-f75fa85135fa
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 2c5e2955584b165ca0ca95ce424a2050fb3f6ccd


---
# <a name="troubleshooting"></a>Устранение неполадок
## <a name="hardware-issues"></a>Проблемы с оборудованием
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>Приложение работает хорошо, но светодиодный индикатор не мигает
Эта проблема всегда связана с подключением микросхемы оборудования. Чтобы определить проблемы, сделайте следующее:

1. Проверьте, выбран ли правильный порт **GPIO** на плате. Следует использовать два порта: **GPIO GND (штырь 6)** и **GPIO 04 (штырь 7)**.
2. Проверьте правильность полярности своего светодиодного индикатора. Более длинная ножка должна указывать **положительный**, анодный штырь.
3. Используйте **штырь 3.3V** и **штырь GND** на устройстве Raspberry Pi 3. Устройство Pi следует считать источником постоянного тока. Проверьте, нормально ли работает светодиодный индикатор.

![Спецификация светодиодного индикатора](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Другие проблемы с оборудованием
Сведения об устранении распространенных проблем на устройстве Raspberry Pi 3 см. на [этой официальной странице](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Проблемы с пакетами Node.js
### <a name="no-response-during-gulp-tasks"></a>Нет ответа при выполнении задач Gulp
Если при выполнении задач Gulp возникли проблемы, можно добавить параметр `--verbose` для отладки. Попробуйте завершить текущие задачи Gulp с помощью клавиш Ctrl + C, а затем выполните следующую команду в окне консоли, чтобы просмотреть сообщения отладки. В выходных данных консоли должны отобразиться подробные сообщения об ошибках.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Проблемы с обнаружением устройств
Справку по устранению распространенных проблем с помощью команды `devdisco` см. в [файле сведений](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="npm-issues"></a>проблемы с NPM
Попробуйте обновить пакет NPM, выполнив следующую команду.

```bash
npm install -g npm
```

Если проблема не исчезла, оставьте комментарии в конце этой статьи или задайте вопрос о проблеме в GitHub в нашем [примере репозитория](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started).

## <a name="remote-debugging"></a>Удаленная отладка
### <a name="run-the-sample-application-in-debug-mode"></a>Запуск примера приложения в режиме отладки
```bash
gulp run --debug
```

Когда модуль отладки будет готов, вы должны увидеть ```Debugger listening on port 5858``` в выходных данных консоли.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Настройка Visual Studio Code для подключения к удаленному устройству
1. Откройте панель **Отладка** слева.
2. Нажмите зеленую кнопку **Начать отладку** (клавиша F5). В Visual Studio Code откроется файл launch.json.
3. Обновите этот файл с использованием следующего содержимого. Замените `[device hostname or IP address]` на IP-адрес или имя узла самого устройства.

> [!NOTE]
> Дополнительные сведения об отладке Visual Studio см. в статье об [отладке в Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging#_launchjson-attributes).


```json
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
            "remoteRoot": null
        }
    ]
}
```

![Конфигурация удаленной отладки](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Присоединение к удаленному приложению
Нажмите зеленую кнопку **Начать отладку** (клавиша F5), чтобы начать отладку.

Дополнительные сведения об отладчике см. в статье [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) (JavaScript в VSCode).

![Интерактивная удаленная отладка](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Проблемы с интерфейсом командной строки Azure
Интерфейс командной строки Azure (Azure CLI) — это сборка предварительной версии. Найти решения можно в [руководстве по установке предварительной версии](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

При появлении ошибок в средстве сообщите о [проблеме](https://github.com/Azure/azure-cli/issues) в разделе **Проблемы** репозитория GitHub.

Справку по устранению распространенных проблем см. в [файле сведений](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Проблемы с установкой Python
### <a name="legacy-installation-issues-macos"></a>Проблемы с устаревшими установками (macOS)
При установке pip возникает ошибка разрешения, если есть устаревшие пакеты, которые устанавливаются с использованием разрешений **su**. Такая ситуация возникает из-за того, что предыдущая установка Python с использованием brew (macOS) удалена не полностью. Некоторые пакеты pip из предыдущей установки созданы с помощью разрешений корневой папки, что приводит к ошибке разрешения. Чтобы решить эту проблему, необходимо удалить эти пакеты, установленные с помощью разрешений корневой папки. Для этого необходимо сделать следующее:

1. Перейдите к папке /usr/local/lib/python2.7/site-packages.
2. Выведите список пакетов, созданных с использованием разрешений корневой папки: `ls -l | grep root`.
3. Удалите пакеты из шага 2: `sudo rm -rf {package name}`.
4. Переустановите Python.

## <a name="azure-iot-hub-issues"></a>Проблемы с Центром Интернета вещей Azure
Если Центр Интернета вещей Azure успешно подготовлен с помощью Azure CLI и требуется инструмент для управления устройствами, подключенными к вашему Центру Интернета вещей, воспользуйтесь указанными ниже инструментами.

### <a name="device-explorer"></a>Обозреватель устройств
Инструмент [обозреватель устройств](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) работает на локальном компьютере Windows и подключается к Центру Интернета вещей в Azure. Он взаимодействует со следующими [конечными точками Центра Интернета вещей](iot-hub-devguide.md):


* *управление удостоверениями устройств* для подготовки устройств, зарегистрированных в Центре Интернета вещей, и управления ими;
* *получение сообщений с устройства в облако*, что позволяет отслеживать сообщения, отправляемые с устройства в Центр Интернета вещей;
* *отправка сообщений из облака на устройство*, что позволяет отправлять сообщения на устройства из вашего Центра Интернета вещей.

Настройте свою строку подключения Центра Интернета вещей в этом инструменте, чтобы использовать все его возможности.

### <a name="iothub-explorer"></a>iothub-explorer
[iothub-explorer](https://github.com/Azure/iothub-explorer) — это пример мультиплатформенного инструмента интерфейса командной строки для управления устройствами. Его можно использовать для управления устройствами в реестре удостоверений, мониторинга сообщений, отправляемых с устройства в облако, и отправки сообщений из облака на устройство.

Чтобы установить последнюю версию (предварительную версию) инструмента iothub-explorer, выполните следующую команду в среде командной строки:

```bash
npm install -g iothub-explorer@latest
```

Дополнительную справку о всех командах iothub-explorer и их параметрах можно получить, выполнив следующую команду:

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Портал Azure
Все возможности интерфейса командной строки помогают создавать все ресурсы Azure и управлять ими. Для подготовки, отладки ресурсов Azure и управления ими можно также воспользоваться [порталом Azure](../azure-portal-overview.md).

## <a name="azure-storage-issues"></a>Проблемы с хранилищем Azure
[Обозреватель служб хранилища Microsoft Azure (предварительная версия)](http://storageexplorer.com) — это автономное приложение от корпорации Майкрософт, которое можно использовать для работы с данными из службы хранилища Azure на платформе Windows, macOS и Linux. Используя этот инструмент, можно подключаться к таблице и просматривать данные в ней. Его можно использовать для устранения неполадок в вашей службе хранилища Azure.




<!--HONumber=Jan17_HO4-->


