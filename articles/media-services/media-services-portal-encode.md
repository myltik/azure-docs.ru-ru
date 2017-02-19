---
title: "Кодирование ресурса-контейнера с использованием Media Encoder Standard на портале Azure | Документация Майкрософт"
description: "Этот учебник поможет закодировать ресурс-контейнер с помощью Media Encoder Standard на портале Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 50e9095d38c96323db3ccce4e3601eccbb9eb2ab


---
# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Кодирование ресурса-контейнера с помощью Media Encoder Standard на портале Azure
> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

При работе со службами мультимедиа Azure один из самых частых сценариев — доставка потоковой передачи с адаптивным битрейтом клиентам. Службы мультимедиа поддерживают следующие технологии потоковой передачи с переменной скоростью: HTTP Live Streaming (HLS), Smooth Streaming и MPEG DASH. Чтобы подготовить видео к потоковой передаче с переменной скоростью, необходимо закодировать исходное видео в файлы с поддержкой разных скоростей. Для кодирования этого видео следует использовать **Media Encoder Standard** .  

Службы мультимедиа также обеспечивают динамическую упаковку, которая позволяет доставлять MP4-файлы с несколькими скоростями в форматах потоковой передачи (MPEG-DASH, HLS, Smooth Streaming) без необходимости повторной упаковки в эти форматы. С динамической упаковкой потребуется хранить и оплачивать файлы только в одном формате хранения, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента.

Чтобы воспользоваться динамической упаковкой, закодируйте исходный файл в набор MP4-файлов с поддержкой нескольких скоростей (шаги кодирования показаны далее в этом руководстве).

Масштабирование обработки мультимедиа описано в [этом](media-services-portal-scale-media-processing.md) разделе.

## <a name="encode-with-the-azure-portal"></a>Кодирование с помощью портала Azure
В этом разделе описаны шаги, которые можно предпринять для кодирования содержимого с помощью стандартного кодировщика служб мультимедиа.

1. Создание учетной записи служб мультимедиа Azure с помощью [портале Azure](https://portal.azure.com/).
2. В окне **Параметры** выберите элемент **Ресурсы**.  
3. В окне **Ресурсы-контейнеры** выберите ресурс, который требуется закодировать.
4. Нажмите кнопку **Кодировать** .
5. В окне**кодирования ресурса** выберите обработчик Media Encoder Standard с предустановкой. Например, если известно, что исходное видео имеет разрешение 1920 x 1080 пикселей, можно использовать предустановку "H264 Multiple Bitrate 1080p". Дополнительные сведения о предустановках см. [здесь](media-services-mes-presets-overview.md). Важно выбрать предустановку в соответствии с параметрами исходного видео. Если у вас есть видео с низким разрешением (640 x 360), не следует использовать предустановку по умолчанию ("H264 Multiple Bitrate 1080p").
   
   Для упрощения управления предусмотрена возможность изменения имени выходного ресурса и задания.
   
   ![Кодирование ресурсов](./media/media-services-portal-vod-get-started/media-services-encode1.png)
6. Нажмите кнопку **Создать**.

## <a name="next-step"></a>Дальнейшие действия
Можно отслеживать выполнение задания кодирования с помощью портала Azure, как описано в [этой](media-services-portal-check-job-progress.md) статье.  

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


