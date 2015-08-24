<properties 
	pageTitle="Создание обработчика мультимедиа | Microsoft Azure" 
	description="Узнайте, как создать компонент обработчика мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure." 
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
	ms.date="08/11/2015" 
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
Кодировщик мультимедиа Azure|Позволяет выполнять задачи кодирования с использованием кодировщика мультимедиа Azure.|[Кодировщик мультимедиа Azure](media-services-encode-asset.md#azure_media_encoder)
Стандартный кодировщик служб мультимедиа|Позволяет выполнять задачи кодирования с использованием стандартного кодировщика мультимедиа.|[Кодировщик мультимедиа Azure](media-services-encode-asset.md#media_encoder_standard)
Расширенный рабочий процесс кодировщика мультимедиа|Позволяет выполнять задачи кодирования с использованием расширенного рабочего процесса кодировщика мультимедиа.|[Расширенный рабочий процесс кодировщика мультимедиа](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.|[Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure](media-services-index-content.md)
Azure Media Hyperlapse (предварительная версия)|Позволяет сгладить «неровности» видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа.|		[Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Расшифровка хранилища| Позволяет расшифровать мультимедийные активы, которые были зашифрованы с помощью шифрования хранилища.|Недоступно
Microsoft Azure Media Packager|Позволяет преобразовать мультимедийные активы из формата MP4 в формат Smooth Streaming. Позволяет также преобразовать мультимедийные активы из формата Smooth Streaming в формат Apple HTTP Live Streaming (HLS).|[Строки предустановок задачи для Azure Media Packager](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|Позволяет шифровать мультимедийные активы с использованием PlayReady Protection.|[Строки предустановок задачи для Azure Media Packager](http://msdn.microsoft.com/library/hh973610.aspx)

##Получение MediaProcessor

>[AZURE.NOTE]При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](media-services-rest-how-to-use.md).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Последующие вызовы необходимо осуществлять к новому URI, как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect_programmatically.md).



Следующий вызов REST показывает, как получить экземпляр обработчика мультимедиа по имени (в данном случае это **кодировщик мультимедиа Azure**).

	
Запрос:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
Ответ:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


##Дальнейшие действия
Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива][], в котором будет показано, как использовать кодировщик Azure Media для кодирования актива.

[Кодировка актива]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=August15_HO7-->