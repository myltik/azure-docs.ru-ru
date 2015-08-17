<properties 
	pageTitle="Документация по интерфейсу API рекомендаций для машинного обучения Azure | Microsoft Azure" 
	description="Машинное обучение Azure: документация по интерфейсу API рекомендаций" 
	services="machine-learning" 
	documentationCenter="" 
	authors="oranms" 
	manager="paulettm" 
	editor="cgronlun"/>
<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="v-ahgumn"/>


#Машинное обучение Azure: документация по интерфейсу API рекомендаций

Версия: 1.0<br>. Последнюю версию можно загрузить [здесь](machine-learning-recommendation-api-documentation.md).

В этом документе описываются интерфейсы API механизма рекомендаций в машинном обучении Azure.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1. Общая информация
Данный документ содержит справочные материалы по интерфейсам API. Сначала следует ознакомиться с документом "Рекомендации в машинном обучении Azure: краткое руководство".

API Рекомендаций в машинном обучении Azure можно разделить на 8 групп:

1.	<ins>Базовая обработка модели</ins> — интерфейсы API, позволяющие выполнять основные операции с моделью, например создание, обновление или удаление модели.
2.	<ins>Расширенная обработка модели</ins> — интерфейсы API, позволяющие получать подробные сведения о данных модели.
3.	<ins>Бизнес-правила модели</ins> — интерфейсы API, позволяющие управлять бизнес-правилами в отношении результатов рекомендаций модели.
4.	<ins>Каталог</ins> — интерфейсы API, позволяющие выполнять базовые операции с каталогом модели. Каталог содержит метаданные по элементам данных об использовании.
5.	<ins>Данные по использованию</ins> — интерфейсы API, позволяющие выполнять основные операции с данными по использованию модели. Данные об использовании в основном виде состоят из строк, содержащих пары <userId>,<itemId>.
6.	<ins>Сборка</ins> — интерфейсы API, позволяющие инициировать сборку модели и выполнять базовые операции, связанные с этой сборкой. Инициировать сборку модели можно, получив значимые данные об использовании.
7.	<ins>Рекомендации</ins> — интерфейсы API, позволяющие использовать рекомендации после завершения сборки модели.
8.	<ins>Уведомления</ins> — интерфейсы API, позволяющие получать уведомления о проблемах, связанных с операциями API. Например, если при передаче данных об использовании через систему сбора данных происходит сбой обработки большинства событий, то вызывается сообщение об ошибке.


##2. Ограничения

* Максимальное количество моделей на одну подписку: 10
* Максимальное количество элементов в каталоге: 100 000
* Максимальное количество поддерживаемых точек использования: около 5 000 000. По мере поступления новых точек будут удаляться самые старые.
* Максимальный размер данных, которые можно отправить в POST (например, импорт данных каталога, импорт данных об использовании): 200 МБ
* Количество транзакций в секунду для неактивной модели рекомендаций: ~ 2. Только активная модель рекомендаций поддерживает до 20 транзакций в секунду.

##3. Интерфейсы API: общие сведения

###3.1. Проверка подлинности
В вопросах, касающихся проверки подлинности, следуйте рекомендациям Microsoft Azure Marketplace. Marketplace поддерживает обычный метод проверки подлинности или проверку подлинности OAuth.

