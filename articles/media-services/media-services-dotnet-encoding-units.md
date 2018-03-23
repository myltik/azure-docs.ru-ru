---
title: Масштабирование обработки мультимедийных данных путем добавления единиц кодирования в Azure | Документация Майкрософт
description: Информация о добавлении единиц кодирования с помощью .NET
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 89203a9499b3624faf41b63f4ea6e7bd29f3f0c9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Масштабирование кодирования с помощью пакета SDK для .NET
> [!div class="op_single_selector"]
> * [Портал](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> Чтобы получить последнюю версию пакета SDK для Java и приступить к разработке с помощью Java, перейдите к статье [Начало работы с клиентским пакетом SDK для Java для служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Чтобы загрузить последний пакет SDK для PHP для служб мультимедиа, найдите версию 0.5.7 пакета Microsoft/WindowAzure в [репозитории Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Обзор
> [!IMPORTANT]
> Обязательно ознакомьтесь с этим [обзором](media-services-scale-media-processing-overview.md), чтобы получить дополнительные сведения о масштабировании обработки мультимедиа.
> 
> 

Чтобы изменить тип зарезервированных единиц и число зарезервированных единиц кодирования с помощью пакета SDK для .NET, сделайте следующее:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Открытие запроса в службу поддержки

По умолчанию каждая учетная запись Служб мультимедиа может масштабироваться до 10 зарезервированных единиц мультимедиа S2 или S3 либо 25 зарезервированных единиц мультимедиа S1 и до 5 зарезервированных единиц потоковой передачи по требованию. Вы можете запросить более высокий предел, открыв запрос в службу поддержки.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

