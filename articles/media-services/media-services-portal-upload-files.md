---
title: " Отправка файлов в учетную запись служб мультимедиа с помощью портала Azure | Документация Майкрософт"
description: "В этом руководстве описаны этапы отправки файлов в учетную запись служб мультимедиа Azure с помощью портала Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8daf63e164dc548023878af091491b5d942276d9


---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Отправка файлов в учетную запись служб мультимедиа с помощью портала Azure
> [!div class="op_single_selector"]
> * [Портал](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

В службах мультимедиа цифровые файлы отправляются в актив. Ресурс может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (и метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.

1. Создание учетной записи служб мультимедиа Azure с помощью [портала Azure](https://portal.azure.com/).
2. В колонке **Параметры** выберите **Ресурсы**.
   
    ![Отправка файлов](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Нажмите кнопку **Отправить** .
   
    Появится окно **загрузки видеоресурса** .
   
   > [!NOTE]
   > Размер файлов неограничен.
   > 
   > 
4. Найдите нужное видео на компьютере, выберите его и нажмите кнопку "ОК".  
   
    Начнется передача. За ходом загрузки можно наблюдать под именем файла.  

По завершении загрузки в окне **Ресурсы** появится новый ресурс. 

## <a name="next-steps"></a>Дальнейшие действия
Теперь можно закодировать отправленные ресурсы. Дополнительную информацию см. в статье, посвященной [кодированию ресурсов](media-services-portal-encode.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO2-->


