<properties 
	pageTitle="Настройка политик доставки ресурсов-контейнеров с помощью REST API служб мультимедиа | Microsoft Azure" 
	description="В этой статье демонстрируется настройка различных политик доставки ресурсов-контейнеров с помощью REST API служб мультимедиа." 
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
	ms.date="09/19/2016"  
	ms.author="juliako"/>

#Настройка политик доставки ресурсов-контейнеров

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Если вы планируете динамически шифровать доставляемые ресурсы-контейнеры, вам следует настроить политику доставки таких ресурсов (один из этапов рабочего процесса доставки содержимого в службах мультимедиа). Политика доставки ресурсов сообщает службам мультимедиа способ доставки ресурса: какие протоколы передачи следует использовать для динамической упаковки ресурса (например, MPEG DASH, HLS, Smooth Streaming или все), следует ли использовать динамическое шифрование и как (конвертное или общее шифрование).

В этом разделе рассматриваются причины и способы создания и настройки политик доставки ресурсов-контейнеров.

>[AZURE.NOTE]Чтобы применять динамическую упаковку и динамическое шифрование, у вас должна быть хотя бы одна единица шифрования (которая также называется единицей потоковой передачи). Дополнительную информацию см. в разделе [Масштабирование службы мультимедиа](media-services-portal-manage-streaming-endpoints.md).
>
>Кроме того, ресурс должен содержать набор MP4-файлов с адаптивной скоростью или файлов Smooth Streaming с адаптивной скоростью.

К одному ресурсу можно применить различные политики. Например, можно применить шифрование PlayReady при использовании Smooth Streaming и конвертное шифрование AES при использовании MPEG DASH и HLS. Потоковая передача по тем протоколам, которые не определены в политике доставки (например, если вы добавили одну политику, которая предусматривает использование только протокола HLS), будет блокироваться. Исключением являются те случаи, когда политика доставки ресурсов совсем не определена. Тогда все протоколы могут использоваться в незашифрованном виде.

Если необходимо доставить зашифрованный в хранилище ресурс-контейнер, необходимо настроить его политику доставки. Перед выполнением потоковой передачи ресурса сервер потоковой передачи удаляет шифрование хранилища и осуществляет потоковую передачу содержимого с помощью указанной политики доставки. Например, для доставки ресурса, зашифрованного с помощью ключа шифрования конвертного типа для AES, задайте для типа политики значение **DynamicEnvelopeEncryption**. Чтобы удалить шифрование хранилища и выполнить потоковую передачу ресурса в незашифрованном виде, задайте для типа политики значение **NoDynamicEncryption**. Далее приведены примеры, показывающие, как настроить эти типы политики.

В зависимости от способа настройки политики доставки ресурсов вы можете выполнить динамическую упаковку, динамическое шифрование и потоковую передачу с помощью следующих протоколов потоковой передачи: Smooth Streaming, HLS, MPEG DASH и HDS.

В следующем списке приведены форматы, которые используются для потоковой передачи ресурсов в формате Smooth, HLS, DASH и HDS.

Smooth Streaming:

{имя конечной точки потоковой передачи - имя учетной записи служб мультимедиа}.streaming.mediaservices.windows.net/{идентификатор указателя}/{имя файла}.ism/Manifest

HLS:

{имя конечной точки потоковой передачи - имя учетной записи служб мультимедиа}.streaming.mediaservices.windows.net/{идентификатор указателя}/{имя файла}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{имя конечной точки потоковой передачи - имя учетной записи служб мультимедиа}.streaming.mediaservices.windows.net/{идентификатор указателя}/{имя файла}.ism/Manifest(format=mpd-time-csf)

HDS

{имя конечной точки потоковой передачи - имя учетной записи служб мультимедиа}.streaming.mediaservices.windows.net/{идентификатор указателя}/{имя файла}.ism/Manifest(format=f4m-f4f)

Указания по публикации ресурса и созданию URL-адреса потоковой передачи см. в статье [Создание URL-адреса потоковой передачи](media-services-deliver-streaming-content.md).


##Рекомендации

