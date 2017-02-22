---
title: " Масштабирование обработки мультимедиа с помощью портала Azure | Документация Майкрософтs"
description: "В этом учебнике пошагово описано, как масштабировать обработку мультимедиа с помощью портала Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 3f9134fdff56e0ee5146cb7ccf08d168ebf84281


---
# <a name="change-the-reserved-unit-type"></a>Изменение типа зарезервированных единиц
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Портал](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Обзор
> [!IMPORTANT]
> Обязательно ознакомьтесь с этим [обзором](media-services-scale-media-processing-overview.md) , чтобы получить дополнительные сведения о масштабировании обработки мультимедиа.
> 
> 

## <a name="scale-media-processing"></a>Масштабирование обработки мультимедиа
Чтобы изменить тип зарезервированных единиц и число зарезервированных единиц, выполните следующие действия:

1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. В окне **Параметры** выберите **Зарезервированные единицы мультимедиа**.
   
    Чтобы изменить число зарезервированных единиц для выбранного типа зарезервированных единиц, используйте ползунок **Media Served Units** (Зарезервированные единицы мультимедиа).
   
    Чтобы изменить **ТИП ЗАРЕЗЕРВИРОВАННЫХ ЕДИНИЦ**, выберите S1, S2 или S3.
   
    ![Страница "Процессоры"](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. Нажмите кнопку СОХРАНИТЬ, чтобы сохранить изменения.
   
    Новые зарезервированные единицы выделяются сразу после нажатия кнопки "СОХРАНИТЬ".

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


