---
title: Настройка кодировщика FMLE для отправки односкоростного обновляющегося потока | Документация Майкрософт
description: В этой статье показано, как настроить кодировщик Flash Media Live Encoder (FMLE) для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в реальном времени.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 055decd689e260a69651c5a3e1ce3f3f78b67340
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783543"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Использование кодировщика FMLE для отправки односкоростного обновляющегося потока
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

В этой статье показано, как настроить кодировщик [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в режиме реального времени. Дополнительные сведения можно найти в разделе [Работа с каналами, выполняющими кодирование в реальном времени с помощью служб мультимедиа Azure](media-services-manage-live-encoder-enabled-channels.md).

В этом учебнике показано, как управлять службами мультимедиа Azure (AMS) с помощью Обозревателя служб мультимедиа Azure. Это средство запускается только на компьютерах с ОС Windows. Если вы используете Mac или Linux, воспользуйтесь классическим порталом Azure для создания [каналов](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) и [программ](media-services-portal-creating-live-encoder-enabled-channel.md).

В этом руководстве мы используем кодек AAC. Однако FMLE не поддерживает AAC по умолчанию. Для использования AAC необходимо приобрести подключаемый модуль, например, от MainConcept: [подключаемый модуль AAC](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>предварительным требованиям
* [Создайте учетную запись служб мультимедиа Azure](media-services-portal-create-account.md).
* Убедитесь, что запущена конечная точка потоковой передачи. Дополнительные сведения см. в статье об [управлении конечными точками потоковой передачи с помощью учетной записи служб мультимедиа](media-services-portal-manage-streaming-endpoints.md).
* Установите последнюю версию средства [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Запустите его и подключитесь к учетной записи AMS.

## <a name="tips"></a>Советы
* По возможности используйте проводное подключение к Интернету.
* Для получения необходимой пропускной способности рекомендуется удвоить скорость потоковой передачи. Это требование не является обязательным, но оно поможет уменьшить влияние перегрузки сети на пропускную способность.
* При использовании программных кодировщиков закройте все ненужные программы.

## <a name="create-a-channel"></a>Создание канала
1. В средстве AMSE откройте вкладку **Live** (Передача) и щелкните правой кнопкой мыши в области канала. Выберите **Создать канал...** в меню.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Укажите имя канала, поле описания является необязательным. В разделе Channel Settings ("Параметры каналов") для параметра Live Encoding ("Кодирование в реальном времени") установите значение **Standard** ("Стандартное"), а для параметра Input Protocol ("Входной протокол") — значение **RTMP**. Остальные параметры можно оставить без изменений.

    Убедитесь, что флажок **Start the new channel now** (Сразу запустить новый канал) установлен.

3. Щелкните **Создать канал**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> На запуск канала может потребоваться до 20 минут.
>
>

Во время запуска канала можно [настроить кодировщик](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Обратите внимание, что тарификация начинается сразу же после перехода канала в состояние готовности. Дополнительные сведения см. в разделе о [состояниях каналов](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Настройка кодировщика FMLE
В этом руководстве используются указанные ниже параметры вывода. В оставшейся части этого раздела этапы настройки описываются более подробно.

**Видео:**

* Кодек: H.264
* Профиль: High (уровень 4.0)
* Скорость: 5000 Кбит/с
* Опорный кадр: 2 секунды (60 секунд)
* Частота кадров: 30

**Звук:**

* Кодек: AAC (LC)
* Скорость: 192 Кбит/с
* Частота выборки: 44,1 кГц

### <a name="configuration-steps"></a>Этапы настройки
1. Перейдите к интерфейсу Flash Media Live Encoder (FMLE) на используемом компьютере.

    Интерфейс представляет собой одну главную страницу с параметрами. Обратите внимание на указанные ниже рекомендуемые параметры для потоковой передачи с FMLE.

   * Формат: H.264 Частота кадров: 30.00
   * Размер входного потока: 1280 x 720
   * Скорость: 5000 кбит/сек (это значение можно изменить в зависимости от ограничений сети)  

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     При использовании источников с чересстрочной разверткой установите флажок «Устранить чересстрочную развертку».
2. Выберите значок гаечного ключа рядом с полем «Формат», там должны быть установлены следующие дополнительные параметры:

   * Профиль: основной
   * Уровень: 4.0
   * Частота опорного кадра: 2 секунды

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Установите следующие важные параметры звука:

   * Формат: AAC
   * Частота выборки: 44100 Гц
   * Скорость: 192 Кбит/с

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Получите входной URL-адрес канала, чтобы назначить его **конечной точке RTMP**FMLE.

    Перейдите обратно в средство AMSE и проверьте состояние запуска канала. После изменения состояния **Starting** (Запуск) на **Running** (Выполняется) можно получить URL-адрес входных данных.

    Когда канал запустится, щелкните правой кнопкой мыши его имя, перейдите вниз к элементу **Copy Input URL to clipboard** (Скопировать входной URL-адрес в буфер обмена) и выберите пункт **Primary Input URL** (Основной входной URL-адрес).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Вставьте этот адрес в поле **URL-адрес FMS** раздела выхода и укажите имя потока.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Для обеспечения дополнительной надежности повторите эти действия с полем «Дополнительный входной URL-адрес».
6. Нажмите кнопку **Подключиться**.

> [!IMPORTANT]
> Прежде чем нажать кнопку **Connect** ("Подключиться"), **необходимо** убедиться, что канал находится в состоянии готовности.
> Кроме того, не оставляйте канал в состоянии готовности без входного потока более 15 минут.
>
>

## <a name="test-playback"></a>Проверка воспроизведения

Перейдите в средство AMSE и щелкните правой кнопкой мыши канал, который необходимо проверить. В меню наведите указатель мыши на пункт **Playback the Preview** (Воспроизведение для предварительного просмотра) и выберите пункт **with Azure Media Player** (с помощью Проигрывателя мультимедиа Azure).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Если поток отображается в проигрывателе, то кодировщик правильно настроен для подключения к AMS.

При получении сообщения об ошибке необходимо сбросить канал и настроить параметры кодировщика. Дополнительные сведения см. в руководстве по [устранению неполадок](media-services-troubleshooting-live-streaming.md).  

## <a name="create-a-program"></a>Создание программы
1. После проверки воспроизведения канала создайте программу. На вкладке **Live** (Передача) в инструменте AMSE щелкните правой кнопкой мыши в области программы и выберите команду **Create New Program** (Создать программу).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Укажите имя программы и при необходимости измените параметр **Archive Window Length** (Длительность окна архивации) (по умолчанию она составляет 4 часа). Также можно указать расположение для хранения или оставить значение по умолчанию.  
3. Установите флажок **Start the Program now** (Запустить программу сейчас).
4. Щелкните **Create Program**(Создать программу).  

    >[!NOTE]
    >Создание программы занимает меньше времени, чем создание канала.
        
5. После запуска программы проверьте воспроизведение. Для этого щелкните правой кнопкой мыши программу, перейдите к пункту **Playback the program(s)** (Воспроизвести программы) и выберите пункт **with Azure Media Player** (с помощью Проигрывателя мультимедиа Azure).  
6. После проверки снова щелкните программу правой кнопкой мыши и выберите команду **Copy the Output URL to Clipboard** (Копировать выходной URL-адрес в буфер обмена) или получите соответствующий адрес, используя пункт меню **Program information and settings** (Сведения о программе и параметры).

Теперь поток можно внедрить в проигрыватель или транслировать аудитории для просмотра в режиме реального времени.  

## <a name="troubleshooting"></a>Устранение неполадок
Дополнительные сведения см. в руководстве по [устранению неполадок](media-services-troubleshooting-live-streaming.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
