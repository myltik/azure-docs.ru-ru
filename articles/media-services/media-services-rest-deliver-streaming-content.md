<properties 
	pageTitle="Доставка содержимого потоковой передачи из служб мультимедиа" 
	description="Узнайте, как создать указатель для создания URL-адреса потоковой передачи. Код использует REST API." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015"  
	ms.author="juliako"/>


#Доставка потокового контента

> [AZURE.SELECTOR]
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)
- [Portal](media-services-manage-content.md#publish)

##Обзор


Вы можете осуществлять потоковую передачу набора MP4-файлов с адаптивной скоростью, создавая указатель потоковой передачи OnDemand и формируя URL-адрес потоковой передачи. В разделе, посвященном [кодированию ресурса](media-services-rest-encode-asset.md), описано кодирование данных в набор MP4-файлов с адаптивной скоростью. Если содержимое шифруется, перед созданием указателя настройте политику доставки ресурсов (как описано в [этой](media-services-rest-configure-asset-delivery-policy.md) статье).

Можно также использовать указатель потоковой передачи OnDemand, чтобы формировать URL-адреса, указывающие на MP4-файлы для последовательного скачивания.

В этом разделе показано, как создать указатель потоковой передачи OnDemand, чтобы опубликовать ресурс и сформировать URL-адреса потоковой передачи Smooth, MPEG DASH и HLS. В нем также показывается, как создать URL-адреса последовательного скачивания.

В [этих](#types) разделах показаны типы перечисления, значения которых используются в вызовах REST.
  
##Создание указателя потоковой передачи OnDemand

Чтобы создать указатель потоковой передачи OnDemand и получить URL-адреса, вам нужно выполнить следующее:


   1. Если содержимое шифруется, установите политику доступа.
   2. создать указатель потоковой передачи OnDemand.
   3. Если планируется потоковая передача, получите файл манифеста потоковой передачи (ISM-файл) в ресурсе. 
   		
	Если вы планируете последовательное скачивание, получите имена MP4-файлов в ресурсе. 
   4. Создайте URL-адрес файла манифеста или URL-адреса MP4-файлов. 


###Создание политики доступа

Запрос:
		
	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	Host: media.windows.net
	Content-Length: 68
	
	{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
	
Ответ:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 311
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
	Server: Microsoft-IIS/8.5
	request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:52:09 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

###Создание указателя потоковой передачи OnDemand

Создание указателя для указанного ресурса и политики активов.

Запрос:
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.11
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	Host: media.windows.net
	Content-Length: 181
	
	{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Ответ:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 637
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
	Server: Microsoft-IIS/8.5
	request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:58:37 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

###Создание URL-адресов потоковой передачи

Для построения URL-адресов Smooth, HLS и MPEG DASH используется значение **Путь**, возвращаемое после создания указателя.

Smooth Streaming: **Путь** + имя файла манифеста + "/manifest"

Пример:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Путь** + имя файла манифеста + "/manifest(format=m3u8-aapl)"

Пример:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Путь** + имя файла манифеста + "/manifest(format=mpd-time-csf)"


Пример:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


###Создание URL-адресов последовательного скачивания

Для построения URL-адреса поэтапного скачивания используется значение **Путь**, возвращаемое после создания указателя.

URL: **Путь** + имя MP4-файла ресурса

Пример:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

##<a id="types"></a>Типы перечислений

    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->