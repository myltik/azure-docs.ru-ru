<properties 
	pageTitle="Краткое руководство по API рекомендаций для машинного обучения | Azure" 
	description="Рекомендации по Машинному обучению Azure. Краткое руководство по началу работы" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="jaymathe"/>

# Краткое руководство по API рекомендаций для машинного обучения

В этом документе описывается, как настроить вашу службу или приложение для использования службы рекомендаций Azure ML. 

##Оглавление

* Общая информация	
* Ограничения	        
* Интеграция	        
  * Проверка подлинности	
  *	URI службы	    
  *	Версия API	    
  *	Создание модели	
  *	Импорт данных каталога	
  *	Импорт данных об использовании	
     * Загрузка файла	
	 * Использование функции сбора данных	
* Построение модели рекомендаций	
* Получение состояния построения	
* Получение рекомендаций	
* Обновление модели	
* Юридическая информация	

 
##Общая информация

Для использования службы "Рекомендации" Azure ML необходимо сделать следующее.

* Создать модель. Модель - это контейнер с данными об использовании, данными каталога и моделью рекомендаций.
* Импортировать данные каталога. Это необязательный шаг. Каталог содержит метаданные элементов. Если не загружать данные каталога, служба рекомендаций будет получать данные о каталоге косвенным образом по данным об использовании.
* Импортировать данные об использовании. Данные об использовании можно загрузить двумя способами:
	* путем загрузки файла, содержащего данные об использовании;
	* путем отправки событий сбора данных.
	Обычно вы загружаете файл с данными об использовании, чтобы создать исходную модель рекомендаций (программу начальной загрузки) и использовать ее, пока система не соберет достаточно данных с помощью формата сбора данных.
* Построить модель рекомендаций. Это асинхронная операция, при которой система рекомендаций принимает все данные об использовании и создает модель рекомендаций. Эта операция может занять несколько минут или часов в зависимости от размера данных и параметров конфигурации построения. При активации построения вы получите идентификатор построения. Используйте его для проверки завершения процесса построения, прежде чем начать использовать рекомендации. 
* Получить рекомендации. Получите рекомендации для определенного элемента или списка элементов.

Все описанные выше действия выполняются с помощью API рекомендаций Azure ML.

##Ограничения

* Максимальное количество моделей на одну подписку: 10
* Максимальное количество элементов в каталоге: 100,000
* Максимальное количество сохраняемых точек использования: ~5 000 000. В случае загрузки или регистрации новых точек самые старые будут удаляться.
* Максимальный размер данных, которые можно отправить в POST (например, импорт данных каталога, импорт данных об использовании): 200 МБ
* Количество транзакций в секунду для неактивной модели рекомендаций: ~2. Только активная модель рекомендаций поддерживает до 20 транзакций в секунду.

##Интеграция

###Проверка подлинности
Следуйте рекомендациям Магазина Microsoft Azure в отношении проверки подлинности. Магазин поддерживает методы проверки подлинности Basic и OAuth.

