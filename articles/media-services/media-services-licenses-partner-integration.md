<properties 
	pageTitle="Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure" 
	description="В этой статье описывается использование служб мультимедиа Azure (AMS) для доставки потока, который зашифрован динамически службой AMS, с помощью лицензий DRM PlayReady и Widevine. Лицензию PlayReady выдает сервер лицензирования служб мультимедиа PlayReady, а лицензию Widevine — сервер лицензирования castLabs." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#Использование партнеров для доставки лицензий Widevine для служб мультимедиа Azure

##Обзор

Службы мультимедиа Microsoft Azure позволяет доставлять MPEG-DASH c защитой по технологии Widevine DRM, который соответствует спецификации общего шифрования (CENC).

>[AZURE.NOTE]В настоящее время службы мультимедиа не предоставляют сервер лицензирования Widevine. Вы можете использовать следующих партнеров AMS для доставки лицензий Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) и [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Для доставки лицензий Widevine можно использовать [castLabs](http://castlabs.com/company/partners/azure/). Дополнительные сведения см. в статье [Использование castLabs для доставки лицензий DRM в службы мультимедиа Azure](media-services-castlabs-integration.md)

##Axinom

Для доставки лицензий Widevine можно использовать [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/). Дополнительные сведения см. в статье [Использование Axinom для доставки лицензий DRM в службы мультимедиа Azure](media-services-axinom-integration.md).


##Схемы обучения работе со службами мультимедиа

Схемы обучения AMS можно просмотреть здесь:

- [Рабочий процесс для потоковой передачи в реальном времени в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Рабочий процесс для потоковой передачи по запросу в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##См. также

Дополнительные сведения см. в статье [Использование общего динамического шифрования PlayReady и (или) Widevine DRM](media-services-protect-with-drm.md).

[Блог по Mingfei](https://azure.microsoft.com/RU-RU/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Oct15_HO2-->