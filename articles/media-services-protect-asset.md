<properties 
	pageTitle="Как шифровать ресурсы в службах мультимедиа - Azure" 
	description="Сведения об использовании защиты Microsoft PlayReady для шифрования активов в службах мультимедиа. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Практическое руководство. Динамическое шифрование ресурса с помощью PlayReady или AES-128

Это одна из статей серии [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md) и серии [Рабочий процесс для потоковой передачи в службах мультимедиа](media-services-live-streaming-workflow.md).
  
##Обзор

Службы мультимедиа Microsoft Azure позволяют доставлять содержимое, зашифрованное (динамически) с помощью AES (с использованием 128-битных ключей шифрования) и PlayReady DRM. Они также обеспечивают службы доставки ключей и лицензий PlayReady авторизованным клиентам. Для доставки защищенного содержимого необходимо настроить политику авторизации ключа содержимого и политики доставки ресурсов.

##Настройка

Дополнительная информация о настройке политики авторизации ключа содержимого 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Настройка политик доставки ресурсов
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 


<!--HONumber=52-->