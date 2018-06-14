---
title: Настройка кодировщика NewTek TriCaster для отправки односкоростного обновляющегося потока | Документация Майкрософт
description: В этой статье показано, как настроить кодировщик Tricaster для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в реальном времени.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 8084f32ac8cc2184d93796468ad66fb73398e876
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783783"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Использование кодировщика NewTek TriCaster для отправки односкоростного обновляющегося потока
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

В этой статье показано, как настроить динамический кодировщик [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в реальном времени. Дополнительные сведения можно найти в разделе [Работа с каналами, выполняющими кодирование в реальном времени с помощью служб мультимедиа Azure](media-services-manage-live-encoder-enabled-channels.md).

В этом учебнике показано, как управлять службами мультимедиа Azure (AMS) с помощью Обозревателя служб мультимедиа Azure. Это средство запускается только на компьютерах с ОС Windows. Если вы используете Mac или Linux, воспользуйтесь классическим порталом Azure для создания [каналов](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) и [программ](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> При использовании Tricaster для отправки веб-канала доставки в каналы AMS с поддержкой кодирования в реальном времени возможны временные сбои видео или звука интерактивного события, если используются некоторые возможности Tricaster, например быстрый монтаж разных веб-каналов или переключение на баннеры и обратно. Команда AMS работает над устранением этих проблем. Сейчас не рекомендуется использовать эти возможности.
>
>

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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Укажите имя канала, поле описания является необязательным. В разделе Channel Settings ("Параметры каналов") для параметра Live Encoding ("Кодирование в реальном времени") установите значение **Standard** ("Стандартное"), а для параметра Input Protocol ("Входной протокол") — значение **RTMP**. Остальные параметры можно оставить без изменений.

    Убедитесь, что флажок **Start the new channel now** (Сразу запустить новый канал) установлен.

3. Щелкните **Создать канал**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> На запуск канала может потребоваться до 20 минут.
>
>

Во время запуска канала можно [настроить кодировщик](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Тарификация начинается сразу же после перехода канала в состояние готовности. Дополнительные сведения см. в разделе о [состояниях каналов](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_tricaster_rtmp></a>Настройка кодировщика NewTek TriCaster
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
1. Создайте новый проект **NewTek TriCaster** в зависимости от того, какой входной источник видео используется.
2. Создав проект, найдите кнопку **Stream** (Потоковая передача) и щелкните значок шестеренки рядом с ним для доступа к меню настройки потока.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Открыв меню, щелкните **New** (Создать) под заголовком «Connection» (Подключение). При появлении запроса для типа подключения выберите **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Последовательно выберите **ОК**.
5. Профиль FMLE теперь можно импортировать, щелкнув стрелку в раскрывающемся списке **Streaming Profile** (Профиль потоковой передачи) и выбрав пункт **Browse** (Обзор).

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Перейдите в каталог, в котором был сохранен настроенный профиль FMLE.
7. Выберите его и нажмите кнопку **ОК**.

    После загрузки профиля перейдите к следующему шагу.
8. Получите входной URL-адрес канала, чтобы назначить его **конечной точке RTMP**Tricaster.

    Перейдите обратно в средство AMSE и проверьте состояние запуска канала. После изменения состояния **Starting** (Запуск) на **Running** (Выполняется) можно получить URL-адрес входных данных.

    Когда канал запустится, щелкните правой кнопкой мыши его имя, перейдите вниз к элементу **Copy Input URL to clipboard** (Скопировать входной URL-адрес в буфер обмена) и выберите пункт **Primary Input URL** (Основной входной URL-адрес).  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Вставьте этот адрес в поле **Location** (Расположение) в разделе **Flash Server** (Сервер Flash Server) в проекте Tricaster. Также укажите имя потока в поле **Stream ID** (Идентификатор потока).

    Если в профиль FMLE добавлена информация о потоке, ее также можно импортировать в этот раздел. Для этого нажмите кнопку **Import Settings** (Импорт параметров), перейдите к сохраненному профилю FMLE и нажмите кнопку **ОК**. Соответствующие поля Flash Server должны заполниться информацией от FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. По завершении нажмите кнопку **ОК** в нижней части окна. Когда входные видео и звук для Tricaster будут готовы, начните потоковую передачу в AMS, нажав кнопку **Stream** (Потоковая передача).

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Прежде чем щелкнуть **Stream** (Потоковая передача), **необходимо** убедиться, что канал готов.
> Кроме того, не оставляйте канал в состоянии готовности без входного потока более 15 минут.
>
>

## <a name="test-playback"></a>Проверка воспроизведения
Перейдите в средство AMSE и щелкните правой кнопкой мыши канал, который необходимо проверить. В меню наведите указатель мыши на пункт **Playback the Preview** (Воспроизведение для предварительного просмотра) и выберите пункт **with Azure Media Player** (с помощью Проигрывателя мультимедиа Azure).  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Если поток отображается в проигрывателе, то кодировщик правильно настроен для подключения к AMS.

При получении ошибки необходимо сбросить канал и настроить параметры кодировщика. Дополнительные сведения см. в руководстве по [устранению неполадок](media-services-troubleshooting-live-streaming.md).  

## <a name="create-a-program"></a>Создание программы
1. После проверки воспроизведения канала создайте программу. На вкладке **Live** (Передача) в инструменте AMSE щелкните правой кнопкой мыши в области программы и выберите команду **Create New Program** (Создать программу).  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
