---
title: "  Публикация содержимого с помощью портала Azure | Документация Майкрософт"
description: "В этом учебнике пошагово описано, как публиковать содержимое с помощью портала Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 403f15db2979a6626d5896ccc950f355f0a06a4e
ms.contentlocale: ru-ru
ms.lasthandoff: 08/08/2017

---
# <a name="publish-content-with-the-azure-portal"></a>Публикация содержимого с помощью портала Azure
> [!div class="op_single_selector"]
> * [Портал](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Обзор
> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Чтобы предоставить пользователю URL-ссылку, которую можно использовать для потоковой передачи или скачивания содержимого, сначала необходимо "опубликовать" ресурс-контейнер, создав указатель. Указатели предоставляют доступ к файлам, содержащимся в активе. Службы мультимедиа поддерживают два типа указателей: 

* Указатели потоковой передачи (OnDemandOrigin), используемые для потоковой передачи с переменной скоростью (например, для потоковой передачи в форматах MPEG DASH, HLS или Smooth Streaming). Чтобы создать указатель потоковой передачи, ресурс должен содержать ISM-файл. 
* Последовательные указатели (SAS), используемые для доставки видео путем последовательного скачивания.

URL-адрес потоковой передачи имеет следующий формат, и его можно использовать для воспроизведения ресурсов Smooth Streaming:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Чтобы создать URL-адрес потоковой передачи HLS, добавьте (format=m3u8-aapl) к URL-адресу.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Чтобы создать URL-адрес для потоковой передачи в формате MPEG DASH, добавьте к исходному адресу строку (format=mpd-time-csf).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

URL-адрес SAS имеет следующий формат.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Дополнительные сведения см. в [обзоре доставки содержимого](media-services-deliver-content-overview.md).

> [!NOTE]
> Для указателей, созданных до марта 2015 года, срок действия составляет два года.  
> 
> 

Чтобы обновить срок действия указателя, используйте [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) или [.NET API](http://go.microsoft.com/fwlink/?LinkID=533259). Обратите внимание, что при обновлении срока действия указателя SAS изменяется URL-адрес.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Публикация ресурса с помощью портала
Для публикации ресурса с помощью портала выполните следующие действия.

1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. Установите флажок **Параметры** > **Ресурсы-контейнеры**.
3. Выберите ресурс, который требуется опубликовать.
4. Нажмите кнопку **Опубликовать** .
5. Выберите тип указателя.
6. Нажмите кнопку **Добавить**.
   
    ![Опубликовать](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL-адрес будет добавлен в список **опубликованных URL-адресов**.

## <a name="play-content-from-the-portal"></a>Воспроизведение контента на портале
Портал Azure предлагает проигрыватель содержимого, с помощью которого можно проверить видео.

Выберите нужное видео и нажмите кнопку **Воспроизвести** .

![Опубликовать](./media/media-services-portal-vod-get-started/media-services-play.png)

Важные особенности

* Убедитесь, что видео опубликовано.
* Этот **проигрыватель мультимедиа** воспроизводит содержимое из конечной точки потоковой передачи по умолчанию. Если требуется воспроизвести из конечной точки потоковой передачи не по умолчанию, щелкните, чтобы скопировать URL-адрес, и используйте другой проигрыватель. Например, [Проигрыватель служб мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* Конечная точка потоковой передачи, из которой осуществляется потоковая передача, должна быть запущена.  

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


