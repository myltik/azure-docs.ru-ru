---
title: "Устранение неполадок в работе с начальным набором Интернета вещей Azure для Intel Edison | Документация Майкрософт"
description: "Страница со сведениями об устранении неполадок в работе Intel Edison Node.js"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "устранение неполадок Arduino"
ms.assetid: f11c5521-8a36-44c0-baad-f5ec26ab4618
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: f45b3bf00d619376ac07418f0c02eca5f3241939
ms.openlocfilehash: e37d21e22b63c552e3de99c08862f3acaabd3598


---
# <a name="troubleshooting"></a>Устранение неполадок
## <a name="hardware-issues"></a>Проблемы с оборудованием
Сведения об устранении распространенных проблем на устройстве Intel Edison см. на [этой официальной странице](https://software.intel.com/en-us/node/637974).

## <a name="nodejs-package-issues"></a>Проблемы с пакетами Node.js
### <a name="no-response-during-gulp-tasks"></a>Нет ответа при выполнении задач Gulp
Если при выполнении задач Gulp возникли проблемы, можно добавить параметр `--verbose` для отладки. Попробуйте завершить текущие задачи Gulp, используя клавиши `Ctrl + C`, а затем выполните следующую команду в окне консоли, чтобы просмотреть сообщения отладки. В выходных данных консоли должны отобразиться подробные сообщения об ошибках. 

```bash
gulp --verbose
```

### <a name="npm-issues"></a>проблемы с NPM
Попробуйте обновить пакет NPM, выполнив следующую команду:

```bash
npm install -g npm
```

Если проблема не исчезла, оставьте комментарии в конце этой статьи или задайте вопрос о проблеме в GitHub в нашем [примере репозитория][sample-repository].

## <a name="remote-debugging"></a>Удаленная отладка

### <a name="run-the-sample-application-in-debug-mode"></a>Запуск примера приложения в режиме отладки

```bash
gulp run --debug
```

Когда модуль отладки будет готов, ```Debugger listening on port 5858``` отобразится в выходных данных консоли.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Настройка VSCode для подключения к удаленному устройству

Откройте панель **Отладка** слева.

Нажмите зеленую кнопку **Начать отладку** (клавиша F5). В VSCode откроется файл **launch.json**, который необходимо обновить.

Обновите файл **launch.json** с использованием следующего содержимого, заменив `[device hostname or IP address]` IP-адресом или именем узла самого устройства.  

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

![Конфигурация удаленной отладки](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Присоединение к удаленному приложению

Нажмите зеленую кнопку **Начать отладку** (клавиша F5), чтобы запустить отладку.

Дополнительные сведения об отладчике см. в статье [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) (JavaScript в VSCode).

![Интерактивная удаленная отладка](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Проблемы с Azure CLI
Интерфейс командной строки Azure (Azure CLI) — это сборка предварительной версии. Решения можно найти в [руководстве по установке предварительной версии](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md). Попытайтесь обновить Azure CLI до последней версии, если команда не работает должным образом.

При появлении ошибок в средстве сообщите о [проблеме](https://github.com/Azure/azure-cli/issues) в разделе **Проблемы** репозитория GitHub.

Справку по устранению распространенных проблем см. в [файле сведений](https://github.com/Azure/azure-cli/blob/master/README.rst).

При появлении сообщения "Не удалось найти версию, которая удовлетворяет требованиям" выполните следующую команду, чтобы обновить pip до последней версии.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Проблемы с установкой Python
### <a name="legacy-installation-issues-macos"></a>Проблемы с устаревшими установками (macOS)
При установке **pip** возникает ошибка разрешения, если есть устаревшие пакеты, которые устанавливаются с использованием разрешений **su**. Такая ситуация возникает из-за того, что предыдущая установка Python с использованием brew (macOS) удалена не полностью. Некоторые пакеты **pip** из предыдущей установки созданы с помощью разрешений корневой папки, что приводит к ошибке разрешения. Чтобы решить эту проблему, необходимо удалить эти пакеты, установленные с помощью разрешений корневой папки. Для этого необходимо сделать следующее:

1. Перейдите к папке /usr/local/lib/python2.7/site-packages.
2. Выведите список пакетов, созданных с использованием разрешений корневой папки: `ls -l | grep root`.
3. Удалите пакеты из шага 2: `sudo rm -rf {package name}`.
4. Переустановите Python.

## <a name="azure-iot-hub-issues"></a>Проблемы с Центром Интернета вещей Azure
Если Центр Интернета вещей Azure успешно подготовлен с помощью `azure-cli` и требуется инструмент для управления устройствами, подключенными к вашему Центру Интернета вещей, воспользуйтесь следующими инструментами:

### <a name="device-explorer"></a>Обозреватель устройств
[Обозреватель устройств](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) работает на локальном компьютере Windows и подключается к Центру Интернета вещей в Azure. Он взаимодействует со следующими [конечными точками Центра Интернета вещей](iot-hub-devguide.md):

- _управление удостоверениями устройств_ для подготовки устройств, зарегистрированных в Центре Интернета вещей, и управления ими;
- _получение сообщений с устройства в облако_, что позволяет отслеживать сообщения, отправляемые с устройства в Центр Интернета вещей;
- _отправка сообщений из облака на устройство_, что позволяет отправлять сообщения на устройства из вашего Центра Интернета вещей.

Настройте свою `IoT hub connection string` в этом инструменте, чтобы использовать его возможности.

### <a name="iot-hub-explorer"></a>Обозреватель Центра Интернета вещей
[Обозреватель Центра Интернета вещей](https://github.com/Azure/iothub-explorer) — это пример мультиплатформенного инструмента интерфейса командной строки для управления клиентами устройств. Инструмент можно использовать для управления устройствами в реестре удостоверений, мониторинга сообщений, отправляемых с устройства в облако, и отправки команд, передаваемых из облака на устройство.

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

## <a name="azure-storage-issues"></a>Проблемы со службой хранилища Azure
[Обозреватель служб хранилища Microsoft Azure (предварительная версия)](http://storageexplorer.com) — это автономное приложение от корпорации Майкрософт, которое можно использовать для работы с данными из [службы хранилища Azure](https://azure.microsoft.com/en-us/services/storage/) на платформе Windows, macOS и Linux. Используя этот инструмент, можно подключаться к таблице и просматривать данные в ней. Его можно использовать для устранения неполадок в вашей службе хранилища Azure.

## <a name="next-steps"></a>Дальнейшие действия
На этой странице приведены только наиболее распространенные проблемы с набором Intel Edison. Вы также можете оставить комментарии внизу страницы, чтобы сообщить о проблемах для дальнейшего устранения неполадок.

Вернитесь к изучению статьи [Начало работы с Intel Edison (Node.js)](iot-hub-intel-edison-kit-node-get-started.md)

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-node-edison-getting-started


<!--HONumber=Dec16_HO2-->