###3.2. URI службы
Корневые URI служб для каждого API рекомендаций Azure ML находятся [здесь](https://api.datamarket.azure.com/amla/recommendations/v1/).

Полный URI службы выражается с помощью элементов спецификации OData.

###3.3. Версия API
У каждого вызова API будет в конце параметр запроса, называемый apiVersion, который должен иметь значение 1.0.

##4. Базовая обработка модели

###4.1. Создание модели
Создание запроса "create model":

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelName |	Допустимы только буквы (A-Z, a-z), цифры (0–9), дефисы (-) и символы подчеркивания (_).<br>Максимальная длина: 20 |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |


**Ответ**.

Код состояния HTTP: 200

OData XML

	feed/entry/content/properties/id – contains the model id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###4.2. Получение модели
Запрос получения модели:

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	id |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	feed/entry/content/properties/Id – Model unique id
	feed/entry/content/properties/Name – Model name
	feed/entry/content/properties/Date – Model creation date
	feed/entry/content/properties/Status – Model status. One of the following:
    	- Created - model is created and does not contains Catalog and Usage
		- ReadyForBuild – model is created and contains Catalog and Usage
	feed/entry/content/properties/HasActiveBuild – indicate if model was built successfully
	feed/entry/content/properties/BuildId – Model active BuildId
	feed/entry/content/properties/Mpr – Model MPR (see ModelInsight for more information)
	feed/entry/content/properties/UserName – Model internal username


	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###4.3. Получение всех моделей
Извлечение всех моделей текущего пользователя

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	feed/entry/content/properties/Id – Model unique id
	feed/entry/content/properties/Name – Model name
	feed/entry/content/properties/Date – Model creation date
	feed/entry/content/properties/Status – Model status. One of the following:
	•	Created - model is created and does not contains Catalog and Usage
	•	ReadyForBuild – model is created and contains Catalog and Usage
	feed/entry/content/properties/HasActiveBuild – indicate if model was built successfully
	feed/entry/content/properties/BuildId – Model active BuildId
	feed/entry/content/properties/Mpr – Model MPR (see ModelInsight for more information)
	feed/entry/content/properties/UserName – Model internal username
	feed/entry/content/properties/UsageFileNames – List of model usage files separated by comma
	feed/entry/content/properties/CatalogId – Model Catalog Id
	feed/entry/content/properties/Description – Model description
	feed/entry/content/properties/CatalogFileName – Model Catalog file name

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###4.4. Обновление модели

Можно обновить описание модели или активного идентификатора построения.<br> <ins>Активный идентификатор построения</ins> — каждое построение для каждой модели имеет идентификатор построения. Активный идентификатор построения — это первое успешное построение каждой новой модели. Если у вас уже есть активный идентификатор сборки, но вы делаете дополнительные сборки для той же модели, при желании его можно задать как идентификатор сборки по умолчанию. Если при использовании рекомендаций не указать идентификатор построения, который необходимо использовать, автоматически будет использоваться идентификатор по умолчанию.<br> Этот механизм позволяет при наличии действующей модели рекомендаций в рабочей среде строить новые модели и тестировать их перед выпуском.


| Метод HTTP | URI |
|:--------|:--------|
|ОТПРАВКА |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	id | Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса: | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Обратите внимание, что XML-теги Description и ActiveBuildId не обязательны. Если вы не хотите задавать эти параметры, удалите весь тег.|

**Ответ**.

Код состояния HTTP: 200

###4.5. Удаление модели
Удаление существующей модели по идентификатору

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	id |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##5. Расширенная обработка модели

###5.1. Подробные сведения о данных модели
Этот API возвращает статистические данные о тех данных об использовании, на которых основана модель.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

Данные возвращаются как коллекция следующих элементов: <pre>
	поток/запись/идентификатор/содержимое/свойства/поток
	ключа/запись/идентификатор/содержимое/свойства/значение
</pre>

В таблице ниже для каждого ключа описывается значение, которое он представляет. <table> <tr> <th>Ключ</th> <th>Описание</th> </tr> <tr> <td>AvgItemLength</td> <td>Среднее количество отдельных пользователей на элемент</td> </tr> <tr> <td>AvgUserLength</td> <td>Среднее количество отдельных элементов на пользователя</td> </tr> <tr> <td>DensificationNumberOfItems</td> <td>Количество элементов после обрезки элементов, не подлежащих моделированию</td> </tr> <tr> <td>DensificationNumberOfUsers</td> <td>Количество точек использования после обрезки пользователей и элементов, не подлежащих моделированию</td> </tr> <tr> <td>DensificationNumberOfRecords</td> <td>Количество точек использования после обрезки пользователей и элементов, не подлежащих моделированию</td> </tr> <tr> <td>MaxItemLength</td> <td>Количество отдельных пользователей для наиболее популярного элемента</td> </tr> <tr> <td>MaxUserLength</td> <td>Максимальное количество отдельных элементов для любого пользователя</td> </tr> <tr> <td>MinItemLength</td> <td>Максимальное количество отдельных пользователей для элемента</td> </tr> <tr> <td>MinUserLength</td> <td>Минимальное количество отдельных элементов для пользователя</td> </tr> <tr> <td>RawNumberOfItems</td> <td>Количество элементов до обрезки элементов, не подлежащих моделированию</td> </tr> <tr> <td>RawNumberOfUsers</td> <td>Количество точек использования до любых обрезок</td> </tr> <tr> <td>RawNumberOfRecords</td> <td>Количество точек использования до любых обрезок</td> </tr> <tr> <td>SampelingNumberOfItems</td> <td>При использовании функции выборки количество элементов в выборке, иначе можно пропустить</td> </tr> <tr> <td>SampelingNumberOfRecords</td> <td>При включении выборки количество пользователей выражает это же значение, иначе можно пропустить</td> </tr> <tr> <td>SampelingNumberOfUsers</td> <td>При включении выборки количество пользователей выражает это же значение, иначе следует пропустить</td> </tr> </table>

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###5.2. Подробные сведения о модели
Этот API возвращает подробные сведения о модели для активной сборки или для конкретной сборки, если она указана.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |С активным идентификатором построения:<br>`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>С идентификатором конкретного построения:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	buildId |	Необязательный — число, которое обозначает успешную сборку. |
|	apiVersion | 1.0 |
||| | Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

Данные возвращаются как коллекция следующих элементов:
<pre>
	поток/запись/идентификатор/содержимое/свойства/поток
	ключа/запись/идентификатор/содержимое/свойства/значение
</pre>

В таблице ниже для каждого ключа описывается значение, которое он представляет. <table> <tr> <th>Ключ</th> <th>Описание</th> </tr> <tr> <td>CatalogCoverage</td> <td>Какая часть каталога может моделироваться с помощью характеристик использования. Для остальных элементов потребуются функции, основанные на содержании.</td> </tr> <tr> <td>Mpr</td> <td>Средний процентильный рейтинг модели. Чем меньше это значение, тем лучше.</td> </tr> <tr> <td>NumberOfDimensions</td> <td>Число измерений, используемых алгоритмом факторизации матрицы.</td> </tr> </table>

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###5.3. Получение образца модели
Получение образца модели рекомендаций

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML<br> Ответ возвращается в формате необработанного текста
<pre>
Уровень 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Жертва: роман 
	655fc955-a5a3-4a26-9723-3090859cb27b, Жертва: Рейтинг романа: 0,5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Падение черного ястреба: рассказ о современной войне Рейтинг: 0,5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Криптономикон Рейтинг: 0,5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Имаджика Рейтинг: 0,5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Рейтинг совпадений: 0,514
56b61441-0eed-46cc-a8f6-112775b81892, Жизнь и смерть в Шанхае 
	56b61441-0eed-46cc-a8f6-112775b81892, Жизнь и смерть в Шанхае Рейтинг: 0,5218 
	53156702-cc0c-443d-b718-6fb74b2491d3, Сын Рейтинг: 0,5212 
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Дым и зеркала: рассказы и иллюзии Рейтинг: 0,5188 
	8f5fe006-79e4-4679-816b-950989d1db4b, Место, в котором я не был (современная американская проза) Рейтинг: 0,5156 
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Счастье: роман Рейтинг: 0,5156 
50471eec-9aeb-4900-84d7-21567ab18546, Если бы Будда ходил на свидания: руководство по поиску любви на духовном пути 
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Божественные тайны сестричек Я-Я: роман Рейтинг: 0,5266 
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Библия ядовитого леса: роман Рейтинг: 0,5252 
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Свиньи в раю Рейтинг: 0,5244 
	e2cbf7ad-0636-4117-8b30-298da6df7077, Сны животных Рейтинг: 0,5227 
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Версия старшей сестры: роман Рейтинг: 0,5222 
5e97148f-defb-4d74-af2d-80f4763bf531, На самом дне океана (книжный клуб Oprah's) 
	5e97148f-defb-4d74-af2d-80f4763bf531, На самом дне океана (книжный клуб Oprah's) рейтинг: 0,537 
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Верхний остров: роман Рейтинг: 0,5277 
	bc5b69db-733b-4346-adde-3927544258f7, Центр города Рейтинг: 0,5275 
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Приятного дня: история о крови и вонючих носках Рейтинг: 0,5252 
	0adf981a-b65b-4c11-b36b-78aca2f948a2, Идеальный шторм: правдивая история о противостоянии человека и моря Рейтинг: 0,5238 
68f97068-ae1a-4163-9e94-396b800b743d, Модок: правдивая история о величайшем слоне всех времен 
	68f97068-ae1a-4163-9e94-396b800b743d, Модок: правдивая история о величайшем слоне всех времен Рейтинг: 0,5379 
	6724862e-e4e7-4022-9614-1468d8b902ff, Маленький дом в прериях Рейтинг: 0,5345 
	cdedb837-1620-496d-94c4-6ccfed888320, Маленький дом в большом лесу Рейтинг: 0,5325 
	382164ba-406b-4187-b726-d7a54b9d790d, Дао Пуха Рейтинг: 0,5309 
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, На берегу тенистого ручья Рейтинг: 0,5285 
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Мужчины — с Марса, женщины — с Венеры: практическое руководство по достижению взаимопонимания и целей в отношениях 
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Мужчины — с Марса, женщины — с Венеры: практическое руководство по достижению взаимопонимания и целей в отношениях Рейтинг: 0,5397 
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Политкорректные сказки на ночь: современные рассказы о нашей жизни и времени Рейтинг: 0,5207 
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Воровская честь Рейтинг: 0,5195 
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, Щедрое дерево Рейтинг: 0,5194 
	883b360f-8b42-407f-b977-2f44ad840877, Страшные истории для рассказа в темноте: сборник американского фольклора (страшные рассказы) Рейтинг: 0,5184 
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Мужчины за работой: бейсбол как ремесло 
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, Архипелаг ГУЛАГ 1918–1956: опыт литературного расследования I–II Рейтинг: 0,5416 
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Мужчины за работой: бейсбол как ремесло Рейтинг: 0,5403 
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Фатерлянд Рейтинг: 0,5394 
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: арена, том 1 Рейтинг: 0,5379 
	8a1e9f36-97af-4614-bed9-24e3940a05f3, Больше сниглетов: слова, которые не попадаются в словарях, но должны в них быть Рейтинг: 0,5377 
12a6d988-be21-4a09-8143-9d5f4261ba16, Мечта об орлах 
	07b10e28-9e7c-4032-90b7-10acab7f2460, Криптономикон Рейтинг: 0,5417 
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Рейтинг совпадений: 0,5416 
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, Семейный рейтинг: 0,5371 
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Класс "Кило" Рейтинг: 0,5366 
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Законное платежное средство Рейтинг: 0,5366 
df87525b-e435-4bd6-8701-4e60ad344e28, Поиск рыбы 
	56d33036-dfda-46b9-8e2a-76cb03921bb0, Секретные материалы: эпицентр Рейтинг: 0,5417 
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Двенадцать пустяков Рейтинг: 0,5416 
	df87525b-e435-4bd6-8701-4e60ad344e28, Поиск рыбы Рейтинг: 0,5408 
	400fe331-2c35-490c-adbc-b28b4b73d56c, Расскажем президенту? Рейтинг: 0,5383 
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Оттенки грации Рейтинг: 0,5358 
de1f62a4-89e6-44d2-aaee-992a4bf093f1, Карта, которая изменила мир: Уильям Смит и рождение современной геологии 
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, Карта, которая изменила мир: Уильям Смит и рождение современной геологии Рейтинг: 0,5422 
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, Мой дядя Освальд Рейтинг: 0,5385 
	34b84627-48af-4a4c-96c4-b26fb3863f56, Полночь в саду добра и зла Рейтинг: 0,5379 
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, Уличный адвокат Рейтинг: 0,5376 
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Смилла и ее чувство снега Рейтинг: 0,5367

Уровень 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, Зловещий кабан (Хиллерман, Тони) 
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, Зловещий кабан (Хиллерман, Тони) Рейтинг: 0,5425 
	74c49398-bc10-4af5-a658-a996a1201254, Дети шторма (Питерс, Элизабет) Рейтинг: 0,5387 
	9ba80080-196e-43fd-8025-391d963f77e7, Плывущая девушка Рейтинг: 0,5372 
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Улыбка убийцы (Скоттолин, Лиза) Рейтинг: 0,5353 
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Законное платежное средство Рейтинг: 0,5332 
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Дни на озере Вобегон 
	0adf981a-b65b-4c11-b36b-78aca2f948a2, Идеальный шторм: правдивая история о противостоянии человека и моря Рейтинг: 0,5433 
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Дни на озере Вобегон Рейтинг: 0,543 
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Сниглеты (cниглеты: все слова, которых нет в словаре, но которые должны в нем быть) Рейтинг: 0,5327 
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Политкорректные истории для чтения в праздники: просвещенное чтение для рождественских каникул Рейтинг: 0,5307 
	798051a8-147d-4d46-b0dc-e836325029e6, ВОЗРАСТ НЕВИННОСТИ (НАЧАЛО ДЛЯ ФИЛЬМА) Рейтинг: 0,5301 
73f3e25a-e996-4162-9ed8-ff3d34075650, О, пионеры! (Penguin Twentieth-Century Classics) 
	cba8163f-6536-436b-8130-47b4a43c827f, Никому не верь (официальное руководство по секретным материалам, т. 2) Рейтинг: 0,5434 
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Малые боги (Discworld Novels (в мягкой обложке)) Рейтинг: 0.5406 
	73f3e25a-e996-4162-9ed8-ff3d34075650, О, пионеры! (Penguin Twentieth-Century Classics) Рейтинг: 0,5403 
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, Цвет магии Рейтинг: 0,539 
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, Истина где-то рядом (официальное руководство по секретным материалам, т. 1) Рейтинг: 0,5367 
271700a5-854a-4d5a-8409-6b57a5ee4de4, Флюк: или я знаю, почему поет крылатый кит 
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Флюк: или я знаю, почему поет крылатый кит Рейтинг: 0,5445 
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, Жена служащего (серия Children of Violence) Рейтинг: 0,5329 
	d279416e-19c0-43f8-9ec9-a585947879ca, Дзен: взгляд на мир Рейтинг: 0,5316 
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Месть отверженных девчонок: тайна Робин Хадсон (Тайны Робин Хадсон (в мягкой обложке)) Рейтинг: 0,5305 
	8ef4751c-7074-409e-a3ac-d49b222fc864, Там, где все происходит Рейтинг: 0,5289 
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Их глаза смотрели на бога 
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Их глаза смотрели на бога Рейтинг: 0,5446 
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, Бобовые деревья Рейтинг: 0,5389 
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, Бог мелочей Рейтинг: 0,5387 
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, Каменные дневники Рейтинг: 0,5355 
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Свиньи в раю Рейтинг: 0,5344 
5f17d90a-2604-4fe8-8977-1a280b9098b1, Очень опасная штучка (романы Стефани Плюм (в мягкой обложке)) 
	5f17d90a-2604-4fe8-8977-1a280b9098b1, Очень опасная штучка (романы Стефани Плюм (в мягкой обложке)) Рейтинг: 0,5446 
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Последняя апелляция Рейтинг: 0,5332 
	efcb1bc4-7278-4a8f-b491-befde02070d6, Момент истины Рейтинг: 0,5329 
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Фактор ожога Рейтинг: 0,5309 
	24c59962-458a-4ec8-b95d-d694e861919c, Дома в Митфорде (митфордские годы) Рейтинг: 0,5303 
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, Такой, каким его создала природа: мальчик, воспитанный как девочка 
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, Такой, каким его создала природа: мальчик, воспитанный как девочка Рейтинг: 0,5449 
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Свиньи в раю Рейтинг: 0,5329 
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Любовь во время холеры (Penguin Great Books of the 20th Century) Рейтинг: 0,5267 
	15689d09-c711-4844-84d8-130a90237b26, Бель Канто Рейтинг: 0,5245 
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Библия ядовитого леса: роман Рейтинг: 0,5235 
98df28ec-41e7-4fca-b77f-8b0d3109085d, Звездный путь: воспоминания 
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, Солнце тоже встает (A Scribner classic) Рейтинг: 0,5451 
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Звездный путь: воспоминания Рейтинг: 0,5442 
	0ce0014a-9a48-4013-a08a-7f2c11877930, Корабль Ее Величества Невиданный Рейтинг: 0,5421 
	15316ca6-1e38-425f-893d-691944a47000, Еще страшные истории для рассказа в темноте Рейтинг: 0,5409 
	329d5682-3dc3-4206-8aa2-eef4b1032258, Письма с Земли Рейтинг: 0,54 
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Дочь Фортуны: роман (книжный клуб Oprah's (в твердой обложке)) 
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Дочь Фортуны: роман (книжный клуб Oprah's (в твердой обложке)) Рейтинг: 0,5462 
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Библия ядовитого леса: роман Рейтинг: 0,5372 
	604eb3bd-6026-4f51-bffd-9fb54f180400, Семейные фотографии: роман Рейтинг: 0,5341 
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Песни на каждый день (книжный клуб Oprah's (в мягкой обложке)) Рейтинг: 0,5334 
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, Бобовые деревья Рейтинг: 0,5319 
d5358189-d70f-4e35-8add-34b83b4942b3, Свиньи в раю 
	d5358189-d70f-4e35-8add-34b83b4942b3, Свиньи в раю Рейтинг: 0,5491 
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Библия ядовитого леса: роман Рейтинг: 0,5401 
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, Каменные дневники Рейтинг: 0,5393 
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Песни на каждый день (книжный клуб Oprah's (в мягкой обложке)) Рейтинг: 0,5382 
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Свиньи в раю Рейтинг: 0,5367

</pre>

##6. Бизнес-правила модели
Можно добавить два типа правил: <strong>Blocklist</strong> и <strong>Upsale</strong>. Список блокировки (Blocklist) позволяет воспользоваться списком элементов, которые не должны возвращаться среди рекомендованных результатов. Правило продажи (Upsale) позволяет принудительно задать элементы, которые должны возвращаться в результатах рекомендаций.

###6.1. Получение правил для модели

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	feed/entry/content/properties/Id – The unique identifier of this rule
	feed/entry/content/properties/Type – The type of the rule. BlockList or Upsale
	feed/entry/content/properties/Parameter – The rule parameter

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###6.2. Добавление правила
| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/AddRule?apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	apiVersion | 1.0 |
|||
| Текст запроса | <ins>Для добавления правила BlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br><ins>Для добавления правила Upsale:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`|

Код состояния HTTP: 200

OData XML

	feed/entry/content/properties/Id – The unique identifier of this rule
	feed/entry/content/properties/Type – The type of the rule. BlockList or Upsale
	feed/entry/content/properties/Parameter – The rule parameter

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###6.3. Удаление правила
| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	filterId |	Уникальный идентификатор фильтра. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

###6.4. Удаление всех правил

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

##7. Каталог

###7.1. Импорт данных каталога

В случае загрузки нескольких файлов каталога в одну модель несколькими вызовами мы вставим только новые элементы каталога. Существующие элементы останутся с исходными значениями. С помощью этого метода нельзя обновлять данные каталога.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
| filename | Текстовый идентификатор каталога.<br>Допустимы только буквы (A-Z, a-z), цифры (0–9), дефисы (-) и символы подчеркивания (_).<br>Максимальная длина: 50 |
|	apiVersion | 1.0 |
|||
| Текст запроса | Данные каталога. Формат:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Имя</th><th>Обязательно</th><th>Тип</th><th>Описание</th></tr><tr><td>ИД элемента</td><td>Да</td><td>Буквенно-цифровой, макс. длина 50</td><td>Уникальный идентификатор элемента</td></tr><tr><td>Имя элемента</td><td>Да</td><td>Буквенно-цифровой, макс. длина 255</td><td>Имя элемента</td></tr><tr><td>Категория элемента</td><td>Да</td><td>Буквенно-цифровой, макс. длина 255</td><td>Категория, к которой относится этот элемент (например, поваренные книги, драматические произведения и т. п.)</td></tr><tr><td>Описание</td><td>Нет</td><td>Буквенно-цифровой, макс. длина 4000</td><td>Описание этого элемента</td></tr></table><br>Максимальный размер файла 200 МБ<br><br>Пример:<br><pre>2406e770-769c-4189-89de-1c9283f93a96,Клара Каллан, книга<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,Комната забвения: проза (Byzantium Book), книга<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework, книга<br>552a1940-21e4-4399-82bb-594b46d7ed54,Ограничение зверей, книга</pre> |


**Ответ**.

Код состояния HTTP: 200

OData XML

	feed\entry\content\properties\LineCount – number of lines accepted
	feed\entry\content\properties\ErrorCount – number of lines that were not inserted due to an error

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###7.2. Получение каталога
Извлечение всех элементов каталога

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	The response includes one entry per catalog item, each entry has the following data:

	feed/entry/content/properties/ExternalId – Catalog item external Id, the one provided by the customer
	feed/entry/content/properties/InternalId – Catalog item internal Id, the one that Azure ML Recommendations has generated
	feed/entry/content/properties/Name – Catalog item name
	feed/entry/content/properties/Category – Catalog item category
	feed/entry/content/properties/Description – Catalog item description
	feed/entry/content/properties/Metadata – Catalog item metadata

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###7.3. Получение элементов каталога по токену

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	token |	Токен имени каталога. Должен содержать не менее трех символов. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	The response includes one entry per catalog item, each entry has the following data:

	feed/entry/content/properties/ExternalId – Catalog item external Id, the one provided by the customer
	feed/entry/content/properties/InternalId – Catalog item internal Id, the one that Azure ML Recommendations has generated
	feed/entry/content/properties/Name – Catalog item name
	feed/entry/content/properties/Category – Catalog item category
	feed/entry/content/properties/Description – Catalog item description
	feed/entry/content/properties/Metadata – Catalog item metadata

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##8. Данные об использовании
###8.1. Импорт данных об использовании
####8.1.1. Загрузка файла
В этом разделе показано, как передавать данные об использовании с помощью файла. Можно вызывать этот API несколько раз с данными об использовании. Для всех вызовов сохранятся все данные об использовании.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
| filename | Текстовый идентификатор каталога.<br>Допустимы только буквы (A-Z, a-z), цифры (0–9), дефисы (-) и символы подчеркивания (_).<br>Максимальная длина: 50 |
|	apiVersion | 1.0 |
|||
| Текст запроса | Данные об использовании. Формат:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Имя</th><th>Обязательно</th><th>Тип</th><th>Описание</th></tr><tr><td>ИД пользователя</td><td>Да</td><td>Буквенно-цифровой</td><td>Уникальный идентификатор пользователя</td></tr><tr><td>ИД элемента</td><td>Да</td><td>Буквенно-цифровой, макс. длина 50</td><td>Уникальный идентификатор элемента</td></tr><tr><td>Время</td><td>Нет</td><td>Дата в формате: ГГГГ/ММ/ДДTЧЧ:ММ:СС (например, 2013/06/20T10:00:00)</td><td>Время даты</td></tr><tr><td>Событие</td><td>Нет, если данные переданы, необходимо указать дату</td><td>Один из следующих элементов:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Максимальный размер файла 200 МБ<br><br>Пример:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Ответ**.

Код состояния HTTP: 200

OData XML

	Feed\entry\content\properties\LineCount – number of lines accepted
	Feed\entry\content\properties\ErrorCount – number of lines that were not inserted due to an error
	Feed\entry\content\properties\FileId – the file identifier


	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####8.1.2. Использование функции сбора данных
В этом разделе показано, как отправлять события в режиме реального времени в службу рекомендаций Azure ML с веб-сайта.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	apiVersion | 1.0 |

Тело запроса

	Event data entry for each event you want to send. You should send for the same user or browser session the same id in the SessionId field.


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

	Example of sending 2 events “Click” and “AddShopCart:
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

**Ответ**. Код состояния HTTP: 200

###8.2. Вывод списка файлов использования модели
Извлечение метаданных из всех файлов об использовании модели

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	forModelId |	Уникальный идентификатор модели. |
|	apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	The response includes one entry per usage file, each entry has the following data:
	feed\entry\content\properties\Id – Usage file Id
	feed\entry\content\properties\Length – Usage file Length in MB
	feed\entry\content\properties\DateModified – Date when Usage file was created
	feed\entry\content\properties\UseInModel – Is usage files used in model

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###8.3. Получение статистики использования
Получение статистики использования:

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели. |
| startDate |	Дата начала. Формат: гггг/ММ/ддТЧЧ:мм:сс |
| endDate |	Дата окончания. Формат: гггг/ММ/ддТЧЧ:мм:сс |
| eventTypes |	Строка разделенных запятыми типов событий или null для получения всех событий. |
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML

	A collection of key/value that each one contains the sum of events for a specific event type grouped by hour.
	The key:
		feed\entry[i]\content\properties\Key – contains the time (grouped by hours) and the event type.
	The value:
		feed\entry[i]\content\properties\Value – total event count

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###8.4. Получение образца файла использования
Извлечение первых 2 КБ содержимого файла использования:

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели. |
| fileId |	Уникальный идентификатор файла использования модели. |
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML<br>
 Ответ возвращается в формате необработанного текста:<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
</pre>

###8.5. Получение файла использования модели
Извлечение всего содержимого файла использования:

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| mid |	Уникальный идентификатор модели. |
| fid |	Уникальный идентификатор файла использования модели. |
| загрузить | 1 |
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

OData XML<br> Ответ возвращается в формате необработанного текста:<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
</pre>

###8.6. Удаление файла использования
Удаление определенного файла использования

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели. |
| fileId | Уникальный идентификатор файла, который нужно удалить |
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200


###8.7. Удаление всех файлов использования
Удаление всех файлы использования модели

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели. |
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

##9. Создание

###9.1. Сборка модели

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели. |
| userDescription | Текстовый идентификатор каталога. Обратите внимание: при использовании пробелов их необходимо заменить сочетанием символов %20 См. пример выше.<br>Максимальная длина: 50 |
| apiVersion | 1.0 |
|||
| Текст запроса | Если это поле оставить пустым, сборка будет выполнена с параметрами сборки по умолчанию.<br><br>Если вы хотите установить параметры сборки, отправьте в тексте запроса следующий XML (см. раздел "Установка параметров сборки"):<br>`<BuildParametersList xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><UseFeaturesInModel>false</UseFeaturesInModel><AllowColdItemPlacement>false</AllowColdItemPlacement><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableFeatureCorrelation>false</EnableFeatureCorrelation><RefreshFeatureScoreOnBuild>false</RefreshFeatureScoreOnBuild><ComputeUpd>false</ComputeUpd><EnableModelingInsights>true</EnableModelingInsights><ModelingFeatureList /><ReasoningFeatureList /></BuildParametersList>`|

**Ответ**.

Код состояния HTTP: 200

OData XML

	This is an asynchronous API. You will get a Build Id as a response. To know when the build has ended, you should call the “Get Builds Status of a Model” API and locate this build Id in the response. Note that a build can take from minutes to hours depending on the size of the data.
	You cannot consume recommendations till the build ends.

	Valid build status:

	* Create – model was created
	* Queued – model build was triggered and it is queued
	* Building – the model is being build
	* Success – the build ended successfully
	* Error – the build ended with a failure
	* Cancelled – build was canceled
	* Cancelling – build is being cancelled

	Feed\entry\content\properties\Id – contains the build id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###9.2. Получение статусов сборок модели
Извлечение сборок определенной модели с указанием их статусов

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели. |
|	onlyLastBuild |	Указывает, следует вернуть весь журнал сборок модели или только состояние последней сборки. |
|	apiVersion |	1.0 |


**Ответ**.

Код состояния HTTP: 200

OData XML

	The response includes one entry per build, each entry has the following data:

	feed/entry/content/properties/UserName – the name of the user
	feed/entry/content/properties/ModelName – the name of the model
	feed/entry/content/properties/ModelId – the model unique identifier
	feed/entry/content/properties/IsDeployed – is the build deployed
	feed/entry/content/properties/BuildId – the build unique identifier
	feed/entry/content/properties/Status – build status. Can be one of the following: Error, Building, Queued, Cancelled, Cancelling, Success
	feed/entry/content/properties/StatusMessage – detailed status message (applies only to specific states)
	feed/entry/content/properties/Progress – build progress (%)
	feed/entry/content/properties/StartTime – Build start time
	feed/entry/content/properties/EndTime – Build end time
	feed/entry/content/properties/ExecutionTime – Build duration
	feed/entry/content/properties/ProgressStep – details about the current stage that a build in progress is in.

    Valid build status:
	* Create – model was created
	* Queued – model build was triggered and it is queued
	* Building – the model is being build
	* Success – the build ended successfully
	* Error – the build ended with a failure
	* Cancelled – build was canceled
	* Cancelling – build is being cancelled

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###9.3. Получение статусов сборок пользователя
Извлечение статусов сборок всех моделей пользователя

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	onlyLastBuild |	Указывает, следует вернуть весь журнал сборок модели или только состояние последней сборки. |
|	apiVersion |	1.0 |


**Ответ**.

Код состояния HTTP: 200

OData XML

	The response includes one entry per build, each entry has the following data:

	feed/entry/content/properties/UserName – the name of the user
	feed/entry/content/properties/ModelName – the name of the model
	feed/entry/content/properties/ModelId – the model unique identifier
	feed/entry/content/properties/IsDeployed – is the build deployed
	feed/entry/content/properties/BuildId – the build unique identifier
	feed/entry/content/properties/Status – build status. Can be one of the following: Error, Building, Queued, Cancelled, Cancelling, Success
	feed/entry/content/properties/StatusMessage – detailed status message (applies only to specific states)
	feed/entry/content/properties/Progress – build progress (%)
	feed/entry/content/properties/StartTime – Build start time
	feed/entry/content/properties/EndTime – Build end time
	feed/entry/content/properties/ExecutionTime – Build duration
	feed/entry/content/properties/ProgressStep – details about the current stage that a build in progress is in.

    Valid build status:
	* Create – model was created
	* Queued – model build was triggered and it is queued
	* Building – the model is being build
	* Success – the build ended successfully
	* Error – the build ended with a failure
	* Cancelled – build was canceled
	* Cancelling – build is being cancelled

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###9.4. Удаление сборки
Удаление сборки

ПРИМЕЧАНИЕ. Активную сборку удалить нельзя. Перед удалением активной сборки необходимо обновить модель до другой активной сборки.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| buildId | Уникальный идентификатор сборки. |
| apiVersion | 1.0 |

**Ответ.**

Код состояния HTTP: 200

###9.5. Отмена сборки
Отмена сборки на этапе построения

| Метод HTTP | URI |
|:--------|:--------|
|ОТПРАВКА |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| buildId | Уникальный идентификатор сборки. |
| apiVersion | 1.0 |

**Ответ.**

Код состояния HTTP: 200

###9.6. Получение параметров сборки
Извлечение параметров сборки

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetBuildParameters?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| buildId | Уникальный идентификатор сборки. |
| apiVersion | 1.0 |

**Ответ.**

Код состояния HTTP: 200

OData XML

Коллекция "ключ/значение", которую содержит каждый файл, содержит один параметр: 
<pre>
	Ключ: поток/запись/контент/свойства/ключ — имя параметра сборки
	Значение: поток/запись/содержание/свойства/значение — значение параметра сборки
</pre>

В таблице ниже для каждого ключа описывается его значение.
	<table>
    	<tr>
        	<th>Ключ</th>
        	<th>Значение</th>
        	<th>Тип</th>
        	<th>Допустимое значение</th>
        </tr>
        <tr>
        	<td>NumberOfModelIterations</td>
        	<td>Число итераций, выполняемых моделью, сказывается на общей длительности вычисления и точности модели. Чем оно больше, тем выше будет точность, но вычисление займет больше времени.</td>
        	<td>Целое число</td>
        	<td>от 10 до 50</td>
        </tr>
        <tr>
        	<td>NumberOfModelDimensions</td>
        	<td>Число измерений связано с числом характеристик, которые модель попытается выявить в ваших данных. Увеличив число измерений, можно разделить результаты на кластеры меньшего размера. Однако, если измерений слишком много, модели будет трудно установить взаимосвязи между элементами.</td>
        	<td>Целое число</td>
        	<td>от 10 до 40</td>
        </tr>
        <tr>
        	<td>MinItemAppearance</td>
        	<td>Можно задать минимальное количество появлений, в которых элемент не включен в модель. Чем выше пороговое значение, тем точнее будут результаты модели. Однако, если пороговое значение слишком велико, у вас может оказаться недостаточно элементов, пригодных для использования.</td>
        	<td>Целое число</td>
        	<td>0 или более</td>
        </tr>
        <tr>
        	<td>MinUserAppearance</td>
        	<td>Можно задать минимальное количество появлений, в которых элемент не включен в модель. Чем выше пороговое значение, тем точнее будут результаты модели. Однако, если пороговое значение слишком велико, у вас может оказаться недостаточно элементов, пригодных для использования, или недостаточно рекомендаций для конкретного пользователя.</td>
        	<td>Целое число</td>
        	<td>от 0 до 20</td>
        </tr>
        <tr>
        	<td>Описание</td>
        	<td>Описание сборки</td>
        	<td>Строка</td>
        	<td>Любой текст, не более 255 символов</td>
        	</tr>
        	<tr>
        	<td>UseFeaturesInModel</td>
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>AllowColdItemPlacement</td>
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>EnableFeatureCorrelation</td>
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>RefreshFeatureScoreOnBuild</td>
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>ComputeUpd</td> 
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>EnableModelingInsights</td> 
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>ModelingFeatureList</td> 
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
        	<td>ReasoningFeatureList</td>
        	<td>Для будущего использования</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
    </table>

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-02T11:23:53Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    		<title type="text">GetBuildParametersEntity</title>
    		<updated>2014-11-02T11:23:53Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
        			<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
        			<d:Value m:type="Edm.String">False</d:Value>
      			</m:properties>
    		</content>
  		</entry>
  		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
          		<m:properties>
        			<d:Key m:type="Edm.String">RefreshFeatureScoreOnBuild</d:Key>
        			<d:Value m:type="Edm.String">False</d:Value>
      			</m:properties>
    		</content>
  		</entry>
      	<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">ComputeUpd</d:Key>
            		<d:Value m:type="Edm.String">False</d:Value>
          		</m:properties>
        	</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
    		<updated>2014-11-02T11:23:53Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">True</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
        	<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">40</d:Value>
				</m:properties>
        	</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
            		<d:Value m:type="Edm.String">20</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">MinItemAppearance</d:Key>
					<d:Value m:type="Edm.String">5</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2014-11-02T11:23:53Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">MinUserAppearance</d:Key>
					<d:Value m:type="Edm.String">5</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
            		<d:Value m:type="Edm.String"></d:Value>
          		</m:properties>
        	</content>
      	</entry>
      	<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
            		<d:Value m:type="Edm.String"></d:Value>
          		</m:properties>
        	</content>
      	</entry>
      	<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
        	<title type="text">GetBuildParametersEntity</title>
        	<updated>2014-11-02T11:23:53Z</updated>
        	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetBuildParameters?buildId='1500075'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
        	<content type="application/xml">
          		<m:properties>
            		<d:Key m:type="Edm.String">Description</d:Key>
            		<d:Value m:type="Edm.String"></d:Value>
          		</m:properties>
        	</content>
      	</entry>
	</feed>

##10. Рекомендации
###10.1. Получение рекомендаций

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели. |
| itemIds | Разделенный запятыми список элементов, для которых предоставляются рекомендации.<br>Макс. длина: 200 |
| numberOfResults | Требуемое число результатов. |
| includeMetatadata | Для использования в будущем, всегда указывайте false. |
| apiVersion | 1.0 |

**Ответ.**

Код состояния HTTP: 200

OData XML

    The response includes one entry per recommended item, each entry has the following data:
		* Feed\entry\content\properties\Id – The recommended item id
		* Feed\entry\content\properties\Name – The name of the item 
		* Feed\entry\content\properties\Rating – The rating of the recommendation, higher number means higher confidence
		* Feed\entry\content\properties\Reasoning – the recommendation reasoning

    The example response below includes 10 recommended items:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

##11. Уведомления
Если в системе постоянно происходят сбои, модуль рекомендаций машинного обучения Microsoft Azure создает уведомления. Существует 3 типа уведомлений:
1. сбой сборки — срабатывает при каждом сбое сборки;
2. сбой обработки в системе получения данных - срабатывает в том случае, если при обработке событий использования происходит более 100 ошибок на модель в течение 5 минут;
3. сбой получения рекомендаций - срабатывает в том случае, если при обработке запросов рекомендаций происходит более 100 ошибок на модель в течение 5 минут.


###11.1. Получение уведомлений
Извлечение всех уведомлений для всех моделей или только для одной из них

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Получение всех уведомлений для всех моделей:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Пример получения уведомлений для определенной модели:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Необязательный параметр. Если он не указан, вы получите все уведомления для всех моделей. <br>Допустимое значение: уникальный идентификатор модели.|
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ.**

Код состояния HTTP: 200

OData XML

    The response includes one entry per notification, each entry has the following data::
		* feed\entry\content\properties\UserName – The internal user name identification
		* feed\entry\content\properties\ModelId – The model id
		* feed\entry\content\properties\Message – The notification message
		* feed\entry\content\properties\DateCreated – the date that this notification was created in UTC format
		* feed\entry\content\properties\NotificationType – the notification types, values: BuildFailure, RecommendationFailure, DataAquisitionFailure

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v1/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###11.2. Удаление уведомлений модели
Удаление всех прочитанных уведомления для определенной модели

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели. |
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200

###11.3. Удаление уведомлений пользователя
Удаляются все уведомления для всех моделей

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| apiVersion | 1.0 |
|||
| Текст запроса | НЕТ |

**Ответ**.

Код состояния HTTP: 200


 

<!---HONumber=August15_HO6-->