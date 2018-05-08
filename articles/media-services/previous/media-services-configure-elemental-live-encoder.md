---
title: Настройка кодировщика Elemental Live для отправки односкоростного обновляющегося потока | Документация Майкрософт
description: В этой статье показано, как настроить кодировщик Elemental Live для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в реальном времени.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 35d8d0438d548e679405aefcbd1982fa1731874a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Использование кодировщика Elemental Live для отправки односкоростного обновляющегося потока
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

В этой статье показано, как настроить кодировщик [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) для отправки односкоростного потока в каналы AMS, которые выполняют кодирование в реальном времени.  Дополнительные сведения можно найти в разделе [Работа с каналами, выполняющими кодирование в реальном времени с помощью служб мультимедиа Azure](media-services-manage-live-encoder-enabled-channels.md).

В этом учебнике показано, как управлять службами мультимедиа Azure (AMS) с помощью Обозревателя служб мультимедиа Azure. Это средство запускается только на компьютерах с ОС Windows. Если вы используете Mac или Linux, воспользуйтесь классическим порталом Azure для создания [каналов](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) и [программ](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>предварительным требованиям
* Вы должны иметь практические знания по созданию событий в реальном времени с помощью веб-интерфейса Elemental Live.
* [Создайте учетную запись служб мультимедиа Azure](media-services-portal-create-account.md).
* Убедитесь, что запущена конечная точка потоковой передачи. Дополнительные сведения см. в статье об [управлении конечными точками потоковой передачи с помощью учетной записи служб мультимедиа](media-services-portal-manage-streaming-endpoints.md).
* Установите последнюю версию средства [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Запустите его и подключитесь к учетной записи AMS.

## <a name="tips"></a>Советы
* По возможности используйте проводное подключение к Интернету.
* Для получения необходимой пропускной способности рекомендуется удвоить скорость потоковой передачи. Хотя это требование не является обязательным, оно поможет уменьшить влияние перегрузки сети на пропускную способность.
* При использовании программных кодировщиков закройте все ненужные программы.

## <a name="elemental-live-with-rtp-ingest"></a>Использование Elemental Live с протоколом RTP
В этом разделе показано, как настроить кодировщик Elemental Live, который отправляет односкоростной обновляющийся поток по протоколу RTP.  Дополнительные сведения см. в подразделе, посвященному [передаче потока MPEG-TS по протоколу RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Создание канала

1. В средстве AMSE перейдите на вкладку **Поток** и щелкните правой кнопкой мыши в области канала. Выберите **Создать канал...** в меню.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Укажите имя канала, поле описания является необязательным. В разделе Channel Settings ("Параметры каналов") для параметра Live Encoding ("Кодирование в реальном времени") установите значение **Standard** (Стандартное), а для параметра Input Protocol ("Входной протокол") — значение **RTP (MPEG-TS)**. Остальные параметры можно оставить без изменений.

    Убедитесь, что флажок **Start the new channel now** (Сразу запустить новый канал) установлен.

3. Щелкните **Создать канал**.

   ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> На запуск канала может потребоваться до 20 минут.
>
>

Во время запуска канала можно [настроить кодировщик](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Обратите внимание, что тарификация начинается сразу же после перехода канала в состояние готовности. Дополнительные сведения см. в разделе о [состояниях каналов](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Настройка кодировщика Elemental Live
В этом руководстве используются следующие параметры вывода. В оставшейся части этого раздела этапы настройки описываются более подробно.

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

#### <a name="configuration-steps"></a>Этапы настройки
1. Перейдите в веб-интерфейс **Elemental Live** и настройте кодировщик для потоковой передачи **UDP/TS**.
2. После создания нового события прокрутите содержимое окна вниз к группам вывода и добавьте группу вывода **UDP/TS** .
3. Создайте выходные данные, щелкнув **New Stream** (Новый поток), а затем **Add Output** (Добавить выходные данные).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Для события Elemental рекомендуется установить код времени в значение «Системные часы». Это позволит кодировщику выполнить повторное подключение в случае сбоя потока.
   >
   >
4. После создания выхода щелкните **Добавить поток**. Теперь можно настроить параметры выхода.
5. Прокрутите содержимое окна вниз до только что созданного потока Stream 1. Щелкните вкладку **Video** (Видео) слева и разверните раздел **Advanced** (Дополнительные параметры).

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Хотя Elemental Live имеет широкий спектр доступных настроек, для потоковой передачи с AMS рекомендуются следующие параметры.

   * Разрешение: 1280 x 720
   * Частота кадров: 30
   * Размер GOP: 60 кадров
   * Режим развертки: прогрессивная
   * Скорость: 5000000 бит/сек (это значение можно изменить в зависимости от ограничений сети)

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Получение входного URL-адреса канала

    Перейдите обратно в средство AMSE и проверьте состояние запуска канала. После изменения состояния **Starting** (Запуск) на **Running** (Выполняется) можно получить URL-адрес входных данных.

    Когда канал запустится, щелкните правой кнопкой мыши его имя, перейдите вниз к элементу **Copy Input URL to clipboard** (Скопировать входной URL-адрес в буфер обмена) и выберите пункт **Primary Input URL** (Основной входной URL-адрес).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Вставьте этот адрес в поле **Основное назначение** Elemental. Все остальные параметры можно оставить без изменений.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Для обеспечения дополнительной надежности повторите эти действия с полем «Дополнительный входной URL-адрес», создав отдельную вкладку «Выход» для потоковой передачи UDP/TS.
3. Щелкните **Create** (Создать) (если событие создано) или **Update** (Обновить) (при изменении имеющегося события), а затем запустите кодировщик.

> [!IMPORTANT]
> Прежде чем нажать кнопку **Start** (Запустить) в веб-интерфейсе Elemental Live, **необходимо** убедиться, что канал находится в состоянии готовности.
> Кроме того, не оставляйте канал в состоянии готовности более 15 минут.
>
>

После отправки потока в течение 30 секунд, вернитесь к средству AMSE и проверьте воспроизведение.  

### <a name="test-playback"></a>Проверка воспроизведения

Перейдите в средство AMSE и щелкните правой кнопкой мыши на канале, который необходимо проверить. В меню наведите указатель мыши на пункт **Playback the Preview** (Воспроизведение для предварительного просмотра) и выберите пункт **with Azure Media Player** (с помощью Проигрывателя мультимедиа Azure).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Если поток отображается в проигрывателе, то кодировщик правильно настроен для подключения к AMS.

При получении ошибки необходимо сбросить канал и настроить параметры кодировщика. Рекомендации см. в статье, посвященной [устранению неполадок](media-services-troubleshooting-live-streaming.md).   

### <a name="create-a-program"></a>Создание программы
1. После проверки воспроизведения канала создайте программу. На вкладке **Live** (Передача) в инструменте AMSE щелкните правой кнопкой мыши в области программы и выберите команду **Create New Program** (Создать программу).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Укажите имя программы и при необходимости измените параметр **Archive Window Length** (Длительность окна архивации) (по умолчанию она составляет 4 часа). Также можно указать расположение для хранения или оставить значение по умолчанию.  
3. Установите флажок **Start the Program now** (Запустить программу сейчас).
4. Щелкните **Create Program**(Создать программу).  

    >[!NOTE]
    > Создание программы занимает меньше времени, чем создание канала.   
      
5. После запуска программы проверьте воспроизведение. Для этого щелкните правой кнопкой мыши программу, перейдите к пункту **Playback the program(s)** (Воспроизвести программы) и выберите пункт **with Azure Media Player** (с помощью Проигрывателя мультимедиа Azure).  
6. После проверки снова щелкните программу правой кнопкой мыши и выберите команду **Copy the Output URL to Clipboard** (Копировать выходной URL-адрес в буфер обмена) или получите соответствующий адрес, используя пункт меню **Program information and settings** (Сведения о программе и параметры).

Теперь поток можно внедрить в проигрыватель или транслировать аудитории для просмотра в режиме реального времени.  

## <a name="troubleshooting"></a>Устранение неполадок
Рекомендации см. в статье, посвященной [устранению неполадок](media-services-troubleshooting-live-streaming.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
