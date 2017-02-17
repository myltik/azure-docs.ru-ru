---
featureFlags:
- usabilla
title: "Подключение Raspberry Pi (Node) к Интернету вещей Azure. Урок 1. Развертывание приложения | Документация Майкрософт"
description: "Клонируйте пример приложения Node.js из Github и разверните его с помощью средства Gulp на плате Raspberry Pi 3. Это приложение будет каждые две секунды включать и выключать светодиодный индикатор на компьютере."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "мигание светодиодного индикатора raspberry pi, включение индикатора с помощью raspberry pi"
ms.assetid: a5a03a57-fe86-416f-90ff-6eca17775842
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 90ceb432bd9506dc40e340af21a3cae4e21a20b0


---
# <a name="create-and-deploy-the-blink-application"></a>Создание и развертывание приложения для включения индикатора
## <a name="what-you-will-do"></a>Выполняемая задача
Клонирование примера приложения Node.js из GitHub и его развертывание с помощью средства Gulp на плате Raspberry Pi 3. Этот пример приложения будет каждые две секунды включать светодиодный индикатор, подключенный к плате. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Как использовать средство `device-discover-cli` для получения сетевых сведений о плате Pi.
* Как развертывать и запускать пример приложения на плате Pi.
* Как развертывать и отлаживать приложения, работающие удаленно на плате Pi.

## <a name="what-you-need"></a>Необходимые элементы
Необходимо успешно выполнить следующие операции:

* [Настройка устройства](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
* [Получение инструментов](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Получение IP-адреса и имени узла для платы Pi
В командной строке Windows или в окне терминала на устройстве под управлением macOS или Ubuntu выполните следующую команду.

```bash
devdisco list --eth
```

Должен отобразиться результат, аналогичный приведенному ниже:

![Обнаружение устройства](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Запишите значения `IP address` и `hostname` для платы Pi. Эти сведения потребуются позже в данной статье.

> [!NOTE]
> Убедитесь, что плата Pi подключена к той же сети, что и компьютер. Например, если компьютер подключен к беспроводной сети, а плата Pi подключена к проводной сети, то IP-адрес может не отобразиться в выходных данных devdisco.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения
Чтобы открыть пример кода, выполните следующие действия.

1. Клонируйте пример репозитория из GitHub, выполнив следующую команду.
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
   ```
2. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:
   
   ```bash
   cd iot-hub-node-raspberrypi-getting-started
   cd Lesson1
   code .
   ```

![Структура репозитория](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

Файл `app.js` в подпапке `app` — это ключевой исходный файл, содержащий код для управления светодиодным индикатором.

### <a name="install-application-dependencies"></a>Установка зависимостей приложения
Установите библиотеки и другие модули, необходимые для примера приложения, выполнив следующую команду:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Настройка подключения устройства
Чтобы настроить подключение устройства, выполните следующие действия.

1. Создайте файл конфигурации устройства, выполнив приведенную ниже команду.
   
   ```bash
   gulp init
   ```
   
   Файл конфигурации `config-raspberrypi.json` содержит учетные данные пользователя для входа в Pi. Чтобы избежать утечки учетных данных пользователя, файл конфигурации создается в подпапке `.iot-hub-getting-started` домашней папки на компьютере.

2. Откройте файл конфигурации устройства в Visual Studio Code, выполнив приведенную ниже команду.
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
3. Замените заполнитель `[device hostname or IP address]` IP-адресом или именем узла, которые вы записали ранее в разделе "Получение IP-адреса и имени узла для платы Pi".
   
   ![Config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> При подключении к устройству Raspberry Pi вместо имени пользователя и пароля можно использовать ключ SSH. Для этого необходимо будет создать ключ, используя команды **ssh-keygen** и **ssh-copy-id pi@\<адрес устройства\>**.
>
> В Windows эти команды доступны следующие в **Git Bash**.
>
> В MacOS необходимо выполнить команду **brew install ssh-copy-id**.
>
> После успешной отправки ключа на устройство Raspberry Pi замените **device_password** свойством **device_key_path** в файле **config-raspberrypi.json**.
>
> Обновленные строки должны выглядеть следующим образом:
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

Поздравляем! Вы успешно создали первый пример приложения для платы Pi.

## <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
### <a name="install-nodejs-and-npm-on-pi"></a>Установка Node.js и NPM на устройстве Pi
Установите Node.js и NPM на плату Pi, выполнив следующую команду.

```bash
gulp install-tools
```

При первом выполнении этой задачи на ее завершение может уйти 10 минут.

### <a name="deploy-and-run-the-sample-app"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения, выполнив следующую команду.

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Проверка работы приложения
На этом этапе светодиодный индикатор на плате Pi должен мигать с частотой в две секунды.  Если индикатор не мигает, см. способы решения распространенных проблем в [руководстве по устранению неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).
![Индикатор мигает](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>Сводка
Вы установили необходимые средства для работы с платой Pi и развернули пример приложения, заставляющего светодиодный индикатор мигать. Теперь можно приступать к созданию, развертыванию и запуску другого примера приложения, которое подключает плату Pi к Центру Интернета вещей Azure для отправки и получения сообщений.

## <a name="next-steps"></a>Дальнейшие действия
[Get the Azure tools](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md) (Получение инструментов Azure)




<!--HONumber=Jan17_HO4-->


