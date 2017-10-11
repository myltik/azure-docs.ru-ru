---
title: "Масштабирование обработки мультимедийных данных путем добавления единиц кодирования в Azure | Документация Майкрософт"
description: "Информация о добавлении единиц кодирования с помощью .NET"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 72a8729d22a9e76c8076d7a3347619a2163e4f09
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
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

## <a name="overview"></a>Обзор
> [!IMPORTANT]
> Обязательно ознакомьтесь с этим [обзором](media-services-scale-media-processing-overview.md) , чтобы получить дополнительные сведения о масштабировании обработки мультимедиа.
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
По умолчанию каждая учетная запись служб мультимедиа может масштабироваться до 25 зарезервированных единиц кодирования и 5 зарезервированных единиц потоковой передачи по требованию. Вы можете запросить более высокий предел, открыв запрос в службу поддержки.

### <a name="open-a-support-ticket"></a>Открытие запроса в службу поддержки
Чтобы открыть запрос в службу поддержки, выполните следующее:

1. Щелкните [Получить поддержку](https://manage.windowsazure.com/?getsupport=true). Если вы не выполнили вход, будет предложено ввести учетные данные.
2. Выберите свою подписку.
3. В группе типа поддержки выберите "Техническая".
4. Щелкните "Создать запрос в службу поддержки".
5. Выберите "Службы мультимедиа Azure" в списке продуктов, представленных на следующей странице.
6. Выберите соответствующее значение в поле "Тип проблемы".
7. Нажмите кнопку "Продолжить".
8. Следуйте указаниям на следующей странице, а затем введите сведения о проблеме.
9. Нажмите кнопку "Отправить" для создания запроса в службу поддержки.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

