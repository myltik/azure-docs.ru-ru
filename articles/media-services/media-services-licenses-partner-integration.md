<properties 
	pageTitle="Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure" 
	description="В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки потока, который зашифрован динамически службой AMS, с помощью лицензий DRM PlayReady и Widevine. Лицензию PlayReady выдает сервер лицензирования служб мультимедиа PlayReady, а лицензию Widevine — сервер лицензирования castLabs." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="juliako"/>

#Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure

##Обзор

Службы мультимедиа Microsoft Azure позволяет доставлять MPEG-DASH c защитой по технологии Widevine DRM, который соответствует спецификации общего шифрования (CENC).

Начиная с версии 3.5.2 пакета SDK служб мультимедиа для .NET, службы мультимедиа позволяют настраивать шаблоны лицензии Widevine и получать лицензии Widevine. Для доставки лицензий Widevine можно использовать следующие партнеры AMS: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) и [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Для доставки лицензий Widevine можно использовать [castLabs](http://castlabs.com/company/partners/azure/). Дополнительные сведения см. в статье [Использование castLabs для доставки лицензий DRM в службы мультимедиа Azure](media-services-castlabs-integration.md).

##Axinom

Для доставки лицензий Widevine можно использовать [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/). Дополнительные сведения см. в статье [Использование Axinom для доставки лицензий DRM в службы мультимедиа Azure](media-services-axinom-integration.md).


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##См. также

[Дополнительные сведения см. в статье Использование общего динамического шифрования PlayReady и (или) Widevine DRM.](media-services-protect-with-drm.md)

[Блог по Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=AcomDC_0420_2016-->