---
title: "Приступая к работе с имитацией устройства и шлюзом Azure IoT. Урок 1. Настройка NUC | Документация Майкрософт"
description: "Настройка Intel NUC в качестве шлюза Интернета вещей, который собирает данные из датчиков и передает их в Центр Интернета вещей Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "шлюз Интернета вещей, intel nuc, компьютер nuc, DE3815TYKE"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b87974be9570f7d03fe84ae0a1d1fa7e346ff189
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Настройка Intel NUC в качестве шлюза Интернета вещей

## <a name="what-you-will-do"></a>Выполняемая задача

- Настройте Intel NUC в качестве шлюза Интернета вещей.
- Установите пакет Edge Интернета вещей Azure на Intel NUC.
- Запустите пример приложения hello_world на Intel NUC для проверки работоспособности шлюза.
Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания

Из этого урока вы узнаете:

- как подключить периферийные устройства к Intel NUC;
- как установить и обновить требуемые пакеты на Intel NUC с помощью Smart Package Manager;
- как запустить пример приложения hello_world на Intel NUC для проверки работоспособности шлюза.

## <a name="what-you-need"></a>Необходимые элементы

- Предварительно установленный пакет DE3815TYKE для Intel NUC с набором программного обеспечения Intel для шлюза Интернета вещей (Wind River Linux *7.0.0.13).
- Кабель Ethernet.
- Клавиатура.
- Кабель HDMI или VGA.
- Монитор с портом HDMI или VGA.

![Комплект для шлюза](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Подключение периферийных устройств к Intel NUC

На следующем рисунке показана конфигурация Intel NUC с подключением к разным периферийным устройствам.

1. Подключение к клавиатуре.
2. Подключение к монитору через кабель VGA или HDMI.
3. Подключение к проводной сети с помощью кабеля Ethernet.
4. Подключение питания с помощью кабеля питания.

![Intel NUC с подключением к периферийным устройствам](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Подключение к системе Intel NUC с главного компьютера через Secure Shell (SSH)

На этом этапе вам потребуются клавиатура и монитор, чтобы узнать IP-адрес устройства NUC. Если вы уже знаете IP-адрес, можете сразу перейти к шагу 3 в этом разделе.

1. Включите Intel NUC, нажав кнопку питания, и войдите в систему.

   По умолчанию имя пользователя и пароль имеют одинаковые значения: `root`.

2. Получите IP-адрес устройства NUC, выполнив команду `ifconfig`. Этот шаг выполняется на устройстве NUC.

   Вот пример результата выполнения такой команды:

   ![Выходные данные ifconfig с IP-адресом NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   В этом примере значение IP-адреса указано после символов `inet addr:`. Этот адрес понадобится вам для удаленного подключения к Intel NUC с главного компьютера.

3. Используйте один из следующих SSH-клиентов для подключения к Intel NUC с главного компьютера.

   - [PuTTY](http://www.putty.org/) для Windows.
   - Встроенный SSH-клиент ОС Ubuntu или macOS.

   Работа с Intel NUC выполняется более эффективно, если подключиться к нему через SSH-клиент с главного компьютера. Для этого вам нужны IP-адрес, имя пользователя и пароль. Вот пример использования SSH-клиента на macOS.
   ![SSH-клиент, запущенный на macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>Установка пакета Edge Интернета вещей Azure

Пакет Edge Интернета вещей Azure содержит предварительно скомпилированные двоичные файлы самого пакета SDK и его зависимостей. В список этих файлов входят Edge Интернета вещей Azure, пакет SDK для Интернета вещей Azure и соответствующие средства. Пакет также содержит пример приложения hello_world, который используется для проверки работоспособности шлюза. Edge Интернета вещей является основой шлюза. Для установки пакета выполните следующие действия.

1. Добавьте репозиторий облака Интернета вещей, выполнив в окне терминала следующие команды:

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   Команда `rpm` импортирует ключ RPM. Команда `smart channel` добавляет канал RPM в Smart Package Manager. Перед запуском команды `smart update` вы должны увидеть примерно такие результаты, как показано ниже.

   ![результаты выполнения команд rpm и smart channel](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. Установите пакет, выполнив следующую команду:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   Здесь `packagegroup-cloud-azure` — это имя пакета. Команда `smart install` используется для установки пакета.

   Когда установка пакета завершится, Intel NUC должен выполнять функции шлюза.

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>Запуск примера приложения hello_world из Edge Интернета вещей Azure

Перейдите в каталог `azureiotgatewaysdk/samples` и запустите пример приложения hello_world. Этот пример приложения создает шлюз из файла `hello_world.json` и использует базовые компоненты архитектуры Edge Интернета вещей Azure, чтобы каждые 5 секунд записывать в журнал сообщение Hello World.

Чтобы запустить пример приложения hello_world, выполните следующую команду:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

При правильной работе шлюза этот пример приложения должен возвращать следующие данные:

![выходные данные приложения](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Сводка

Поздравляем! Итак, вы завершили настройку Intel NUC в качестве шлюза. Теперь можно переходить к следующему уроку, в котором вы настроите главный компьютер, настроите Центр Интернета вещей Azure и зарегистрируете логическое устройство Центра Интернета вещей Azure.

## <a name="next-steps"></a>Дальнейшие действия
[Подготовка главного компьютера и Центра Интернета вещей Azure](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