- Невозможно удалить политику доставки ресурсов-контейнеров (AssetDeliveryPolicy), связанную с ресурсом-контейнером, пока существует указатель OnDemand (потоковой передачи) для этого ресурса-контейнера. Рекомендуется удалить политику из ресурса-контейнера перед удалением политики.
- Если политика доставки ресурсов-контейнеров не задана, создать указатель потоковой передачи в зашифрованном ресурсе-контейнере хранилища. Если ресурс-контейнер хранилища не зашифрован, система позволит создать указатель и выполнить потоковую передачу ресурса-контейнера в незашифрованном виде без политики доставки ресурса-контейнера.
- С одним ресурсом-контейнером можно связать несколько политик доставки ресурсов-контейнеров, но можно указать только один способ обработки заданного протокола доставки ресурса-контейнера (AssetDeliveryProtocol). То есть при попытке связать две политики доставки, определяющие протокол AssetDeliveryProtocol.SmoothStreaming, появится ошибка, поскольку система не знает, какую из них необходимо применить, когда клиент выполняет запрос Smooth Streaming.
- При наличии ресурса-контейнера с существующим указателем потоковой передачи новую политику привязать к ресурсу-контейнеру нельзя (можно либо разорвать связь ресурса-контейнера с существующей политикой, либо обновить политику доставки, связанную с ресурсом-контейнером). Необходимо сначала удалить указатель потоковой передачи, затем настроить политики и повторно создать указатель потоковой передачи. При повторном создании указателя потоковой передачи вы можете использовать тот же идентификатор указателя (locatorId), но необходимо убедиться, что это не приведет к проблемам для клиентов, так как кэширование содержимого с помощью исходного или нисходящего CDN выполнить нельзя.

>[AZURE.NOTE] При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](media-services-rest-how-to-use.md).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Последующие вызовы необходимо осуществлять к новому универсальному коду ресурса (URI), как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect-programmatically.md).


##Политики доставки незашифрованных ресурсов

###<a id="create_asset_delivery_policy"></a>Создание политики доставки активов
Следующий запрос HTTP создает политику доставки ресурсов, указывающую, что не следует применять динамическое шифрование и осуществлять доставку с помощью потоковой передачи по любому из следующих протоколов: MPEG DASH, HLS и протоколы Smooth Streaming.

Информацию о том, какие значения можно задать при создании политики доставки ресурсов, см. в разделе [Типы, используемые при определении AssetDeliveryPolicy](#types).


Запрос:
	  
	POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
	Host: media.windows.net
	
	{"Name":"Clear Policy",
	"AssetDeliveryProtocol":7,
	"AssetDeliveryPolicyType":2,
	"AssetDeliveryConfiguration":null}

Ответ:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 363
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
	request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
	x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 08 Feb 2015 06:21:27 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
	"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
	"Name":"Clear Policy",
	"AssetDeliveryProtocol":7,
	"AssetDeliveryPolicyType":2,
	"AssetDeliveryConfiguration":null,
	"Created":"2015-02-08T06:21:27.6908329Z",
	"LastModified":"2015-02-08T06:21:27.6908329Z"}
	
###<a id="link_asset_with_asset_delivery_policy"></a>Привязка ресурса к политике доставки ресурсов

Следующий запрос HTTP связывает указанный ресурс с политикой доставки ресурсов.

