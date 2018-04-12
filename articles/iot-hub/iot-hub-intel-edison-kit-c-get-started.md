---
title: Intel Edison в облако (C) — подключение Intel Edison к Центру Интернета вещей Azure | Документы Майкрософт
description: Узнайте, как настроить и подключить модуль Intel Edison к Центру Интернета вещей Azure и передавать данные с этого модуля в облако Azure.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timlt
tags: ''
keywords: Интернет вещей azure intel edison, Центр Интернета вещей intel edison, отправка данных intel edison в облако, intel edison в облако
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/17/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbdbe0230f742cd7228f04a4a83c9bd567527e8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Подключение Intel Edison к Центру Интернета вещей Azure (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

В этом руководстве вы начнете с того, что узнаете основы работы с Intel Edison. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md).

Нет начального набора? Начните [здесь](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Что нужно сделать

* Настройте модули Intel Edison и Grove.
* Создайте Центр Интернета вещей.
* Зарегистрируйте устройство для Edison в Центре Интернета вещей.
* Запустите пример приложения на Edison для отправки данных в Центр Интернета вещей.

Подключите Intel Edison к созданному Центру Интернета вещей. После этого запустите пример приложения на Edison, чтобы собрать данные о температуре и влажности с датчика температуры Grove. После этого отправим данные с датчика в Центр Интернета вещей.

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей Azure и получить строку подключения нового устройства.
* Как подключить Edison к датчику температуры Grove.
* Как собирать данные датчика, запустив пример приложения на Edison.
* Как отправить данные датчика в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

![Необходимые элементы](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Плата Intel Edison.
* плата расширения Arduino;
* Активная подписка Azure. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут.
* ПК или компьютер Mac под управлением Windows или Linux.
* Подключение к Интернету.
* кабель micro-USB (тип B-A);
* источник питания постоянного тока (DC). Источник питания должен иметь такие параметры:
  - напряжение постоянного тока 7–15 В;
  - мощность не менее 1500 мА;
  - центральная клемма положительной полярности.

Ниже приведены необязательные компоненты.

* Grove Base Shield V2
* Grove — датчик температуры
* Кабель Grove
* все прокладки и винты, которые входят в комплект поставки, включая два винта для крепления модуля к плате расширения и четыре набора винтов с пластиковыми опорами;

> [!NOTE] 
Эти компоненты необязательны, поскольку пример кода поддерживает использование смоделированных данных датчиков.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Настройка Intel Edison

### <a name="assemble-your-board"></a>Сборка платы

Этот раздел описывает процедуру подключения модуля Intel® Edison к плате расширения.

1. Поместите модуль Intel® Edison в белый контур на плате расширения, совместив отверстия в модуле с винтами на плате расширения.

2. Нажмите на модуль в точке под словами `What will you make?`, опустив его вниз до щелчка.

   ![Сборка платы 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Используйте две шестигранные гайки (входят в комплект) для крепления модуля к плате расширения.

   ![Сборка платы 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Вставьте винт в одно из четырех угловых отверстий на плате расширения. Наденьте на винт и плотно затяните белую пластмассовую опору.

   ![Сборка платы 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Повторите этот шаг для других угловых опор.

   ![Сборка платы 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Сборка платы завершена.

   ![Собранная плата](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Подключение Grove Base Shield и датчика температуры

1. Расположите Grove Base Shield на плате. Убедитесь, что все контакты хорошо подключены к плате.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. С помощью кабеля Grove подключите датчик температуры Grove к порту **A0** Grove Base Shield.

   ![Подключение к датчику температуры](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Подключение Edison и датчика](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Теперь датчик готов.

### <a name="power-up-edison"></a>Подключение питания Edison

1. Подключите источник питания.

   ![Подключение источника питания](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Должен загореться зеленый светодиод (на плате расширения Arduino* он обозначен как DS1).

3. Подождите минуту, пока завершится загрузка системной платы.

   > [!NOTE]
   > Если у вас нет источника питания постоянного тока, вы можете подать на плату питание через порт USB. Дополнительные сведения см. в разделе `Connect Edison to your computer`. Такой способ подачи питания может привести к непредсказуемому поведению системной платы, особенно при использовании Wi-Fi или двигателей.

### <a name="connect-edison-to-your-computer"></a>Подключение Edison к компьютеру

1. Переведите микропереключатель вниз, в сторону двух портов micro-USB, чтобы перевести Edison в режим устройства. Различия между режимами устройства и узла описаны [здесь](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Переключение микропереключателя вниз](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Подключите кабель micro-USB к верхнему порту micro-USB.

   ![Верхний порт micro-USB](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Другой конец кабеля USB подключите к компьютеру.

   ![Подключение USB к компьютеру](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. Вы можете быть уверены, что инициализация платы завершилась, когда компьютер присоединит новый диск (примерно так же, как при подключении SD-карты).

## <a name="download-and-run-the-configuration-tool"></a>Скачивание и запуск инструмента настройки
Скачайте последнюю версию инструмента настройки, выбрав нужный вариант в разделе `Installers` на [этой странице](https://software.intel.com/en-us/iot/hardware/edison/downloads). Запустите инструмент и выполните инструкции, которые будут отображаться на экране. Нажимайте кнопку "Далее" по мере необходимости.

### <a name="flash-firmware"></a>Встроенное ПО
1. На странице `Set up options` нажмите кнопку `Flash Firmware`.
2. Выберите образ, который нужно установить на плату:
   - чтобы скачать и установить последнюю версию встроенного ПО, предлагаемого Intel для вашей платы, выберите `Download the latest image version xxxx`;
   - чтобы установить образ, который вы ранее сохранили на своем компьютере, выберите `Select the local image`. Найдите и выберите образ для установки на плату.
3. Инструмент настройки попытается установить ПО на плату. Этот процесс может занять до 10 минут.

### <a name="set-password"></a>Установка пароля
1. На странице `Set up options` нажмите кнопку `Enable Security`.
2. Для платы Intel® Edison вы можете задать пользовательское имя. Это делать не обязательно.
3. Введите пароль для вашей платы, а затем щелкните `Set password`.
4. Запишите пароль, он вам пригодится позднее.

### <a name="connect-wi-fi"></a>Подключение Wi-Fi
1. На странице `Set up options` нажмите кнопку `Connect Wi-Fi`. Подождите примерно одну минуту, пока компьютер найдет доступные сети Wi-Fi.
2. В раскрывающемся списке `Detected Networks` выберите нужную сеть.
3. В раскрывающемся списке `Security` выберите режим безопасности для этой сети.
4. Укажите имя и пароль для входа, затем нажмите `Configure Wi-Fi`.
5. Запишите полученный IP-адрес, он вам пригодится позже.

> [!NOTE]
> Убедитесь, что плата Edison подключена к той же сети, что и компьютер. Компьютер подключается к плате Edison по IP-адресу.

   ![Подключение к датчику температуры](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Поздравляем! Вы успешно настроили устройство Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Запуск примера приложения на Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Подготовка пакета SDK устройства для Центра Интернета вещей

1. Используйте один из следующих SSH-клиентов для подключения к Intel Edison с главного компьютера. IP-адрес и пароль взяты из средства настройки.
    - [PuTTY](http://www.putty.org/) для Windows.
    - Встроенный SSH-клиент ОС Ubuntu или macOS (выполните `ssh root@"the IP address"`).

2. Клонируйте пример клиентского приложения на свое устройство. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. После этого перейдите к папке репозитория и выполните указанную ниже команду, чтобы создать пакет SDK Центра Интернета вещей Azure.

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Откройте файл конфигурации, выполнив следующую команду:

   ```bash
   nano config.h
   ```

   ![Файл конфигурации](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   В этом файле можно настроить два макроса. Первый из них — `INTERVAL`, который определяет промежуток времени между отправкой двух сообщений в облако. Второй — `SIMULATED_DATA`, который представляет собой логическое значение, определяющее, будут ли использоваться смоделированные данные датчика.

   Если у вас **нет датчика**, задайте для параметра `SIMULATED_DATA` значение `1`, чтобы пример приложения создал и использовал смоделированные данные датчика.

2. Сохраните изменения и закройте окно, нажав клавиши Control-O > ВВОД > Control-X.

### <a name="build-and-run-the-sample-application"></a>Создание и запуск примера приложения

1. Создайте пример приложения, выполнив следующую команду:

   ```bash
   cmake . && make
   ```
   ![Выходные данные при создании](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Запустите пример приложения, выполнив следующую команду:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Обязательно скопируйте и вставьте строку подключения устройства, заключив ее в одинарные кавычки.

Должны отобразиться следующие результаты, содержащие данные датчика и сообщения, которые отправляются в Центр Интернета вещей.

![Выходные данные — данные датчика, отправленные с Intel Edison в Центр Интернета вещей](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Дополнительная информация

Вы запустили пример приложения, чтобы собрать данные датчика и отправить их в Центр Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
