---
title: Настройка кодировщика Telestream Wirecast для отправки односкоростного обновляющегося потока | Документация Майкрософт
description: 'В этой статье показано, как настроить динамический кодировщик Wirecast для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в реальном времени. '
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 0e4fb0b7c915969da1760eaccc77aa399030752e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Использование кодировщика Wirecast для отправки односкоростного обновляющегося потока
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

В этой статье показано, как настроить динамический кодировщик [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в режиме реального времени.  Дополнительные сведения можно найти в разделе [Работа с каналами, выполняющими кодирование в реальном времени с помощью служб мультимедиа Azure](media-services-manage-live-encoder-enabled-channels.md).

В этом учебнике показано, как управлять службами мультимедиа Azure (AMS) с помощью Обозревателя служб мультимедиа Azure. Это средство запускается только на компьютерах с ОС Windows. Если вы используете Mac или Linux, воспользуйтесь классическим порталом Azure для создания [каналов](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) и [программ](media-services-portal-creating-live-encoder-enabled-channel.md).

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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Укажите имя канала, поле описания является необязательным. В разделе Channel Settings ("Параметры каналов") для параметра Live Encoding ("Кодирование в реальном времени") установите значение **Standard** ("Стандартное"), а для параметра Input Protocol ("Входной протокол") — значение **RTMP**. Остальные параметры можно оставить без изменений.

    Убедитесь, что флажок **Start the new channel now** (Сразу запустить новый канал) установлен.

3. Щелкните **Создать канал**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> На запуск канала может потребоваться до 20 минут.
>
>

Во время запуска канала можно [настроить кодировщик](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Тарификация начинается сразу же после перехода канала в состояние готовности. Дополнительные сведения см. в разделе о [состояниях каналов](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_wirecast_rtmp></a>Настройка кодировщика Telestream Wirecast
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
1. Откройте приложение Telestream Wirecast на используемом компьютере и настройте его для потоковой передачи RTMP.
2. Настройте выходные данные, перейдя на вкладку **Output** (Вывод) и выбрав **Output Settings…** (Параметры вывода…).

    В поле **Output Destination** (Назначение вывода) выберите значение **RTMP Server** (Сервер RTMP).
3. Последовательно выберите **ОК**.
4. На странице параметров в поле **Destination** (Назначение) задайте значение **Azure Media Services** (Службы мультимедиа Azure).

    В поле «Профиль кодировки» будет выбран профиль **Azure H.264 720 p 16:9 (1280 x 720)**. Чтобы настроить эти параметры, выберите значок шестеренки справа от раскрывающегося списка и затем выберите **New Preset**(Новая предустановка).

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Настройка предустановок кодировщика.

    Задайте имя набора параметров и проверьте следующие рекомендуемые параметры:

    **Видео**

   * Кодировщик: MainConcept H.264
   * Кадров в секунду: 30
   * Средняя скорость: 5000 кбит/сек (это значение можно изменить в зависимости от ограничений сети)
   * Профиль: основной
   * Ключевой кадр: каждые 60 кадров

    **Звук:**

   * Конечная скорость: 192 Кбит/сек
   * Частота выборки: 44,100 кГц

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Нажмите кнопку **Save**(Сохранить).

    Созданный профиль теперь доступен для выбора в поле «Профиль кодировки».

    Убедитесь, что выбран новый профиль.
7. Получите входной URL-адрес канала, чтобы назначить его **конечной точке RTMP**Wirecast.

    Перейдите обратно в средство AMSE и проверьте состояние запуска канала. После изменения состояния **Starting** (Запуск) на **Running** (Выполняется) можно получить URL-адрес входных данных.

    Когда канал запустится, щелкните правой кнопкой мыши его имя, перейдите вниз к элементу **Copy Input URL to clipboard** (Скопировать входной URL-адрес в буфер обмена) и выберите пункт **Primary Input URL** (Основной входной URL-адрес).  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. В окне Wirecast **Output Settings** (Параметры вывода) вставьте этот адрес в поле **Address** (Адрес) раздела параметров вывода и укажите имя потока.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Нажмите кнопку **ОК**.
2. На главном экране **Wirecast** подтвердите готовность источников ввода для видео и звука, а затем щелкните **Stream** (Потоковая передача) в верхнем левом углу.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Прежде чем щелкнуть **Stream** (Потоковая передача), **необходимо** убедиться, что канал готов.
> Кроме того, не оставляйте канал в состоянии готовности без входного потока более 15 минут.
>
>

## <a name="test-playback"></a>Проверка воспроизведения

Перейдите в средство AMSE и щелкните правой кнопкой мыши канал, который необходимо проверить. В меню наведите указатель мыши на пункт **Playback the Preview** (Воспроизведение для предварительного просмотра) и выберите пункт **with Azure Media Player** (с помощью Проигрывателя мультимедиа Azure).  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Если поток отображается в проигрывателе, то кодировщик правильно настроен для подключения к AMS.

При получении сообщения об ошибке необходимо сбросить канал и настроить параметры кодировщика. Дополнительные сведения см. в руководстве по [устранению неполадок](media-services-troubleshooting-live-streaming.md).  

## <a name="create-a-program"></a>Создание программы
1. После проверки воспроизведения канала создайте программу. На вкладке **Live** (Передача) в инструменте AMSE щелкните правой кнопкой мыши в области программы и выберите команду **Create New Program** (Создать программу).  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Укажите имя программы и при необходимости измените параметр **Archive Window Length** (Длительность периода архивации); по умолчанию она составляет четыре часа. Также можно указать расположение для хранения или оставить значение по умолчанию.  
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
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
