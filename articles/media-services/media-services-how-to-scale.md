<properties 
	pageTitle="Масштабирование службы мультимедиа" 
	description="Узнайте, как можно масштабировать службы мультимедиа, указав число зарезервированных единиц потоковой передачи по требованию и зарезервированных единиц кодирования, которые требуется подготовить для вашей учетной записи." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
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


#Масштабирование службы мультимедиа  

##Обзор

Можно масштабировать **службы мультимедиа**, указав число **зарезервированных единиц потоковой передачи** и **зарезервированных единиц кодирования**, которые необходимо использовать при подготовке учетной записи.

Можно также масштабировать учетную запись служб мультимедиа, добавляя к ней учетные записи хранения. Каждая учетная запись хранения ограничена 500 ТБ. Чтобы увеличить емкость хранилища за пределы ограничений по умолчанию, можно присоединить несколько учетных записей хранения для одной учетной записи служб мультимедиа.

Этот раздел содержит ссылки на соответствующие разделы.

##<a id="streaming_endpoins"></a>Зарезервированные единицы потоковой передачи

Дополнительную информацию см. в разделе [Масштабирование единиц потоковой передачи](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="encoding_reserved_units"></a>Зарезервированные единицы кодирования

Информацию о масштабировании единиц кодирования см. в следующих статьях о **портале** и **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

Обратите внимание, что зарезервированные единицы одинаковы для задач кодирования и индексирования.

##<a id="storage"></a>Масштабирование хранилища

Дополнительную информацию см. в разделах [Управление активами служб мультимедиа в нескольких учетных записях хранения](https://msdn.microsoft.com/library/azure/dn271889.aspx) и [Работа со службой хранилища Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx).

##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0420_2016-->