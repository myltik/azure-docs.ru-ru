---
title: Отправка файлов в учетную запись служб мультимедиа на портале Azure | Документация Майкрософт
description: В этом руководстве описываются действия по отправке файлов в учетную запись служб мультимедиа на портале Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 36e1f797263e367a73fde140d979243f96e83948
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33780557"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Отправка файлов в учетную запись служб мультимедиа на портале Azure
> [!div class="op_single_selector"]
> * [Портал](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. на странице с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 

В службах мультимедиа Azure цифровые файлы отправляются в ресурс. Ресурс может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (и метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи.

> [!NOTE]
> В службах мультимедиа установлен максимальный размер обрабатываемых файлов. Дополнительные сведения об ограничениях на размер файлов см. в статье [Квоты и ограничения](media-services-quotas-and-limitations.md).
>

## <a name="upload-files"></a>Отправка файлов
1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. Установите флажок **Параметры** > **Ресурсы-контейнеры**. Затем нажмите кнопку **Отправить**.
   
    ![Отправка файлов](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Появится окно **загрузки видеоресурса** .
   
   > [!NOTE]
   > В службах мультимедиа нет ограничения на размер файла при отправке видео.
 
3. На компьютере перейдите к видео, которое нужно отправить. Выберите видео и нажмите кнопку **ОК**.  
   
    Начнется отправка. Ход отправки отображается под именем файла.  

Когда отправка завершится, в области **Ресурсы-контейнеры** появится новый ресурс. 

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [закодировать отправленные ресурсы](media-services-portal-encode.md).

* Вы также можете использовать службу "Функции Azure", чтобы активировать задание кодирования, когда файл поступит в настроенный контейнер. Дополнительные сведения см. в примере в статье [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Службы мультимедиа: интеграция служб мультимедиа Azure со службой "Функции Azure" и Azure Logic Apps).


