---
title: Масштабирование обработки мультимедиа с помощью портала Azure | Документация Майкрософт
description: В этом руководстве описывается, как масштабировать обработку данных мультимедиа с помощью портала Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 51973916c97282ac93032ab833402d9d1356647e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785783"
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

> [!NOTE]
> Чтобы получить последнюю версию пакета SDK для Java и приступить к разработке с помощью Java, перейдите к статье [Начало работы с клиентским пакетом SDK для Java для служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Чтобы загрузить последний пакет SDK для PHP для служб мультимедиа, найдите версию 0.5.7 пакета Microsoft/WindowAzure в [репозитории Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Обзор

Учетная запись служб мультимедиа связана с типом зарезервированных единиц, который определяет скорость обработки задач обработки мультимедиа. Вы можете выбрать один из следующих типов зарезервированных единиц: **S1**, **S2** или **S3**. Например, если использовать тип зарезервированной единицы **S2**, задание по кодированию выполняется быстрее по сравнению заданием, для которого выбран тип **S1**.

Помимо указания типа зарезервированных единиц, можно также указать параметр, позволяющий использовать **зарезервированные единицы** при подготовке учетной записи. Количеством подготовленных зарезервированных единиц определяется количество задач мультимедиа, которые могут одновременно обрабатываться в данной учетной записи.

>[!NOTE]
>Зарезервированные единицы позволяют распараллелить всю обработку мультимедиа, включая задания индексирования с использованием индексатора мультимедийных данных Azure. Однако в отличие от кодировки, задания индексирования не будут обрабатываться быстрее при использовании более производительных зарезервированных единиц.

> [!IMPORTANT]
> Обязательно ознакомьтесь с этим [обзором](media-services-scale-media-processing-overview.md) , чтобы получить дополнительные сведения о масштабировании обработки мультимедиа.
> 
> 

## <a name="scale-media-processing"></a>Масштабирование обработки мультимедиа
Чтобы изменить тип зарезервированных единиц и число зарезервированных единиц, выполните следующие действия:

1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. В окне **Параметры** выберите **Зарезервированные единицы мультимедиа**.
   
    Чтобы изменить число зарезервированных единиц для выбранного типа зарезервированных единиц, используйте ползунок **Media Served Units** (Зарезервированные единицы мультимедиа) в верху экрана.
   
    Чтобы изменить параметр **ТИП ЗАРЕЗЕРВИРОВАННЫХ ЕДИНИЦ**, щелкните строку **Скорость зарезервированных единиц обработки**. Затем выберите нужную ценовую категорию: S1, S2 или S3.
   
3. Нажмите кнопку СОХРАНИТЬ, чтобы сохранить изменения.
   
    Новые зарезервированные единицы выделяются сразу после нажатия кнопки "СОХРАНИТЬ".

## <a name="next-steps"></a>Дополнительная информация
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

