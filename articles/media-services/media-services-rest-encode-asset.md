<properties 
	pageTitle="Как кодировать ресурс с помощью обработчика мультимедиа Azure" 
	description="Узнайте, как использовать кодировщик мультимедиа Azure для кодировки мультимедийного контента в службах мультимедиа. В примерах кода используется REST API." 
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
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Как кодировать ресурс с помощью обработчика мультимедиа Azure

Это одна из статей цикла [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md).

##Обзор
Для поставки цифрового видео по Интернету необходимо сжать мультимедиа. Цифровые видеофайлы довольно объемные. Это затрудняет их доставку через Интернет и влияет на качество их отображения на устройствах клиентов. Кодирование — это процесс сжатия аудио- и видеофайлов, чтобы их могли просматривать клиенты.

Задания кодирования — одни из самых распространенных операций обработки в службах мультимедиа. Они создаются для преобразования файлов мультимедиа из одного формата кодирования в другой. При кодировании можно использовать встроенный кодировщик служб мультимедиа. Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в Магазине Azure. Информацию о задачах кодировки можно указать с помощью строк предустановок, заданных для кодировщика, или файлов конфигурации. См. типы доступных предустановок в разделе [Предустановки задач для служб мультимедиа Azure](https://msdn.microsoft.com/library/azure/dn619392.aspx). Если вы использовали сторонний кодировщик, вам следует [проверить свои файлы](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Каждое задание может состоять из одной или нескольких задач в зависимости от типа обработки, которую необходимо выполнить. Задания и связанные с ними задачи можно создавать через REST API двумя способами: задачи можно определять прямо в сущностях Job в свойстве навигации задач или через пакетную обработку по протоколу OData. Пакет SDK служб мультимедиа использует пакетную обработку, однако для удобства чтения примеров кода в этом разделе задачи определены встроенными средствами. Сведения о пакетной обработке см. в статье [Пакетная обработка по протоколу OData](http://www.odata.org/documentation/odata-version-3-0/batch-processing/). Пример пакетной обработки можно также найти в статье о [сущности Job](https://msdn.microsoft.com/library/azure/hh974289.aspx).

Мы советуем всегда кодировать мезонинные файлы в набор MP4-файлов с адаптивной скоростью, а затем преобразовывать его в нужный формат с помощью [динамической упаковки](https://msdn.microsoft.com/library/azure/jj889436.aspx). Для использования динамической упаковки вам потребуется получить по крайней мере одну единицу потоковой передачи по запросу для конечной точки потоковой передачи, из которой планируется передавать содержимое. Дополнительные сведения см. в статье [Масштабирование служб мультимедиа](media-services-manage-origins.md#scale_streaming_endpoints).

Если выходящий ресурс зашифрован в хранилище, необходимо настроить политику доставки ресурсов. Дополнительную информацию см. в разделе [Настройка политики доставки ресурсов](media-services-rest-configure-asset-delivery-policy.md).



##Создание задания с одной задачей кодирования 

>[AZURE.NOTE]При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
>
>При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в разделе [Настройка для разработки REST API служб мультимедиа](media-services-rest-how-to-use.md).

>После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой универсальный код ресурса (URI) служб мультимедиа. Последующие вызовы необходимо осуществлять к новому универсальному коду ресурса (URI), как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect_programmatically.md).


В следующем примере показано, как создать и опубликовать задание с одной задачей, предназначенной для кодирования видео с определенным разрешением и качеством. При кодировании с помощью кодировщика служб мультимедиа Azure вы можете использовать предустановки конфигурации задач, указанные [здесь](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Запрос:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Ответ:
	
	HTTP/1.1 201 Created

	. . . 

##Рекомендации

- Чтобы определить количество входных или выходных ресурсов, используемых задачей, в свойствах TaskBody НЕОБХОДИМО использовать XML-литерал. В статье «Задача» приведено определение схемы XML.
- В определении TaskBody каждое внутреннее значение <inputAsset> и <outputAsset> необходимо установить как JobInputAsset(value) или JobOutputAsset(value).
- В задаче может содержаться несколько выходных ресурсов. Значение JobOutputAsset(x) может использоваться только один раз в качестве выходных данных задачи в задании.
- В качестве входного ресурса задачи можно указать JobInputAsset или JobOutputAsset.
- Задачи не должны образовывать цикл.
- Значение параметра, которое передается в JobInputAsset или JobOutputAsset, — это значение индекса для ресурса. Фактические ресурсы определяются в свойствах навигации ресурсов InputMediaAsset и OutputMediaAsset в определении сущности Job. 
- Так как службы мультимедиа созданы на платформе OData версии 3, ссылки на отдельные ресурсы в коллекциях свойств навигации ресурсов InputMediaAsset и OutputMediaAsset добавляются в виде пары «имя-значение» __metadata: uri.
- Ресурсы InputMediaAsset сопоставляются с одним или несколькими ресурсами, созданными в службах мультимедиа. Ресурсы OutputMediaAsset создаются системой. Они не ссылаются на существующий ресурс.
- Ресурсам OutputMediaAsset можно присвоить имя с помощью атрибута assetName. Если этот атрибут отсутствует, именем ресурса OutputMediaAsset будет внутреннее текстовое значение элемента <outputAsset> с суффиксом, которым может быть значение параметра имени задания или параметра идентификатора задания (если свойство Name не определено). Например, если задать для атрибута assetName значение Sample, для свойства Name ресурса OutputMediaAsset будет задано значение Sample. Тем не менее, если значение атрибута assetName не задано, но в качестве имени задания задано NewJob, имя OutputMediaAsset будет выглядеть следующим образом: JobOutputAsset(значение)_NewJob. 

В следующем примере показано, как установить атрибут assetName:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##Создание задания с цепными задачами

Во многих сценариях приложений разработчикам необходимо создать серию задач обработки. В службах мультимедиа вы можете создавать серии цепных задач. Каждая задача выполняет разные шаги обработки. Эти задачи также могут использовать разные обработчики мультимедиа. Цепные задачи могут передать ресурсы из одной задачи в другую, следуя линейной последовательности задач ресурса. Тем не менее, в последовательность не должны быть включены задачи, выполняемые в задании. При создании цепной задачи цепные объекты **ITask** создаются в одном объекте **IJob**.

>[AZURE.NOTE]Сейчас количество задач ограничено 30 задачами на задание. Если вам необходимо создать цепь из более чем 30 задач, создайте для них несколько заданий.


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
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###Рекомендации

Чтобы включить создание цепных задач:

- в задании должно быть по крайней мере 2 задачи;
- должна существовать хотя бы одна задача, входные данные которой являются выходными данными другой задачи в задании.


## Создание задания с помощью сущности JobTemplate


При обработке нескольких ресурсов с помощью обычного набора задач сущности JobTemplate можно использовать для определения предустановленных наборов задач по умолчанию, порядка задач и т. д.

В следующем примере показано, как создать сущность JobTemplate, попутно определив сущность TaskTemplate. Сущность TaskTemplate кодирует файл ресурса, используя в качестве обработчика мультимедиа кодировщик служб мультимедиа Azure. Но также можно использовать и другие обработчики.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]В отличие от других сущностей служб мультимедиа вы должны определить новый идентификатор GUID для каждой сущности TaskTemplate и указать его в тексте запроса в taskTemplateId и свойстве Id. Схема идентификации содержимого должна соответствовать схеме, описанной в статье «Идентификация сущностей служб мультимедиа Azure». Кроме того, сущности JobTemplates нельзя обновлять. Вместо этого необходимо создавать новые сущности с необходимыми изменениями.
 

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


##Управление именами выходных файлов кодировщика служб мультимедиа 

По умолчанию кодировщик служб мультимедиа Azure создает имена выходных файлов, совмещая различные атрибуты входного ресурса и процесса кодирования. Каждый атрибут определяется с помощью макроса, как описано ниже.

Ниже приведен полный список макросов, которые можно использовать для именования выходных файлов. Audio Bitrate — скорость кодированного звука, указанная в килобитах в секунду.

- Audio Codec — кодек, используемый для кодирования звука. Допустимые значения: AAC, WMA и DDP.
- Channel Count — количество кодируемых каналов. Допустимые значения: 1, 2 и 6.
- Default extension — расширение файла по умолчанию. 
- Language — код языка BCP-47, который обозначает язык звуковой дорожки. Текущее значение по умолчанию: und. 
- Original File Name — имя файла, переданного в службу хранилища Azure.
- StreamId — идентификатор потока, указанный в атрибуте streamID элемента <StreamInfo> в файле с предустановками. 
- Video Codec — кодек, используемый для кодирования видео. Допустимые значения: H264 и VC1.
- Video Bitrate — скорость кодированного видео, указанная в килобитах в секунду.

Различные комбинации этих макросов позволяют определять имена файлов, которые будет создавать кодировщик служб мультимедиа. Например, по умолчанию применяется такой способ именования:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

Способ именования файлов определяется с помощью атрибута DefaultMediaOutputFileName элемента [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Например:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

Кодировщик вставит между макросами символы подчеркивания. Например, указанная выше конфигурации создаст такое имя файла: MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.

##Дальнейшие действия
Вы узнали, как создать задание для кодирования актива. Теперь вы можете приступить к изучению статьи [Проверка хода выполнения задания с помощью служб мультимедиа](media-services-rest-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->