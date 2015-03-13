<properties 
	pageTitle="Как кодировать ресурс с помощью кодировщика мультимедиа Azure" 
	description="Узнайте, как использовать кодировщик мультимедиа Azure для кодировки мультимедийного контента в службах мультимедиа. В примерах кода используется REST API." 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Как кодировать ресурс с помощью кодировщика мультимедиа Azure

Это одна из статей серии [Рабочий процесс для видео по запросу в службах мультимедиа](../media-services-video-on-demand-workflow). 

## Обзор
Для поставки цифрового видео по Интернету необходимо сжать мультимедиа. Цифровые видеофайлы довольно объемные. Это затрудняет их доставку через Интернет и влияет на качество их отображения на устройствах клиентов. Кодирование - это процесс сжатия аудио- и видеофайлов, чтобы их могли просматривать клиенты.

Задания кодирования - одни из самых распространенных операций обработки в службах мультимедиа. Они создаются для преобразования файлов мультимедиа из одного формата кодирования в другой. При кодировании можно использовать встроенный кодировщик служб мультимедиа. Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в Магазине Azure. Информацию о задачах кодировки можно указать с помощью строк предустановок, заданных для кодировщика, или файлов конфигурации. См. типы доступных предустановок в разделе "Предустановки задач для служб мультимедиа Azure". Если вы использовали сторонний кодировщик, вам следует [проверить свои файлы](https://msdn.microsoft.com/ru-ru/library/azure/dn750842.aspx).

Мы советует всегда кодировать мезонинные файлы в набор MP4-файлов с адаптивной скоростью, а затем преобразовывать его в нужный формат с помощью [динамической упаковки](https://msdn.microsoft.com/ru-ru/library/azure/jj889436.aspx).

## Создание задания с одной задачей кодирования 

>[AZURE.NOTE] При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](../media-services-rest-how-to-use).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой URI служб мультимедиа. Используйте для последующих вызовов новый URI, как описано в разделе [Подключение к службам мультимедиа с помощью REST API](../media-services-rest-connect_programmatically/). 


В следующем примере показано, как создать и опубликовать задание с одной задачей, предназначенной для кодирования видео с определенным разрешением и качеством. При кодировании с помощью кодировщика мультимедиа Azure вы можете использовать предустановки конфигурации задач, указанные [здесь](https://msdn.microsoft.com/ru-ru/library/azure/dn619389.aspx).
	
Запрос:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Ответ:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"\/Date(1336771959431)\/","LastModified":"\/Date(1336771959431)\/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

## Создание задания с цепными задачами

Во многих сценариях приложений разработчикам необходимо создать серию задач обработки. В службах мультимедиа вы можете создавать серии цепных задач. Каждая задача выполняет разные шаги обработки. Эти задачи также могут использовать разные обработчики мультимедиа. Цепные задачи могут передать ресурсы из одной задачи в другую, следуя линейной последовательности задач ресурса. Тем не менее, в последовательность не должны быть включены задачи, выполняемые в задании. При создании цепной задачи цепные объекты **ITask** создаются в одном объекте **IJob**.

>[AZURE.NOTE] Сейчас количество задач ограничено 30 задачами на задание. Если вам необходимо создать цепь из более чем 30 задач, создайте для них несколько заданий.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


## Дальнейшие действия
Вы узнали, как создать задание для кодирования ресурса. Теперь вы можете приступить к изучению статьи [Практическое руководство: проверка хода выполнения задания](../media-services-rest-check-job-progress/) .

[Azure Marketplace]: https://datamarket.azure.com/
[Предустановка кодировщика]: http://msdn.microsoft.com/library/dn619392.aspx
[Практическое руководство. Получение экземпляра обработчика мультимедиа]:http://go.microsoft.com/fwlink/?LinkId=301732
[Практическое руководство. Отправка зашифрованного актива]:http://go.microsoft.com/fwlink/?LinkId=301733
[Практическое руководство. Доставка ресурса путем скачивания]:http://go.microsoft.com/fwlink/?LinkId=301734
[Практическое руководство. Проверка хода выполнения задания]:http://go.microsoft.com/fwlink/?LinkId=301737
[Предустановка задачи для Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=45--> 
