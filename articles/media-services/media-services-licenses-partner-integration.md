---
title: "Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure | Документация Майкрософт"
description: "В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки потока, который зашифрован динамически службой AMS, с помощью лицензий DRM PlayReady и Widevine. Лицензию PlayReady выдает сервер лицензирования служб мультимедиа PlayReady, а лицензию Widevine — сервер лицензирования castLabs."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b4211cef3b4f3ffa2c0c97fd7650606f3eef7008
ms.contentlocale: ru-ru
ms.lasthandoff: 11/17/2016

---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure
## <a name="overview"></a>Обзор
Службы мультимедиа Microsoft Azure позволяет доставлять MPEG-DASH c защитой по технологии Widevine DRM, который соответствует спецификации общего шифрования (CENC).

Начиная с версии 3.5.2 пакета SDK служб мультимедиа для .NET, службы мультимедиа позволяют настраивать шаблоны лицензии Widevine и получать лицензии Widevine. Для доставки лицензий Widevine можно использовать следующих партнеров AMS: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
Для доставки лицензий Widevine можно использовать [castLabs](http://castlabs.com/company/partners/azure/). Дополнительные сведения см. в статье [Использование castLabs для доставки лицензий DRM в службы мультимедиа Azure](media-services-castlabs-integration.md).

## <a name="axinom"></a>Axinom
Для доставки лицензий Widevine можно использовать [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/). Дополнительные сведения см. в статье [Использование Axinom для доставки лицензий DRM в службы мультимедиа Azure](media-services-axinom-integration.md).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Дополнительные материалы
[Дополнительные сведения см. в статье Использование общего динамического шифрования PlayReady и (или) Widevine DRM.](media-services-protect-with-drm.md)

[Блог по Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)