###URI службы 
Корневые URI служб для каждого API рекомендаций Azure ML находятся [здесь](https://api.datamarket.azure.com/amla/recommendations/v1/).

Полный URI службы выражается с помощью элементов спецификации OData.  

###Версия API
У каждого вызова API будет в конце параметр запроса, называемый apiVersion, который должен иметь значение 1.0.

###Создание модели
Создание запроса "create model":

![Table1][1]

Ответ:

Код состояния HTTP: 200

OData XML
	
	feed/entry/content/properties/id - contains the model id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'</id>
	  <rights type="text" />	
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###Импорт данных каталога

В случае загрузки нескольких файлов каталога в одну модель несколькими вызовами мы вставим только новые элементы каталога. Существующие элементы останутся с исходными значениями.

![Table2][2]

Ответ:

Код состояния HTTP: 200

OData XML	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###Импорт данных об использовании

####Загрузка файла
В этом разделе показано, как загрузить данные об использовании с помощью файла. Можно вызывать этот API несколько раз с данными об использовании. Все данные будут сохранены для всех вызовов.

![Table3a][3]
![Table3b][4]

Ответ:

Код состояния HTTP: 200

OData XML	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error
	Feed\entry\content\properties\FileId - the file identifier


	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####Использование функции сбора данных
В этом разделе показано, как отправлять события в режиме реального времени в службу рекомендаций Azure ML с веб-сайта.

![Table4][5]

Тело запроса

	Ввод данных для каждого события, которое необходимо отправить. Одному и тому же пользователю или сеансу браузера следует отправлять один и тот же идентификатор в поле SessionId.


	Example of Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event> 


	Example of Recommendation Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RecommendationClick</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>


	Example of Adding to shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Removing from shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RemoveShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Purchase:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">   
	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>   
	<SessionId>11112222</SessionId>   
	<EventData>     
	<EventData>       
		<Name>Purchase</Name> 
		<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>       
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
	</EventData>     
	</EventData> 
	</Event>

	Example of sending 2 events "Click" and "AddShopCart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      <ItemName>itemName</ItemName>
      <ItemDescription>item description</ItemDescription>
      <ItemCategory>category</ItemCategory>
    </EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    </EventData>
  	</EventData>
	</Event>

Ответ:
Код состояния HTTP: 200

###Построение модели рекомендаций

![Table5][6]

Ответ:

Код состояния HTTP: 200

	OData XML	
	This is an asynchronous API. You will get a Build Id as a response. To know when the build has ended, you should call the "Get Builds Status of a Model" API and locate this build Id in the response. Note that a build can take from minutes to hours depending on the size of the data.
	You cannot consume recommendations till the build ends.

	Valid build status:

	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Id - contains the build id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###Получение состояния построения модели
![Table6][7]

Ответ:

Код состояния HTTP: 200

	OData XML	Valid build status:
	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Status -contains the build status

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###Получение рекомендаций
![Table7][8]

Код состояния HTTP: 200

	OData XML	The response includes one entry per recommended item, each entry has the following data:
	* Feed\entry\content\properties\Id - The recommended item id
	* Feed\entry\content\properties\Name - The name of the item 
	* Feed\entry\content\properties\Rating - The rating of the recommendation, higher number means higher confidence
	* Feed\entry\content\properties\Reasoning - the recommendation reasoning
	The example response below includes 10 recommended items:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###Обновление модели
Можно обновить описание модели или активного идентификатора построения.
Активный идентификатор построения - каждое построение для каждой модели имеет идентификатор построения. Активный идентификатор построения - это первое успешное построение каждой новой модели. Как только у вас будет активный идентификатор построения и вы выполните дополнительные построения для той же модели, следует явно задать его в качестве идентификатора построения по умолчанию, если необходимо. Если при использовании рекомендаций не указать идентификатор построения, который необходимо использовать, автоматически будет использоваться идентификатор по умолчанию.

Этот механизм позволяет при наличии действующей модели рекомендаций в рабочей среде строить новые модели и тестировать их перед выпуском.

![Table8][9]

Ответ:

Код состояния HTTP: 200

	OData XML
	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'" />
	</feed>

##Юридическая информация
Данный документ предоставляется на условиях "как есть". Сведения и мнения, содержащиеся в этом документе, включая URL-адреса и другие ссылки на веб-сайты, могут быть изменены без предварительного уведомления. 
Некоторые примеры, приведенные в этом документе, предназначены только для иллюстрации и являются вымышленными. Никакой реальной связи нет и не предполагается. 
Этот документ не предоставляет вам никаких законных прав на интеллектуальную собственность в продуктах корпорации Майкрософт. Вы можете скопировать и использовать данный документ для внутренних справочных целей. 
(c) Корпорация Майкрософт (Microsoft Corporation), 2014 г. Все права защищены. 


[1]: ./media/machine-learning-recommendation-api-quick-start-guide/Table01.png
[2]: ./media/machine-learning-recommendation-api-quick-start-guide/Table02.png
[3]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03a.png
[4]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03b.png
[5]: ./media/machine-learning-recommendation-api-quick-start-guide/Table04.png
[6]: ./media/machine-learning-recommendation-api-quick-start-guide/Table05.png
[7]: ./media/machine-learning-recommendation-api-quick-start-guide/Table06.png
[8]: ./media/machine-learning-recommendation-api-quick-start-guide/Table07.png
[9]: ./media/machine-learning-recommendation-api-quick-start-guide/Table08.png


<!--HONumber=46--> 
 