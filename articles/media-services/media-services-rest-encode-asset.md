<properties 
	pageTitle="Кодирование ресурса-контейнера с помощью Media Encoder Standard | Microsoft Azure" 
	description="Узнайте, как использовать стандартный кодировщик мультимедиа для кодировки мультимедийного содержимого в службах мультимедиа. В примерах кода используется REST API." 
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


#Кодирование ресурса-контейнера с помощью стандартного кодировщика мультимедиа


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST](media-services-rest-encode-asset.md)
- [Портал](media-services-portal-encode.md)

##Обзор
Для поставки цифрового видео по Интернету необходимо сжать мультимедиа. Цифровые видеофайлы довольно объемные. Это затрудняет их доставку через Интернет и влияет на качество их отображения на устройствах клиентов. Кодирование — это процесс сжатия аудио- и видеофайлов, чтобы их могли просматривать клиенты.

Задания кодирования — одни из самых распространенных операций обработки в службах мультимедиа. Они создаются для преобразования файлов мультимедиа из одного формата кодирования в другой. При кодировании можно использовать встроенный кодировщик служб мультимедиа (стандартный кодировщик мультимедиа). Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в Магазине Azure. Информацию о задачах кодировки можно указать с помощью строк предустановок, заданных для кодировщика, или файлов конфигурации. Типы доступных предустановок см. в разделе [Предустановки задач для MES (стандартный Media Encoder)](http://msdn.microsoft.com/library/mt269960).

Каждое задание может состоять из одной или нескольких задач в зависимости от типа обработки, которую необходимо выполнить. REST API позволяет создавать задания и связанные с ними задачи одним из двух способов:

- встроенными средствами с помощью свойств навигации задач в сущностях Job или
- с помощью пакетной обработки OData.


Мы советуем всегда кодировать мезонинные файлы в набор MP4-файлов с адаптивной скоростью, а затем преобразовывать его в нужный формат с помощью [динамической упаковки](media-services-dynamic-packaging-overview.md). Для использования динамической упаковки вам потребуется получить по крайней мере одну единицу потокового воспроизведения по запросу для конечной точки потоковой передачи, из которой планируется передавать содержимое. Дополнительные сведения см. в статье [Масштабирование служб мультимедиа](media-services-portal-manage-streaming-endpoints.md).

Если выходящий ресурс зашифрован в хранилище, необходимо настроить политику доставки ресурсов. Дополнительную информацию см. в разделе [Настройка политики доставки ресурсов](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Прежде чем начать ссылаться на обработчики мультимедиа, убедитесь, что у вас есть правильный идентификатор обработчика мультимедиа. Дополнительные сведения см. в статье [Получение обработчиков мультимедиа](media-services-rest-get-media-processor.md).

##Создание задания с одной задачей кодирования

>[AZURE.NOTE] При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](media-services-rest-how-to-use.md).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Последующие вызовы необходимо осуществлять к новому универсальному коду ресурса (URI), как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect-programmatically.md).
>
>Если вы используете JSON и указываете ключевое слово **\_\_metadata** в запросе (например, для ссылки на связанный объект), то вы ДОЛЖНЫ задать для заголовка **Accept** [подробный формат JSON](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.

В следующем примере показано, как создать и опубликовать задание с одной задачей, предназначенной для кодирования видео с определенным разрешением и качеством. При кодировании с помощью стандартного кодировщика мультимедиа вы можете использовать предустановки конфигурации задач, указанные [здесь](http://msdn.microsoft.com/library/mt269960).

Запрос:

POST https://media.windows.net/API/Jobs HTTP/1.1 Content-Type: application/json;odata=verbose Accept: application/json;odata=verbose DataServiceVersion: 3.0 MaxDataServiceVersion: 3.0 x-ms-version: 2.11 Authorization: Bearer <значение маркера> x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Ответ:
	
	HTTP/1.1 201 Created

	. . . 

###Задание имени выходного актива

В следующем примере показано, как установить атрибут assetName:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}

##Рекомендации

- Чтобы определить количество входных или выходных ресурсов, используемых задачей, в свойствах TaskBody НЕОБХОДИМО использовать XML-литерал. В статье "Задача" приведено определение схемы XML.
- В определении TaskBody каждое внутреннее значение <inputAsset> и <outputAsset> необходимо установить как JobInputAsset(value) или JobOutputAsset(value).
- В задаче может содержаться несколько выходных ресурсов. Значение JobOutputAsset(x) может использоваться только один раз в качестве выходных данных задачи в задании.
- В качестве входного ресурса задачи можно указать JobInputAsset или JobOutputAsset.
- Задачи не должны образовывать цикл.
- Значение параметра, которое передается в JobInputAsset или JobOutputAsset, — это значение индекса для ресурса. Фактические ресурсы определяются в свойствах навигации ресурсов InputMediaAsset и OutputMediaAsset в определении сущности Job.
- Так как службы мультимедиа созданы на платформе OData версии 3, ссылки на отдельные ресурсы в коллекциях свойств навигации ресурсов InputMediaAsset и OutputMediaAsset добавляются в виде пары "имя-значение" \_\_metadata: uri.
- Ресурсы InputMediaAsset сопоставляются с одним или несколькими ресурсами, созданными в службах мультимедиа. Ресурсы OutputMediaAsset создаются системой. Они не ссылаются на существующий ресурс.
- Ресурсам OutputMediaAsset можно присвоить имя с помощью атрибута assetName. Если этот атрибут отсутствует, именем ресурса OutputMediaAsset будет внутреннее текстовое значение элемента <outputAsset> с суффиксом, которым может быть значение параметра имени задания или параметра идентификатора задания (если свойство Name не определено). Например, если задать для атрибута assetName значение Sample, для свойства Name ресурса OutputMediaAsset будет задано значение Sample. Тем не менее, если значение атрибута assetName не задано, но в качестве имени задания задано NewJob, имя OutputMediaAsset будет выглядеть следующим образом: JobOutputAsset(значение)\_NewJob.


##Создание задания с цепными задачами

Во многих сценариях приложений разработчикам необходимо создать серию задач обработки. В службах мультимедиа вы можете создавать серии цепных задач. Каждая задача выполняет разные шаги обработки. Эти задачи также могут использовать разные обработчики мультимедиа. Цепные задачи могут передать ресурсы из одной задачи в другую, следуя линейной последовательности задач ресурса. Тем не менее, в последовательность не должны быть включены задачи, выполняемые в задании. При создании цепной задачи цепные объекты **ITask** создаются в одном объекте **IJob**.

>[AZURE.NOTE] Сейчас количество задач ограничено 30 задачами на задание. Если вам необходимо создать цепь из более чем 30 задач, создайте для них несколько заданий.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

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
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


###Рекомендации

Чтобы включить создание цепных задач:

- в задании должно быть по крайней мере 2 задачи;
- должна существовать хотя бы одна задача, входные данные которой являются выходными данными другой задачи в задании.

## Использование пакетной обработки OData 

В следующем примере показано, как использовать пакетную обработку OData для создания задания и задач. Сведения о пакетной обработке см. в статье [Пакетная обработка по протоколу OData](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
	POST https://media.windows.net/api/$batch HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Accept: multipart/mixed
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net
	
	
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-ID: 1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/$1/Tasks HTTP/1.1
	Content-ID: 2
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{  
	   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	   "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="Custom output name">JobOutputAsset(0)</outputAsset></taskBody>"
	}

	--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## Создание задания с помощью сущности JobTemplate


При обработке нескольких ресурсов с помощью обычного набора задач сущности JobTemplate можно использовать для определения предустановленных наборов задач по умолчанию, порядка задач и т. д.

В следующем примере показано, как создать сущность JobTemplate, попутно определив сущность TaskTemplate. Сущность TaskTemplate кодирует файл ресурса, используя в качестве обработчика мультимедиа стандартный кодировщик мультимедиа. Но также можно использовать и другие обработчики.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]В отличие от других сущностей служб мультимедиа вы должны определить новый идентификатор GUID для каждой сущности TaskTemplate и указать его в тексте запроса в taskTemplateId и свойстве Id. Схема идентификации содержимого должна соответствовать схеме, описанной в статье "Идентификация сущностей служб мультимедиа Azure". Кроме того, сущности JobTemplates нельзя обновлять. Вместо этого необходимо создавать новые сущности с необходимыми изменениями.
 

При успешном выполнении возвращается следующий ответ:
	
	HTTP/1.1 201 Created
	
	. . .


В следующем примере показано, как можно создать задание, ссылаясь на идентификатор JobTemplate.

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

При успешном выполнении возвращается следующий ответ:
	
	HTTP/1.1 201 Created
	
	. . . 



##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Дальнейшие действия
Вы узнали, как создать задание для кодирования ресурса-контейнера. Теперь вы можете приступить к изучению статьи [Практическое руководство. Проверка хода выполнения задания](media-services-rest-check-job-progress.md).


##См. также

[Получение обработчиков мультимедиа](media-services-rest-get-media-processor.md)

<!---HONumber=AcomDC_0921_2016-->