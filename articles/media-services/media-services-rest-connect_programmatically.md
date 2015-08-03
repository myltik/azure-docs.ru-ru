<properties 
	pageTitle="Подключение к учетной записи служб мультимедиа с помощью REST API" 
	description="В этом разделе показано, как подключиться к службам мультимедиа с помощью REST API." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="juliako"/>


# Подключение к учетной записи служб мультимедиа с помощью REST API служб мультимедиа

Это одна из статей циклов [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md) и [Рабочий процесс для потоковой передачи в службах мультимедиа](media-services-live-streaming-workflow.md).

В этом разделе описывается установка программного подключения к службам мультимедиа Microsoft Azure при программировании с помощью REST API служб мультимедиа.

При обращении к службам мультимедиа Microsoft Azure необходимы два элемента: маркер доступа, предоставленный Azure Access Control Services (ACS), и универсальный код ресурса самих служб мультимедиа. При создании этих запросов можно использовать любые средства. Главное — указать правильные значения заголовков и выполнить правильную передачу маркера доступа при вызове служб мультимедиа.

Ниже перечислены этапы наиболее распространенного рабочего процесса использования REST API служб мультимедиа для подключения к службам мультимедиа:

1. Получение маркера доступа 
2. Подключение к универсальному коду ресурса служб мультимедиа 

	>[AZURE.NOTE]После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Используйте для последующих вызовов новый URI. Кроме того, вы можете получить ответ 200 HTTP/1.1, содержащий описание метаданных API ODATA.

3. Последующие вызовы API будут отправляться на новый URL-адрес.

	Например, если после попытки подключения вы получите следующее:

		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	Необходимо опубликовать последующие вызовы API по адресу https://wamsbayclus001rest-hs.cloudapp.net/api/.

##Получение маркера доступа

Для прямого доступа к службам мультимедиа с помощью REST API необходимо получить маркер доступа из ACS и использовать его при каждом HTTP-запросе, отправляемом в службу. Этот маркер похож на другие маркеры, предоставляемые ACS на основе утверждений доступа в заголовке HTTP-запроса и использующие протокол OAuth версии 2. Для непосредственного подключения к службам мультимедиа не нужно выполнять другие предварительные требования.

В следующем примере показан заголовок и текст HTTP-запроса, используемого для получения маркера.

**Заголовок**:

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**Текст**:

В тексте этого запроса следует указать значения client_id и client_secret. Эти значения соответствуют значениям AccountName и AccountKey соответственно. Они предоставляются службами мультимедиа при настройке учетной записи.

Обратите внимание, что ключ AccountKey для учетной записи службы мультимедиа должен быть закодирован как URL-адрес (см. раздел [Percent-Encoding](http://tools.ietf.org/html/rfc3986#section-2.1) при использовании его в качестве значения для client_secret в запросе маркера доступа.

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Например:

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


В следующем примере показан ответ HTTP-запроса, в тексте которого содержится маркер доступа.

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]Мы советуем кэшировать значения access_token и expires_in во внешнее хранилище. Позже данные маркера можно извлечь из хранилища и повторно использовать в вызовах REST API служб мультимедиа. Это особенно удобно в сценариях, в которых маркер может безопасно использоваться несколькими процессами или компьютерами.

Обязательно следите за значением expires_in маркера доступа и при необходимости обновляйте вызовы REST API новыми маркерами.

###Подключение к универсальному коду ресурса служб мультимедиа

Корневой URI для служб мультимедиа — это https://media.windows.net/. Изначально необходимо подключиться к этому URI, а если вы получите ответ 301 (перенаправление), следует использовать для последующих вызовов новый URI. Кроме того, не используйте в запросах логику автоматического перенаправления или следования. HTTP-команды и тексты запросов не будут перенаправляться на новый URI.

Обратите внимание, что корневой URI для передачи и скачивания файлов ресурсов — https://yourstorageaccount.blob.core.windows.net/, где имя учетной записи хранения — это имя, использованное при настройке учетной записи служб мультимедиа.

В следующем примере показан HTTP-запрос к корневому URI служб мультимедиа (https://media.windows.net/). При выполнении запроса возвращается код ошибки 301 (перенаправление). Следующий запрос использует новый URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).

**HTTP-запрос**:
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.9
	Accept: application/json
	Host: media.windows.net


**HTTP-ответ**:
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**HTTP-запрос** (с использованием нового URI):
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.9
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-ответ**:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE]После получения нового универсального кода ресурса именно этот новый код нужно использовать для взаимодействия со службами мультимедиа.


<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=July15_HO4-->