Запрос:

	POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
	Host: media.windows.net
	
	{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Ответ:

	HTTP/1.1 204 No Content


##Политика доставки ресурсов DynamicEnvelopeEncryption 

###Создание ключа содержимого типа EnvelopeEncryption и связывание его с ресурсом

При определении политики доставки DynamicEnvelopeEncryption необходимо обязательно связать ресурс с ключом содержимого типа EnvelopeEncryption. Дополнительные сведения см. в разделе [Создание ключа содержимого](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Получение URL-адреса доставки

Получите URL-адрес доставки для указанного метода доставки ключа содержимого, созданного на предыдущем шаге. Клиент использует возвращенный URL-адрес для запроса ключа AES или лицензии PlayReady, чтобы воспроизвести защищенное содержимое.

Укажите тип URL-адреса, который необходимо получить в тексте HTTP-запроса. Если необходимо обеспечить защиту содержимого с помощью PlayReady, запросите URL-адрес для получения лицензии PlayReady служб мультимедиа, используя для параметра keyDeliveryType значение 1: {"keyDeliveryType":1}. Если необходимо обеспечить защиту содержимого с помощью конвертного шифрования, запросите URL-адрес для получения ключа, указав для параметра keyDeliveryType значение 2: {"keyDeliveryType":2}.

Запрос:
	
	POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
	Content-Type: application/json
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
	Host: media.windows.net
	Content-Length: 21
	
	{"keyDeliveryType":2}

Ответ:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 198
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
	request-id: d26f65d2-fe65-4136-8fcf-31545be68377
	x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 08 Feb 2015 21:42:30 GMT
	
	{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###Создание политики доставки активов

Следующий HTTP-запрос создает сущность **AssetDeliveryPolicy**, для которой настроено динамическое конвертное шифрование содержимого (**DynamicEnvelopeEncryption**), передаваемого по протоколу **HLS** (в этом примере потоковая передача по другим протоколам будет блокироваться).


Информацию о том, какие значения можно задать при создании политики доставки ресурсов, см. в разделе [Типы, используемые при определении AssetDeliveryPolicy](#types).

Запрос:

	POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
	x-ms-version: 2.11
	x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
	Host: media.windows.net
	
	{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{"Key":2,"Value":"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/"}]"}

	
Ответ:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 460
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
	request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
	x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 09 Feb 2015 05:24:38 GMT
	
	{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{"Key":2,"Value":"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###Привязка ресурса к политике доставки ресурсов

См. в разделе [Привязка ресурса к политике доставки ресурсов](#link_asset_with_asset_delivery_policy)

##Политика доставки ресурсов DynamicCommonEncryption 

###Создание ключа содержимого типа CommonEncryption и связывание его с ресурсом

При определении политики доставки DynamicCommonEncryption необходимо обязательно связать ресурс с ключом содержимого типа CommonEncryption. Дополнительные сведения см. в разделе [Создание ключа содержимого](media-services-rest-create-contentkey.md)).


###Получение URL-адреса доставки

Получите URL-адрес доставки для метода доставки ключа содержимого PlayReady, созданного на предыдущем шаге. Клиент использует возвращенный URL-адрес для запроса лицензии PlayReady, чтобы воспроизвести защищенное содержимого. Дополнительную информацию см. в разделе [Получение URL-адреса доставки](#get_delivery_url).

###Создание политики доставки активов

Следующий HTTP-запрос создает сущность **AssetDeliveryPolicy**, для которой настроено динамическое общее шифрование содержимого (**DynamicCommonEncryption**), передаваемого по протоколу **Smooth Streaming** (в этом примере потоковая передача по другим протоколам будет блокироваться).

Информацию о том, какие значения можно задать при создании политики доставки ресурсов, см. в разделе [Типы, используемые при определении AssetDeliveryPolicy](#types).


Запрос:

	POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
	x-ms-version: 2.11
	x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
	Host: media.windows.net
	
	{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{"Key":2,"Value":"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Если требуется защитить содержимое с помощью Widevine DRM, обновите значения AssetDeliveryConfiguration, чтобы использовать WidevineLicenseAcquisitionUrl (со значением 7), и укажите URL-адрес службы доставки лицензий. Вы можете использовать следующих партнеров AMS для доставки лицензий Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) и [castLabs](http://castlabs.com/company/partners/azure/).

Например:
 
	
	{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{"Key":7,"Value":"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]При шифровании с помощью Widevine будет возможна только доставка посредством DASH. Обязательно укажите DASH (2) в протоколе доставки ресурсов-контейнеров.
  
###Привязка ресурса к политике доставки ресурсов

См. в разделе [Привязка ресурса к политике доставки ресурсов](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Типы, используемые при определении AssetDeliveryPolicy

###AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

###ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->