<properties 
	pageTitle="Как создать обработчик мультимедиа — Azure" 
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
	ms.date="05/12/2015" 
	ms.author="juliako"/>


#Получение экземпляра процессора мультимедиа

Это одна из статей цикла [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md).


##Обзор

В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Имя обработчика мультимедиа</th>
       <th>Описание</th>
	<th>Дополнительные сведения</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Позволяет выполнять задачи кодирования с использованием обработчика мультимедиа Azure.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Строки предустановок задачи для кодировщика службы мультимедиа Azure</a></td>
    </tr>
    <tr>
       <td>Рабочий процесс Premium обработчика мультимедиа</td>
       <td>Позволяет выполнять задачи кодирования с использованием рабочего процесса Premium обработчика мультимедиа.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Кодирование с помощью расширенного рабочего процесса кодировщика мультимедиа.</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure</a>.</td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Позволяет преобразовать мультимедийные активы из формата MP4 в формат Smooth Streaming. Позволяет также преобразовать мультимедийные активы из формата Smooth Streaming в формат Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Позволяет шифровать мультимедийные активы с использованием PlayReady Protection.</td>
        <td><a href=" http://go.microsoft.com/fwlink/?LinkId=613274">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (предварительная версия)</td>
		<td>Позволяет сгладить «неровности» видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа.</td>
		<td><a href="http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Расшифровка хранилища</td>
        <td>Позволяет расшифровать мультимедийные активы, которые были зашифрованы с помощью шифрования хранилища.</td>
		<td>Недоступно</td>
    </tr>  </tbody>
</table>

<br />

##Получение MediaProcessor

>[AZURE.NOTE]При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](media-services-rest-how-to-use.md).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Последующие вызовы необходимо осуществлять к новому универсальному коду ресурса (URI), как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect_programmatically.md).



Следующий вызов REST показывает, как получить экземпляр обработчика мультимедиа по имени (в данном случае это **кодировщик службы мультимедиа Azure**).

	
Запрос:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
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

<!---HONumber=July15_HO4-->