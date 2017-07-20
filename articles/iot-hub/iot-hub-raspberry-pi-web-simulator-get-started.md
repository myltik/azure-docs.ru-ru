---
title: "Подключение веб-симулятора Raspberry Pi (Node.js) к Центру Интернета вещей для передачи данных в облако | Документация Майкрософт"
description: "Подключение веб-симулятора Raspberry Pi к Центру Интернета вещей для передачи данных с него в облако Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "симулятор raspberry pi, raspberry pi и центр интернета вещей azure, raspberry pi и центр интернета вещей, отправка данных с raspberry pi в облако, подключение raspberry pi к облаку"
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: fcba6f37f4a71da78f0e8b1c83c7a60711bbed09
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017


---

# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Подключение онлайн-симулятора Raspberry Pi к Центру Интернета вещей Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

В этом руководстве описано, как начать работу с онлайн-симулятором Raspberry Pi. Кроме того, вы узнаете, как можно легко подключить симулятор Pi к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md). 

Если вы используете физические устройства, ознакомьтесь со статьей [Подключение Raspberry Pi к Центру Интернета вещей Azure (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md), чтобы приступить к работе. 

## <a name="what-you-do"></a>В рамках этого руководства мы:

* Изучим основы использования онлайн-симулятора Raspberry Pi.
* Создайте центр IoT.
* Зарегистрируем устройство для Pi в Центре Интернета вещей.
* Запустим пример приложения на Pi для отправки симулированных данных датчика в Центр Интернета вещей.

Подключите виртуальное устройство Raspberry Pi к созданному Центру Интернета вещей. Затем запустите пример приложения с помощью симулятора, чтобы создать данные датчика. После этого отправьте данные с датчика в Центр Интернета вещей.

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей Azure и получить строку подключения нового устройства. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут.
* Как использовать онлайн-симулятор Raspberry Pi.
* Как отправить данные датчика в Центр Интернета вещей.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Общие сведения о веб-симуляторе Raspberry Pi

Нажмите кнопку, чтобы запустить онлайн-симулятор Raspberry Pi.

> [!div class="button"]
[Запустить симулятор Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/)

В веб-симуляторе есть 3 области.
* Область сборки. В стандартной схеме Pi соединяется с датчиком BME280 и светодиодным индикатором. В предварительной версии эта область заблокирована, поэтому вы не можете выполнить настройку.
* Область кодирования. Интерактивный редактор кода, позволяющий писать код, используя Raspberry Pi. Стандартный пример приложения позволяет выполнить сбор данных датчика BME280 и отправить их в Центр Интернета вещей. Приложение полностью совместимо с реальными устройствами Pi. 
* Встроенное окно консоли. Показывает выходные данные вашего кода. В верхней части этого окна есть 3 кнопки.
   * **Запуск.** Запуск приложения в области кодирования.
   * **Сброс.** Сброс параметров области кодирования до стандартных параметров примера приложения.
   * **Fold/Expand** (Свернуть или развернуть). В правой части расположена кнопка, с помощью которой можно свернуть или развернуть окно консоли.

> [!NOTE] 
Веб-симулятор Raspberry Pi доступен в предварительной версии. Оставляйте свои комментарии в [чате Gitter](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Исходный код доступен на сайте [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Общие сведения об онлайн-симуляторе Pi](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Запуск примера приложения на веб-симуляторе Pi

1. В области кодирования убедитесь, что вы работаете над стандартным примером приложения. Замените значения заполнителя в 15 строке на строку подключения устройства Центра Интернета вещей.
   ![Замена строки подключения устройства](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Щелкните **Запуск** или введите `npm start`, чтобы запустить приложение.


Должны отобразиться следующие результаты, содержащие данные датчика и сообщения, которые отправляются в Центр Интернета вещей: ![Выходные данные — данные датчика, отправленные с Raspberry Pi в Центр Интернета вещей](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Дальнейшие действия

Вы запустили пример приложения, чтобы собрать данные датчика и отправить их в Центр Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

