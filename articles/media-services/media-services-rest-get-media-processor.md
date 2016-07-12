<properties 
	pageTitle="Создание обработчика мультимедиа | Microsoft Azure" 
	description="Узнайте, как создать компонент обработчика мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure." 
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
	ms.date="06/22/2016" 
	ms.author="juliako"/>


#Получение экземпляра процессора мультимедиа


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##Обзор

В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

Имя обработчика мультимедиа|Описание|Дополнительные сведения
---|---|---
Стандартный кодировщик служб мультимедиа|Предоставляет стандартные возможности для кодирования по запросу. |[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md)
Расширенный рабочий процесс кодировщика мультимедиа|Позволяет выполнять задачи кодирования с использованием расширенного рабочего процесса кодировщика мультимедиа.|[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md)
Azure Media Indexer| Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (предварительная версия)|Позволяет сгладить "неровности" видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Кодировщик мультимедиа Azure|Цена снижена
Расшифровка хранилища| Цена снижена|
Azure Media Packager|Цена снижена|
Azure Media Encryptor|Цена снижена|

##Получение MediaProcessor

>[AZURE.NOTE] При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](media-services-rest-how-to-use.md).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Последующие вызовы необходимо осуществлять к новому универсальному коду ресурса (URI), как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect-programmatically.md).


Следующий вызов REST показывает, как получить экземпляр обработчика мультимедиа по имени (в данном случае это **стандартный кодировщик мультимедиа**).



	
Запрос:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer <token>
	x-ms-version: 2.11
	Host: media.windows.net
	
Ответ:
		
	. . .
	
	{  
	   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "Description":"Media Encoder Standard",
	         "Name":"Media Encoder Standard",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"1.1"
	      }
	   ]
	}


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Дальнейшие действия

Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива](media-services-rest-get-started.md), в котором будет показано, как использовать стандартный кодировщик мультимедиа для кодирования ресурса-контейнера.

<!---HONumber=AcomDC_0629_2016-->