---
title: "Подключение Intel Edison (Node) к Интернету вещей Azure. Урок 1. Настройка устройства | Документация Майкрософт"
description: "Настройка Intel Edison для первого использования."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Arduino, настройка, подключение Arduino к компьютеру, настройка Arduino, плата Arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 372c9b6d-e701-4ff6-8151-d262aa76aa55
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 203d3976b9ba0960f5a3dbff4e2b1fcdf4c0dc33
ms.contentlocale: ru-ru
ms.lasthandoff: 01/25/2017

---
<a id="configure-your-intel-edison" class="xliff"></a>

# Настройка Intel Edison
<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача
Настройте Intel Edison для первого использования: соберите плату, подключите ее и установите на компьютере инструмент для настройки, с помощью которого установите встроенное ПО Intel Edison, настроите пароль и подключите устройство к Wi-Fi. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания
В этой статье вы узнаете следующее:

* как собрать и включить плату Edison;
* как прошить встроенное ПО Edison, установить пароль и подключиться к Wi-Fi.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы
Чтобы выполнить эту операцию, вам понадобятся следующие элементы из начального набора Intel Edison:

* модуль Intel® Edison;
* плата расширения Arduino;
* все прокладки и винты, которые входят в комплект поставки, включая два винта для крепления модуля к плате расширения и четыре набора винтов с пластиковыми опорами;
* кабель micro-USB (тип B-A);
* источник питания постоянного тока (DC). Источник питания должен иметь такие параметры:
  - напряжение постоянного тока&7;–15 В;
  - мощность не менее 1500 мА;
  - центральная клемма положительной полярности.

  ![Предметы в начальном наборе](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

Кроме того, вам понадобится следующее:

* Компьютер под управлением Windows, Mac или Linux.
* Беспроводное подключение, к которому будет подключаться Edison.
* Подключение к Интернету для скачивания инструмента настройки.

<a id="assemble-your-board" class="xliff"></a>

## Сборка платы

Этот раздел описывает процедуру подключения модуля Intel® Edison к плате расширения.

1. Поместите модуль Intel® Edison в белый контур на плате расширения, совместив отверстия в модуле с винтами на плате расширения.

2. Нажмите на модуль в точке под словами `What will you make?`, опустив его вниз до щелчка.

   ![Сборка платы 2](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. Используйте две шестигранные гайки (входят в комплект) для крепления модуля к плате расширения.

   ![Сборка платы 3](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. Вставьте винт в одно из четырех угловых отверстий на плате расширения. Наденьте на винт и плотно затяните белую пластмассовую опору.

   ![Сборка платы 4](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. Повторите этот шаг для других угловых опор.

   ![Сборка платы 5](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

Сборка платы завершена.

   ![Собранная плата](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

<a id="power-up-edison" class="xliff"></a>

## Подключение питания Edison

1. Подключите источник питания.

   ![Подключение источника питания](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. Должен загореться зеленый светодиод (на плате расширения Arduino* он обозначен как DS1).

3. Подождите минуту, пока завершится загрузка системной платы.

   > [!NOTE]
   > Если у вас нет источника питания постоянного тока, вы можете подать на плату питание через порт USB. Дополнительные сведения см. в разделе `Connect Edison to your computer`. Такой способ подачи питания может привести к непредсказуемому поведению системной платы, особенно при использовании Wi-Fi или двигателей.

<a id="connect-edison-to-your-computer" class="xliff"></a>

## Подключение Edison к компьютеру

1. Переведите микропереключатель вниз, в сторону двух портов micro-USB, чтобы перевести Edison в режим устройства. Различия между режимами устройства и узла описаны [здесь](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Переключение микропереключателя вниз](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. Подключите кабель micro-USB к верхнему порту micro-USB.

   ![Верхний порт micro-USB](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. Другой конец кабеля USB подключите к компьютеру.

   ![Подключение USB к компьютеру](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

4. Вы можете быть уверены, что инициализация платы завершилась, когда компьютер присоединит новый диск (примерно так же, как при подключении SD-карты).

<a id="download-and-run-the-configuration-tool" class="xliff"></a>

## Скачивание и запуск инструмента настройки
Скачайте последнюю версию инструмента настройки, выбрав нужный вариант в разделе `Installers` на [этой странице](https://software.intel.com/en-us/iot/hardware/edison/downloads). Запустите инструмент и выполните инструкции, которые будут отображаться на экране. Нажимайте кнопку "Далее" по мере необходимости.

<a id="flash-firmware" class="xliff"></a>

### Встроенное ПО
1. На странице `Set up options` нажмите кнопку `Flash Firmware`.
2. Выберите образ, который нужно установить на плату:
   - чтобы скачать и установить последнюю версию встроенного ПО, предлагаемого Intel для вашей платы, выберите `Download the latest image version xxxx`;
   - чтобы установить образ, который вы ранее сохранили на своем компьютере, выберите `Select the local image`. Найдите и выберите образ для установки на плату.
3. Инструмент настройки попытается установить ПО на плату. Этот процесс может занять до 10 минут.

<a id="set-password" class="xliff"></a>

### Установка пароля
1. На странице `Set up options` нажмите кнопку `Enable Security`.
2. Для платы Intel® Edison вы можете задать пользовательское имя. Это необязательно.
3. Введите пароль для вашей платы, а затем щелкните `Set password`.
4. Запишите пароль, он вам пригодится позднее.

<a id="connect-wi-fi" class="xliff"></a>

### Подключение Wi-Fi
1. На странице `Set up options` нажмите кнопку `Connect Wi-Fi`. Подождите примерно одну минуту, пока компьютер найдет доступные сети Wi-Fi.
2. В раскрывающемся списке `Detected Networks` выберите нужную сеть.
3. В раскрывающемся списке `Security` выберите режим безопасности для этой сети.
4. Укажите имя и пароль для входа, затем нажмите `Configure Wi-Fi`.
5. Запишите полученный IP-адрес, он вам пригодится позже.

> [!NOTE]
> Убедитесь, что плата Edison подключена к той же сети, что и компьютер. Компьютер подключается к плате Edison по IP-адресу.

Поздравляем! Вы успешно настроили устройство Edison.

<a id="summary" class="xliff"></a>

## Сводка
Из этой статьи вы узнали, как собрать плату Edison, как установить встроенное ПО, задать пароль и подключить устройство к Wi-Fi с помощью инструмента настройки. Обратите внимание, что светодиодный индикатор еще не светится. Следующая задача — установить необходимые инструменты и программное обеспечение, которые потребуются для запуска примера приложения на устройстве Edison.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Get the tools][get-the-tools] (Получение инструментов)
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
