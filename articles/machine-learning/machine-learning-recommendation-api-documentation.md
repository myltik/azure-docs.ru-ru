<properties 
	pageTitle="Документация по интерфейсу API рекомендаций для машинного обучения | Microsoft Azure" 
	description="Документация по интерфейсу API рекомендаций для машинного обучения Azure, предназначенная для системы рекомендаций, доступной в Microsoft Azure Marketplace." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/17/2016" 
	ms.author="LuisCa"/>

#Машинное обучение Azure: документация по интерфейсу API рекомендаций

В этом документе описываются доступные в Marketplace API-интерфейсы для рекомендаций по машинному обучению Microsoft Azure.


> Этот документ относится к API рекомендаций, который будет считаться устаревшим с 31 декабря 2016 г. Вам следует использовать [когнитивную службу API рекомендаций](https://www.microsoft.com/cognitive-services/ru-RU/recommendations-api).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. Общая информация
Данный документ содержит справочные материалы по интерфейсам API. Сначала следует ознакомиться с документом "Рекомендации по Машинному обучению Azure: краткое руководство".

Интерфейсы API рекомендаций для машинного обучения Azure можно разделить на следующие логические группы:

- <ins>ограничения</ins> — ограничения API рекомендаций.
- <ins>общая информация</ins> — сведения об аутентификации, универсальном коде ресурса (URI) службы и управлении версиями.
- <ins>Базовая обработка модели</ins> — интерфейсы API, позволяющие выполнять основные операции с моделью, например создание, обновление или удаление модели.
- <ins>Расширенная обработка модели</ins> — интерфейсы API, позволяющие получать подробные сведения о данных модели.
- <ins>Бизнес-правила модели</ins> — интерфейсы API, позволяющие управлять бизнес-правилами в отношении результатов рекомендаций модели.
- <ins>Каталог</ins> — интерфейсы API, позволяющие выполнять основные операции с каталогом модели. Каталог содержит метаданные по элементам данных об использовании.
- <ins>Функция</ins> — интерфейсы API, позволяющие получить сведения об элементе в каталоге и о том, как использовать эти сведения для создания лучших рекомендаций.
- <ins>Данные по использованию</ins> — интерфейсы API, позволяющие выполнять основные операции с данными по использованию модели. Данные по использованию в базовой форме состоят из строк, содержащих пары &#60;userId&#62;,&#60;itemId&#62;.
- <ins>Сборка</ins> — интерфейсы API, позволяющие инициировать сборку модели и выполнять основные операции, связанные с этой сборкой. Инициировать сборку модели можно, получив значимые данные об использовании.
- <ins>Рекомендации</ins> — интерфейсы API, позволяющие использовать рекомендации после завершения сборки модели.
- <ins>Данные пользователей</ins> — интерфейсы API, которые позволяют получать сведения о работе пользователей.
- <ins>Уведомления</ins> — интерфейсы API, позволяющие получать уведомления о проблемах, связанных с операциями API. Например, если при передаче данных по использованию через систему сбора данных происходит сбой обработки большинства событий, то вызывается сообщение об ошибке.

##2) Ограничения

- Максимальное число моделей на одну подписку — 10.
- Максимальное количество сборок на каждую модель равно 20.
- Максимальное количество элементов в каталоге равно 100 000.
- Максимальное количество поддерживаемых точек использования — около 5 000 000. По мере поступления новых точек будут удаляться самые старые.
- Максимальный размер данных, которые можно отправить в POST (например, импорт данных каталога, импорт данных по использованию), составляет 200 МБ.
- Максимальное число элементов, которое можно запрашивать при получении рекомендаций — 150.

##3) Интерфейсы API: общие сведения

###3\.1. Аутентификация
В вопросах, касающихся проверки подлинности, следуйте рекомендациям Microsoft Azure Marketplace. Магазин поддерживает методы проверки подлинности Basic и OAuth.

###3\.2. URI службы
Корневой код URI служб для интерфейсов API рекомендаций по Машинному обучению Azure находится [здесь](https://api.datamarket.azure.com/amla/recommendations/v3/).

Полный URI службы выражается с помощью элементов спецификации OData.

###3\.3. Версия API
У каждого вызова API будет в конце параметр запроса, называемый apiVersion, который должен иметь значение 1.0.

###3\.4. Идентификаторы с учетом регистра
Идентификаторы, возвращаемые любым интерфейсом API, учитывают регистр и должны использоваться в исходном виде при передаче в качестве параметров в последующих вызовах API. Например, регистр учитывается в идентификаторах моделей и каталогов.

##4\. Качество рекомендаций и неактивные элементы

###4\.1. Качество рекомендаций

Создания модели рекомендаций обычно достаточно для того, чтобы система могла предоставлять рекомендации. Однако качество рекомендаций зависит от обработанных данных по использованию и охвата каталога. Например, если имеется много неактивных элементов (то есть элементов с низкой степенью использования), у системы могут возникнуть трудности с предоставлением рекомендаций для таких элементов или их использованием в качестве рекомендованных. Для решения этой проблемы система позволяет использовать метаданные элементов, которые повышают эффективность рекомендаций. Эти метаданные называются характеристиками. Примерами характеристик могут служить имена автора книги или актера фильма. Характеристики предоставляются через каталог в виде строк "ключ-значение". Полный формат файла каталога см. в [разделе, посвященном импорту каталога](#81-import-catalog-data).

###4\.2. Сборка рейтинга

Характеристики могут повысить эффективность модели рекомендаций, но для этого они должны быть значимыми. В этих целях была введена новая сборка — сборка рейтинга. С ее помощью ранжируется полезность характеристик. Значимая характеристика имеет рейтинг не ниже 2. После определения значимых характеристик запустите сборку рекомендаций со списком (или подсписком) этих характеристик. С помощью характеристик можно повышать эффективность рекомендаций как для активных, так и для неактивных элементов. Чтобы использовать их для активных элементов, следует настроить параметр сборки `UseFeatureInModel`. Чтобы использовать характеристики для неактивных элементов, следует включить параметр сборки `AllowColdItemPlacement`. Примечание. Включить параметр `AllowColdItemPlacement`, не включая `UseFeatureInModel`, нельзя.

###4\.3. Обоснование рекомендаций

Обоснование рекомендаций — еще один аспект использования характеристик. Модуль рекомендаций Машинного обучения Azure может использовать характеристики для предоставления объяснений рекомендаций (то есть их обоснования), что повышает доверие получателя к рекомендациям. Чтобы включить обоснование, перед запросом сборки рекомендаций следует настроить параметры `AllowFeatureCorrelation` и `ReasoningFeatureList`.


##5\. Базовая обработка модели

###5\.1. Создание модели
Создает запрос на создание модели.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelName |	Допустимы только буквы (A-Z, a-z), цифры (0–9), дефисы (-) и символы подчеркивания (\_).<br>Максимальная длина: 20 |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |


**Ответ**:

Код состояния HTTP: 200

- `feed/entry/content/properties/id` — содержит идентификатор модели.
**Примечание**. В идентификаторе модели учитывается регистр.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###5\.2. Получение модели
Создает запрос на получение модели.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	id |	Уникальный идентификатор модели (с учетом регистра) |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Данные модели содержатся в следующих элементах:

- `feed/entry/content/properties/Id` — уникальный идентификатор модели.
- `feed/entry/content/properties/Name` — имя модели.
- `feed/entry/content/properties/Date` — дата создания модели.
- `feed/entry/content/properties/Status` — состояние модели. Один из следующих типов:
    - Created — модель создана и не содержит каталога и данных по использованию;
	- ReadyForBuild — модель создана и содержит каталог и данные по использованию.
- `feed/entry/content/properties/HasActiveBuild` — указывает, создана ли модель успешно.
- `feed/entry/content/properties/BuildId` — идентификатор активной сборки модели.
- `feed/entry/content/properties/Mpr` — средний процентильный рейтинг модели (MPR, подробнее см. в разделе, посвященном параметру ModelInsight).
- `feed/entry/content/properties/UserName` — имя внутреннего пользователя модели.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###5\.3. Получение всех моделей
Извлекает все модели текущего пользователя.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

- `feed/entry/content/properties/Id` — уникальный идентификатор модели.
- `feed/entry/content/properties/Name` — имя модели.
- `feed/entry/content/properties/Date` — дата создания модели.
- `feed/entry/content/properties/Status` — состояние модели. Один из следующих типов:
  - Created — модель создана и не содержит каталога и данных по использованию;
  - ReadyForBuild — модель создана и содержит каталог и данные по использованию.
- `feed/entry/content/properties/HasActiveBuild` — указывает, создана ли модель успешно.
- `feed/entry/content/properties/BuildId` — идентификатор активной сборки модели.
- `feed/entry/content/properties/Mpr` — средний процентильный рейтинг модели (подробнее см. в разделе, посвященном параметру ModelInsight).
- `feed/entry/content/properties/UserName` — имя внутреннего пользователя модели.
- `feed/entry/content/properties/UsageFileNames` — список файлов использования модели через запятую.
- `feed/entry/content/properties/CatalogId` — идентификатор каталога модели.
- `feed/entry/content/properties/Description` — описание модели.
- `feed/entry/content/properties/CatalogFileName` — имя файла каталога модели.

OData XML


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###5\.4. Обновление модели

Можно обновить описание модели или активного идентификатора сборки.<br>
<ins>Активный идентификатор сборки</ins> — каждая сборка для каждой модели имеет идентификатор сборки. Активный идентификатор сборки — это первая успешная сборка каждой новой модели. Как только у вас будет активный идентификатор сборки и вы выполните дополнительные сборки для той же модели, следует явно задать его в качестве идентификатора сборки по умолчанию, если необходимо. Если при использовании рекомендаций не указать идентификатор сборки, который необходимо использовать, автоматически будет использоваться идентификатор по умолчанию.<br>
Этот механизм позволяет при наличии действующей модели рекомендаций в рабочей среде выполнять сборку новых моделей и тестировать их перед выпуском.


| Метод HTTP | URI |
|:--------|:--------|
|ОТПРАВКА |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	id | Уникальный идентификатор модели (с учетом регистра) |
|	версия\_API | 1\.0 |
|||
| Текст запроса | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Обратите внимание, что теги XML Description и ActiveBuildId необязательны. Если вы не хотите задавать Description или ActiveBuildId, удалите весь тег.|

**Ответ**:

Код состояния HTTP: 200

###5\.5. Удаление модели
Удаляет существующую модель по идентификатору.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	id |	Уникальный идентификатор модели (с учетом регистра) |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6\. Расширенная обработка модели

###6\.1. Подробные сведения о данных модели
Возвращает статистические данные по данным использования, на которых основана модель.

Доступно только для сборки рекомендаций.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Данные возвращаются в виде коллекции свойств.

- `feed/entry/id/content/properties/key` — содержит имя свойства.
- `feed/entry/id/content/properties/value` — содержит значение свойства.

В таблице ниже описываются значения, представленные каждым ключом.

|Ключ|Описание|
|:-----|:----|
| AvgItemLength | Среднее число отдельных пользователей на элемент. |
| AvgUserLength | Среднее число отдельных элементов на пользователя. |
| DensificationNumberOfItems | Количество элементов после удаления элементов, которые нельзя смоделировать. |
| DensificationNumberOfUsers | Количество точек использования после удаления пользователей и элементов, которые нельзя смоделировать. |
| DensificationNumberOfRecords | Количество точек использования после удаления пользователей и элементов, которые нельзя смоделировать. |
| MaxItemLength | Количество отдельных пользователей для самого популярного элемента. |
| MaxUserLength | Максимальное число отдельных элементов для пользователя. |
| MinItemLength | Максимальное число отдельных пользователей для элемента. |
| MinUserLength | Минимальное число отдельных элементов для пользователя. |
| RawNumberOfItems | Количество элементов в файлах использования. |
| RawNumberOfUsers | Количество точек использования перед любыми операциями удаления. |
| RawNumberOfRecords | Количество точек использования перед любыми операциями удаления. |
| SamplingNumberOfItems | Недоступно |
| SamplingNumberOfRecords | Недоступно |
| SamplingNumberOfUsers | Недоступно |

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6\.2. Подробные сведения о модели
Возвращает подробные сведения о модели для активной сборки или для конкретной сборки, если она указана.

Доступно только для сборки рекомендаций.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |С активным идентификатором сборки:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>С идентификатором конкретной сборки:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	buildId |	Необязательно — число, которое обозначает успешную сборку. |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Данные возвращаются в виде коллекции свойств.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


В таблице ниже описываются значения, представленные каждым ключом.

| Ключ | Описание |
|:---- |:----|
| CatalogCoverage | Какую часть каталога можно смоделировать с помощью характеристик использования. Для остальных элементов потребуются характеристики на основе содержимого. |
| Mpr | Средний процентильный рейтинг модели. Чем меньше это значение, тем лучше. |
| NumberOfDimensions | Число измерений, используемых алгоритмом факторизации матрицы. |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6\.3. Получение образца модели
Получает образец модели рекомендаций.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>С идентификатором конкретной сборки:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

OData XML

Ответ возвращается в формате необработанного текста.

<pre>
Уровень 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Рейтинг: 0,5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Рейтинг: 0,5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Рейтинг: 0,5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Рейтинг: 0,5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Рейтинг: 0,514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Рейтинг: 0,5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Рейтинг: 0,5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Рейтинг: 0,5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) Рейтинг: 0,5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel Рейтинг: 0,5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Рейтинг: 0,5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Рейтинг: 0,5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Рейтинг: 0,5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Рейтинг: 0,5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel Рейтинг: 0,5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Рейтинг: 0,537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Рейтинг: 0,5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown Рейтинг: 0,5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks Рейтинг: 0,5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Рейтинг: 0,5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived Рейтинг: 0,5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Рейтинг: 0,5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Рейтинг: 0,5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Рейтинг: 0,5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Рейтинг: 0,5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Рейтинг: 0,5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Рейтинг: 0,5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Рейтинг: 0,5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Рейтинг: 0,5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) Рейтинг: 0,5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II Рейтинг: 0,5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball Рейтинг: 0,5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Рейтинг: 0,5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Рейтинг: 0,5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Рейтинг: 0,5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Рейтинг: 0,5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Рейтинг: 0,5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Рейтинг: 0,5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Рейтинг: 0,5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Рейтинг: 0,5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Рейтинг: 0,5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Рейтинг: 0,5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Рейтинг: 0,5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Рейтинг: 0,5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Рейтинг: 0,5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Рейтинг: 0,5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Рейтинг: 0,5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Рейтинг: 0,5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Рейтинг: 0,5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Рейтинг: 0,5367

Уровень 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Рейтинг: 0,5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Рейтинг: 0,5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Рейтинг: 0,5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Рейтинг: 0,5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Рейтинг: 0,5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Рейтинг: 0,5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Рейтинг: 0,543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) Рейтинг: 0,5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Рейтинг: 0,5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Рейтинг: 0,5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Рейтинг: 0,5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Рейтинг: 0,5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Рейтинг: 0,5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Рейтинг: 0,539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Рейтинг: 0,5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings Рейтинг: 0,5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Рейтинг: 0,5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Рейтинг: 0,5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Рейтинг: 0,5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Рейтинг: 0,5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Рейтинг: 0,5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Рейтинг: 0,5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Рейтинг: 0,5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Рейтинг: 0,5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Рейтинг: 0,5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Рейтинг: 0,5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Рейтинг: 0,5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Рейтинг: 0,5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Рейтинг: 0,5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Рейтинг: 0,5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Рейтинг: 0,5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Рейтинг: 0,5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Рейтинг: 0,5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto Рейтинг: 0,5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Рейтинг: 0,5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Рейтинг: 0,5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Рейтинг: 0,5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Рейтинг: 0,5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Рейтинг: 0,5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Рейтинг: 0,54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) Рейтинг: 0,5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Рейтинг: 0,5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Рейтинг: 0,5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Рейтинг: 0,5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Рейтинг: 0,5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Рейтинг: 0,5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Рейтинг: 0,5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Рейтинг: 0,5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Рейтинг: 0,5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Рейтинг: 0,5367

</pre>


##7\. Бизнес-правила модели

Поддерживаются следующие типы правил:
- Правило <strong>Blocklist</strong> позволяет предоставлять список элементов, которые не должны возвращаться с рекомендованными результатами.

- Правило <strong>FeatureBlockList</strong> позволяет блокировать элементы на основе значений его компонентов.

*Не добавляйте в один список блокировок больше 1000 позиций, иначе время ожидания вызова будет превышено. Если вам нужно заблокировать больше 1000 записей, создайте несколько вызовов списка блокировок.*

- Правило <strong>Upsale</strong> позволяет принудительно задать элементы, которые должны возвращаться в результатах рекомендаций.

- Правило <strong>WhiteList</strong> позволяет предлагать рекомендации только из определенного списка элементов.

- Правило <strong>FeatureWhiteList</strong> позволяет рекомендовать только элементы с определенными значениями компонентов.

- Правило <strong>PerSeedBlockList</strong> позволяет указать для каждого элемента список элементов, которые не могут возвращаться как результаты рекомендаций.




###7\.1. Получение правил для модели

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Пример:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

- `feed/entry/content/properties/Id` — уникальный идентификатор правила.
- `feed/entry/content/properties/Type` — тип правила.
- `feed/entry/content/properties/Parameter` — параметр правила.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.2. Добавление правила

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|ЗАГОЛОВОК |`"Content-Type", "text/xml"`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	версия\_API | 1\.0 |
|||
| Текст запроса | 
<ins>Всякий раз, когда для бизнес-правил определяется идентификатор элемента, обязательно используйте внешний идентификатор этого элемента (используемый в файле каталога).</ins><br> 
<ins>Чтобы добавить правило BlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins> 
<ins>Чтобы добавить правило FeatureBlockList:</ins><br> 
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins>
Чтобы добавить правило Upsale:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"],"NumberOfItemsToUpsale":5}</Value></ApiFilter>`<br><br> 
<ins>Чтобы добавить правило WhiteList:</ins><br> 
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>
<ins>Чтобы добавить правило FeatureWhiteList:</ins><br> 
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins>
Чтобы добавить правило PerSeedBlockList:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**Ответ**:

Код состояния HTTP: 200

Интерфейс API возвращает только что созданное правило и его подробные данные. Свойства правила можно получить по следующим путям:

- `feed/entry/content/properties/Id` — уникальный идентификатор правила.
- `feed/entry/content/properties/Type` — тип правила: BlockList или Upsale.
- `feed/entry/content/properties/Parameter` — параметр правила.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.3. Удаление правила

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	filterId |	Уникальный идентификатор фильтра |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

###7\.4. Удаление всех правил

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

##8\. Каталог

###8\.1. Импорт данных каталога

В случае передачи нескольких файлов каталога в одну модель несколькими вызовами мы вставим только новые элементы каталога. Существующие элементы останутся с исходными значениями. С помощью этого метода нельзя обновлять данные каталога.

Данные каталога должны иметь следующий формат:

- Без характеристик: `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- С характеристиками: `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

Примечание. Максимальный размер файла равен 200 МБ.

**Сведения о формате**

| Имя | Обязательно | Тип | Описание |
|:---|:---|:---|:---|
| Идентификатор элемента |Да | [A-z], [a-z], [0-9], [\_] &#40;символ подчеркивания&#41;, [-] &#40;дефис&#41;<br> Максимальная длина: 50 | Уникальный идентификатор элемента. |
| Имя элемента | Да | Любые алфавитно-цифровые символы<br> Максимальная длина: 255 | Имя элемента. | 
| Категория элемента | Да | Любые алфавитно-цифровые символы<br> Максимальная длина: 255 | Категория, к которой относится этот элемент (например, кулинарные книги, драма и т. д.); не может быть пустой. |
| Описание | Нет, если только нет характеристик (но может быть пустым) | Любые алфавитно-цифровые символы<br> Максимальная длина: 4000 | Описание элемента. |
| Список характеристик | Нет | Любые буквенно-цифровые символы<br> Максимальная длина: 4000; максимальное число компонентов: 20 | Разделенный запятыми список компонентов (имя=значение), который позволяет расширить модель рекомендаций. См. раздел [Дополнительные разделы](#2-advanced-topics). |


| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|ЗАГОЛОВОК |`"Content-Type", "text/xml"`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
| filename | Текстовый идентификатор каталога.<br>Допустимы только буквы (A–Z, a–z), цифры (0–9), дефисы (-) и символы подчеркивания (\_).<br>Максимальная длина: 50 |
|	версия\_API | 1\.0 |
|||
| Текст запроса | Пример (с компонентами):<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> | |


**Ответ**:

Код состояния HTTP: 200

API возвращает отчет об импорте.
- `feed\entry\content\properties\LineCount` — количество принятых строк.
- `feed\entry\content\properties\ErrorCount` — количество строк, которые не были вставлены из-за ошибки.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.2. Получение каталога
Извлекает все элементы каталога. Каталог будет извлекаться по одной странице. Если вы хотите получить элементы по заданному индексу, можно использовать параметр $skip odata. Например, если вы хотите получить элементы, начиная с позиции 100, добавьте в запрос параметр $skip=100.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Ответ включает одну запись для каждого элемента каталога. Каждая запись содержит следующие данные:

- `feed/entry/content/properties/ExternalId` — внешний идентификатор элемента каталога, предоставленный клиентом.
- `feed/entry/content/properties/InternalId` — внутренний идентификатор элемента каталога, созданный API рекомендаций Машинного обучения Azure.
- `feed/entry/content/properties/Name` — имя элемента каталога.
- `feed/entry/content/properties/Category` — категория элемента каталога.
- `feed/entry/content/properties/Description` — описание элемента каталога.
- `feed/entry/content/properties/Metadata` — метаданные элемента каталога.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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

###8\.3. Получение элементов каталога по токену

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	token |	Токен имени элемента каталога. Должен содержать не менее трех символов. |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Ответ включает одну запись для каждого элемента каталога. Каждая запись содержит следующие данные:

- `feed/entry/content/properties/InternalId` — внутренний идентификатор элемента каталога, созданный API рекомендаций Машинного обучения Azure.
- `feed/entry/content/properties/Name` — имя элемента каталога.
- `feed/entry/content/properties/Rating` — (для будущего использования).
- `feed/entry/content/properties/Reasoning` — (для будущего использования).
- `feed/entry/content/properties/Metadata` — (для будущего использования).
- `feed/entry/content/properties/FormattedRating` — (для будущего использования).

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

##9\. Данные об использовании
###9\.1. Импорт данных по использованию
####9\.1.1. Передача файла
В этом разделе показано, как передать данные по использованию с помощью файла. Можно вызывать этот API несколько раз с данными об использовании. Для всех вызовов сохранятся все данные об использовании.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
| filename | Текстовый идентификатор каталога.<br>Допустимы только буквы (A–Z, a–z), цифры (0–9), дефисы (-) и символы подчеркивания (\_).<br>Максимальная длина: 50 |
|	версия\_API | 1\.0 |
|||
| Текст запроса | Данные об использовании. Формат:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Имя</th><th>Обязательный</th><th>Тип</th><th>Описание</th></tr><tr><td>Идентификатор пользователя</td><td>Да</td><td>[A–z], [a–z], [0–9], [\_] &#40;символ подчеркивания&#41;, [-] &#40;дефис&#41;<br> Максимальная длина: 255.</td><td> Уникальный идентификатор пользователя.</td></tr><tr><td>Идентификатор элемента</td><td>Да</td><td>[A–z], [a–z], [0–9], [&#95;] &#40;символ подчеркивания&#41;, [-] &#40;дефис&#41;<br> Максимальная длина: 50.</td><td>Уникальный идентификатор элемента.</td></tr><tr><td>Время</td><td>Нет</td><td>Дата в формате: ГГГГ/ММ/ДДTЧЧ:ММ:СС (например, 2013/06/20T10:00:00).</td><td>Время данных.</td></tr><tr><td>Событие</td><td>Нет. Если этот параметр указан, необходимо также указать дату.</td><td>Одно из следующих значений:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Максимальный размер файла: 200 МБ.<br><br>Пример:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Ответ**:

Код состояния HTTP: 200

- `Feed\entry\content\properties\LineCount` — количество принятых строк.
- `Feed\entry\content\properties\ErrorCount` — количество строк, которые не были вставлены из-за ошибки.
- `Feed\entry\content\properties\FileId` — идентификатор файла.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9\.1.2. Использование функции сбора данных
В этом разделе показано, как отправлять события в режиме реального времени в службу рекомендаций Машинного обучения Azure с веб-сайта.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|ЗАГОЛОВОК |`"Content-Type", "text/xml"`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	версия\_API | 1\.0 |
|Тело запроса| Ввод данных события для каждого события, которое нужно отправить. Одному и тому же пользователю или сеансу браузера следует отправлять один и тот же идентификатор в поле SessionId. (Пример текста события см. ниже.)|


- Пример события Click:

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

- Пример события RecommendationClick:

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

- Пример события AddShopCart:

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

- Пример события RemoveShopCart:

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

- Пример события Purchase:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
			<EventData>
				<Name>Purchase</Name>
				<PurchaseItems>
					<PurchaseItem>
						<ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
						<Count>1</Count>
					</PurchaseItem>
					<PurchaseItem>
						<ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
						<Count>3</Count>
					</PurchaseItem>
				</PurchaseItems>
			</EventData>
		</EventData>
		</Event>
		
		
		



- Пример отправки событий двух типов, Click и AddShopCart:

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

###9\.2. Вывод списка файлов использования модели
Извлекает метаданные из всех файлов использования модели. Файлы использования будут извлекаться по одной странице. Каждая страница содержит 100 элементов. Если вы хотите получить элементы по заданному индексу, можно использовать параметр $skip odata. Например, если вы хотите получить элементы, начиная с позиции 100, добавьте в запрос параметр $skip=100.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	forModelId |	Уникальный идентификатор модели |
|	версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Ответ включает одну запись для каждого файла использования. Каждая запись содержит следующие данные:

- `feed\entry\content\properties\Id` — идентификатор файла использования.
- `feed\entry\content\properties\Length` — размер файла использования в мегабайтах.
- `feed\entry\content\properties\DateModified` — дата создания файла использования.
- `feed\entry\content\properties\UseInModel` — применяется ли файл использования в модели.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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

###9\.3. Получение статистики использования
Получает статистику использования.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
| startDate |	Дата начала. Формат: гггг/ММ/ддТЧЧ:мм:сс |
| endDate |	Дата окончания. Формат: гггг/ММ/ддТЧЧ:мм:сс |
| eventTypes |	Строка разделенных запятыми типов событий или null для получения всех событий. |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Коллекция элементов "ключ-значение". Каждый элемент содержит суммарное число событий определенного типа за указанный час.

- `feed\entry[i]\content\properties\Key` — содержит время (с группировкой по часам) и тип события.
- `feed\entry[i]\content\properties\Value` — общее число событий.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9\.4. Получение образца файла использования
Извлекает первые 2 КБ содержимого из файла использования.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
| fileId |	Уникальный идентификатор файла использования модели. |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Ответ возвращается в формате необработанного текста.
<pre>
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


###9\.5. Получение файла использования модели
Извлекает все содержимое файла использования.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| mid |	Уникальный идентификатор модели |
| fid |	Уникальный идентификатор файла использования модели. |
| загрузить | 1 |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Ответ возвращается в формате необработанного текста.
<pre>
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

###9\.6. Удаление файла использования
Удаляет указанный файл использования.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
| fileId | Уникальный идентификатор файла, который нужно удалить |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200


###9\.7. Удаление всех файлов использования
Удаляет все файлы использования модели.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

##10\. Функции
В этом разделе показано, как извлечь информацию о характеристиках, например импортированные характеристики и их значения, рейтинг и время назначения рейтинга. Характеристики импортируются вместе с данными каталога, а связывание рейтингов осуществляется при сборке рейтинга. Рейтинг характеристики может изменяться в соответствии с особенностями данных по использованию и типом элементов. Но для согласованности данных по использованию и элементов рейтинг должен меняться в незначительных пределах. Рейтинг характеристики должен быть неотрицательным числом. Число 0 означает, что характеристике не присвоен рейтинг (такое происходит, если этот интерфейс API был вызван до завершения первой сборки рейтинга). Дата назначения рейтинга называется возрастом оценки.

###10\.1. Получение информации о характеристиках (для последней сборки рейтинга)
Извлекает информацию о характеристиках, включая рейтинги, для последней успешной сборки рейтинга.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
|samplingSize| Количество значений, которое нужно включить для каждой характеристики в соответствии с данными в каталоге. <br/>Возможные значения:<br> –1 — все образцы; <br>0 — ни одного образца; <br>N — возвращается N образцов для каждой характеристики.|
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |


**Ответ**:

Код состояния HTTP: 200

Ответ содержит список записей с информацией о характеристиках. Каждая запись содержит следующие сведения:

- `feed/entry/content/m:properties/d:Name` — название характеристики.
- `feed/entry/content/m:properties/d:RankUpdateDate` — дата, в которую этой характеристике был назначен рейтинг, то есть возраст оценки характеристики. Историческая дата ("0001-01-01T00:00:00") означает, что сборка рейтинга еще не выполнялась.
- `feed/entry/content/m:properties/d:Rank` — рейтинг характеристики (число с плавающей запятой). При рейтинге не менее 2,0 характеристика считается значимой.
- `feed/entry/content/m:properties/d:SampleValues` — разделенный запятыми список значений до нужного размера выборки.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10\.2. Получение информации о характеристиках (для определенной сборки рейтинга)

Извлекает информацию о характеристиках, включая рейтинги, для определенной сборки рейтинга.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| Имя параметра |	Допустимые значения |
|:--------			|:--------			|
| modelId |	Уникальный идентификатор модели |
|samplingSize| Количество значений, которое нужно включить для каждой характеристики в соответствии с данными в каталоге.<br/> Возможные значения:<br> –1 — все образцы; <br>0 — ни одного образца; <br>N — возвращается N образцов для каждой характеристики.|
|rankBuildId| Уникальный идентификатор сборки рейтинга или –1 для последней сборки|
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |


**Ответ**:

Код состояния HTTP: 200

Ответ содержит список записей с информацией о характеристиках. Каждая запись содержит следующие сведения:

- `feed/entry/content/m:properties/d:Name` — название характеристики.
- `feed/entry/content/m:properties/d:RankUpdateDate` — дата, в которую этой характеристике был назначен рейтинг, то есть возраст оценки характеристики. Историческая дата ("0001-01-01T00:00:00") означает, что сборка рейтинга еще не выполнялась.
- `feed/entry/content/m:properties/d:Rank` — рейтинг характеристики (число с плавающей запятой). При рейтинге не менее 2,0 характеристика считается значимой.
- `feed/entry/content/m:properties/d:SampleValues` — разделенный запятыми список значений до нужного размера выборки.

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11\. Создание

  В этом разделе описываются различные интерфейсы API, связанные со сборками. Существует три типа сборок: сборка рекомендаций, сборка рейтингов и сборка FBT (часто покупаемая вместе с ними).

Сборка рекомендаций служит для создания модели рекомендаций, используемой для прогнозов. Прогнозы (для сборок этого типа) бывают двух типов:
* I2I — т. н. рекомендации "элемент-элемент" — получив элемент или список элементов, эта функция прогнозирует список элементов, которые могут представлять большой интерес.
* U2I — т. н. рекомендации "пользователь-элемент" — получив идентификатор пользователя (а при необходимости и список элементов), эта функция прогнозирует список элементов, которые могут представлять большой интерес для соответствующего пользователя (и его дополнительный выбор элементов). Рекомендации U2I основаны на журнале элементов, которые представляли интерес для пользователя до момента создания модели.

Сборка рейтингов — это техническая сборка, которая позволяет определять полезность характеристик. Обычно, чтобы получить оптимальную модель рекомендаций с использованием характеристик, нужно выполнить следующие действия:
- Запустить сборку рейтингов (если только оценка характеристик не является постоянной) и дождаться получения оценки характеристик.
- Получить рейтинги компонентов, вызвав API [получения информации о компонентах](#101-get-features-info-for-last-rank-build).
- Настроить сборку рекомендаций со следующими параметрами:
	- `useFeatureInModel` — присвойте значение True.
	- `ModelingFeatureList` — присвойте разделенный запятыми список компонентов с рейтингом не менее 2,0 (в соответствии с рейтингами, полученными на предыдущем шаге).
	- `AllowColdItemPlacement` — присвойте значение True.
	- Кроме того, параметру `EnableFeatureCorrelation` можно присвоить значение True, а параметру `ReasoningFeatureList` — список компонентов, которые будут использоваться в качестве объяснений (обычно это тот же список, который используется при моделировании, или его часть).
- Запустите сборку рекомендаций с настроенными параметрами.

Примечание. Если не настроить параметры (то есть запустить сборку рекомендаций без параметров) или не отключить использование характеристик явным образом (например, присвоив параметру `UseFeatureInModel` значение False), система присвоит параметрам, связанным с характеристикам, приведенные выше значения, если существует сборка рейтингов.

В отношении параллельного выполнения сборки рейтингов и сборки рекомендаций для одной и той же модели нет никаких ограничений. Однако одновременно выполнять две сборки одного типа для модели нельзя.

Сборка FBT (Frequently Bought Together, "С этим товаром часто покупают") — это еще один алгоритм рекомендаций, иногда называемый консервативным. Он подходит для каталогов, которые по своему характеру являются неоднородными (однородные каталоги: книги, фильмы, продукты питания, одежда; неоднородные: компьютеры и устройства, товары из разных областей, большое многообразие).

Примечание. Если переданные файлы использования содержат необязательное поле "тип события", то для моделирования по алгоритму FBT будут использоваться только события Purchase. Если тип события не указан, все события будут рассматриваться как покупки.


####11\.1. Параметры сборки

Каждый тип сборки можно настроить с помощью набора параметров (описанных ниже). Если не настроить параметры, система автоматически присвоит им значения в соответствии с информацией, имеющейся на момент запуска сборки.

#####11\.1.1. Уплотнение данных по использованию
Пользователи и элементы с небольшим числом точек использования могут создавать больше шума, чем информации. Система пытается предсказать минимальное число точек использования на одного пользователя или элемент, которое следует использовать в модели. Это значение будет находиться в диапазоне, ограничиваемом параметрами ItemCutoffLowerBound и ItemCutoffUpperBound для элементов и параметрами UserCutOffLowerBound и UserCutoffUpperBound для пользователей. Эффект уплотнения можно свести к минимуму для элементов или пользователей, задав по крайней мере одну из соответствующих границ равной нулю.

#####11\.1.2. Параметры сборки рейтингов

В таблице ниже описываются параметры сборки рейтингов.

|Ключ|Описание|Тип|Допустимое значение|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Число итераций, выполняемых моделью, сказывается на общей длительности вычисления и точности модели. Чем оно больше, тем выше будет точность, но вычисление займет больше времени.| Число | 10–50 |
| NumberOfModelDimensions | Число измерений связано с числом характеристик, которые модель попытается выявить в ваших данных. Увеличив число измерений, можно разделить результаты на кластеры меньшего размера. Однако если измерений слишком много, модели будет трудно установить взаимосвязи между элементами. | Число | 10-40 |
|ItemCutOffLowerBound| Определяет нижнюю границу элемента для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|ItemCutOffUpperBound| Определяет верхнюю границу элемента для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|UserCutOffLowerBound| Определяет нижнюю границу пользователя для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|UserCutOffUpperBound| Определяет верхнюю границу пользователя для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |

#####11\.1.3. Параметры сборки рекомендаций
В таблице ниже описываются параметры сборки рекомендаций.

|Ключ|Description (Описание)|Тип|Допустимое значение|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Число итераций, выполняемых моделью, сказывается на общей длительности вычисления и точности модели. Чем оно больше, тем выше будет точность, но вычисление займет больше времени.| Число | 10–50 |
| NumberOfModelDimensions | Число измерений связано с числом характеристик, которые модель попытается выявить в ваших данных. Увеличив число измерений, можно разделить результаты на кластеры меньшего размера. Однако если измерений слишком много, модели будет трудно установить взаимосвязи между элементами. | Число | 10-40 |
|ItemCutOffLowerBound| Определяет нижнюю границу элемента для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|ItemCutOffUpperBound| Определяет верхнюю границу элемента для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|UserCutOffLowerBound| Определяет нижнюю границу пользователя для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|UserCutOffUpperBound| Определяет верхнюю границу пользователя для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
| Description (Описание) | Описание сборки. | Строка | Любой текст, максимум 512 символов. |
| EnableModelingInsights | Позволяет вычислять показатели модели рекомендаций. | Логический | True или False |
| UseFeaturesInModel | Указывает, можно ли использовать характеристики для повышения эффективности модели рекомендаций. | Логический | True или False |
| ModelingFeatureList | Разделенный запятыми список названий характеристик, которые будут использоваться при сборке рекомендаций для повышения эффективности модели рекомендаций. | Строка | Названия характеристик, до 512 символов |
| AllowColdItemPlacement | Указывает, должны ли в рекомендации предоставляться неактивные элементы на основе сходства характеристик. | Логический | True или False |
| EnableFeatureCorrelation | Указывает, можно ли использовать характеристику в обосновании. | Логический | True или False |
| ReasoningFeatureList | Разделенный запятыми список названий характеристик, которые будут использоваться в тексте обоснования (например, в объяснении рекомендации). | Строка | Названия характеристик, до 512 символов |
| EnableU2I | Разрешить личные рекомендации, т. н. U2I (рекомендации "пользователь-элемент"). | Логический | True или False (по умолчанию True) |

#####11\.1.4. Параметры сборки FBT
В таблице ниже описываются параметры сборки рекомендаций.

|Ключ|Description (Описание)|Тип|Допустимое значение (по умолчанию)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | Насколько консервативной является модель. Число одновременных вхождений элементов, которое следует учитывать при моделировании.| Число | 3–50 (6) |
|FbtMaxItemSetSize | Ограничивает число элементов в наборе часто встречающихся.| Число | 2–3 (2) |
|FbtMinimalScore | Минимальный рейтинг, который должен иметь набор часто встречающихся элементов для включения в возвращаемые результаты. Чем выше это значение, тем лучше.| Double | 0 и выше (0) |
|FbtSimilarityFunction | Определяет функцию подобия для использования в процессе построения. Увеличение повышает вероятность точности прогнозов, совместное появление повышает предсказуемость. Коэффициент Жаккара является хорошим компромиссом. | Строка | cooccurrence, точность, jaccard (точность) |


###11\.2. Запуск сборки рекомендаций

  По умолчанию этот интерфейс API запускает сборку модели рекомендаций. Для запуска сборки рейтингов (с целью оценки характеристик) следует использовать вариант API сборки с параметром типа сборки.


| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|ЗАГОЛОВОК |`"Content-Type", "text/xml"` (при отправке текста запроса)|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
| userDescription | Текстовый идентификатор каталога. Обратите внимание: при использовании пробелов их необходимо заменить сочетанием символов %20 См. пример выше.<br>Максимальная длина: 50 |
| версия\_API | 1\.0 |
|||
| Текст запроса | Если оставить это поле пустым, сборка будет выполняться с параметрами по умолчанию.<br><br>Чтобы определить параметры сборки, отправьте их в формате XML в тексте запроса, как в примере ниже. (Описание параметров см. в разделе "Параметры сборки".)`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**Ответ**:

Код состояния HTTP: 200

Это асинхронный интерфейс API. В качестве ответа вы получите идентификатор сборки. Чтобы узнать, завершилась ли сборка, вызовите API "Получение статусов сборок модели" и найдите этот идентификатор сборки в ответе. Имейте в виду, что сборка может длиться от нескольких минут до нескольких часов в зависимости от размера данных.

Пока сборка не завершится, использовать рекомендации нельзя.

Допустимые состояния сборки:

- Create — запрос сборки был создан.
- Queued — запрос сборки был отправлен и поставлен в очередь.
- Building — выполняется сборка.
- Success — сборка успешно завершилась;
- Error — сборка завершилась сбоем;
- Cancelled — сборка отменена;
- Cancelling — отправлен запрос на отмену сборки.


Обратите внимание на то, что идентификатор сборки можно найти по следующему пути: `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###11\.3. Запуск сборки (рекомендаций, рейтингов или FBT)

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|ЗАГОЛОВОК |`"Content-Type", "text/xml"` (при отправке текста запроса)|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели |
| userDescription | Текстовый идентификатор каталога. Обратите внимание: при использовании пробелов их необходимо заменить сочетанием символов %20 См. пример выше.<br>Максимальная длина: 50 |
| buildType | Тип вызываемой сборки: <br/> – Recommendation — для сборки рекомендаций; <br> – Ranking — для сборки рейтингов; <br/> – Fbt — для сборки FBT.
| версия\_API | 1\.0 |
|||
| Текст запроса | Если оставить это поле пустым, сборка будет выполняться с параметрами по умолчанию.<br><br>Чтобы определить параметры сборки, отправьте их в формате XML в тексте запроса, как в примере ниже. (Описание и полный список параметров см. в разделе "Параметры сборки".)`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**Ответ**:

Код состояния HTTP: 200

Это асинхронный интерфейс API. В качестве ответа вы получите идентификатор сборки. Чтобы узнать, завершилась ли сборка, вызовите API "Получение статусов сборок модели" и найдите этот идентификатор сборки в ответе. Имейте в виду, что сборка может длиться от нескольких минут до нескольких часов в зависимости от размера данных.

Пока сборка не завершится, использовать рекомендации нельзя.

Допустимые состояния сборки:

- Create — модель создана;
- Queued — сборка модели запущена и поставлена в очередь;
- Building — сборка модели выполняется;
- Success — сборка успешно завершилась;
- Error — сборка завершилась сбоем;
- Cancelled — сборка отменена;
- Cancelling — сборка отменяется.

Обратите внимание на то, что идентификатор сборки можно найти по следующему пути: `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




###11\.4. Получение статусов сборок модели
Извлекает сборки определенной модели с указанием их состояния.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели |
|	onlyLastBuild |	Указывает, следует ли вернуть весь журнал сборок модели или только состояние последней сборки. |
|	версия\_API |	1\.0 |


**Ответ**:

Код состояния HTTP: 200

Ответ включает одну запись для каждой сборки. Каждая запись содержит следующие данные:

- `feed/entry/content/properties/UserName` — имя пользователя.
- `feed/entry/content/properties/ModelName` — имя модели.
- `feed/entry/content/properties/ModelId` — уникальный идентификатор модели.
- `feed/entry/content/properties/IsDeployed` — развертывается ли сборка (то есть является ли она активной).
- `feed/entry/content/properties/BuildId` — уникальный идентификатор сборки.
- `feed/entry/content/properties/BuildType` — тип сборки.
- `feed/entry/content/properties/Status` — состояние сборки. Может иметь одно из следующих значений: Error, Building, Queued, Cancelling, Cancelled, Success.
- `feed/entry/content/properties/StatusMessage` — подробное сообщение о состоянии (относится только к некоторым состояниям).
- `feed/entry/content/properties/Progress` — ход выполнения сборки (%).
- `feed/entry/content/properties/StartTime` — время начала сборки.
- `feed/entry/content/properties/EndTime` — время окончания сборки.
- `feed/entry/content/properties/ExecutionTime` — длительность сборки.
- `feed/entry/content/properties/ProgressStep` — сведения о текущем этапе выполняемой сборки.

Допустимые состояния сборки:
- Created — запись запроса сборки создана;
- Queued — запрос сборки инициирован и помещен в очередь;
- Building — сборка выполняется;
- Success — сборка успешно завершилась;
- Error — сборка завершилась сбоем;
- Cancelled — сборка отменена;
- Cancelling — сборка отменяется.

Допустимые значения типа сборки:
- Rank — сборка рейтинга;
- Recommendation — сборка рекомендаций.


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
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


###11\.5. Получение состояний сборок
Извлекает состояния сборок всех моделей пользователя.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	onlyLastBuild |	Указывает, следует вернуть весь журнал сборок модели или только состояние последней сборки. |
|	версия\_API |	1\.0 |


**Ответ**:

Код состояния HTTP: 200

Ответ включает одну запись для каждой сборки. Каждая запись содержит следующие данные:

- `feed/entry/content/properties/UserName` — имя пользователя.
- `feed/entry/content/properties/ModelName` — имя модели.
- `feed/entry/content/properties/ModelId` — уникальный идентификатор модели.
- `feed/entry/content/properties/IsDeployed` — развертывается ли сборка.
- `feed/entry/content/properties/BuildId` — уникальный идентификатор сборки.
- `feed/entry/content/properties/BuildType` — тип сборки.
- `feed/entry/content/properties/Status` — состояние сборки. Может иметь одно из следующих значений: Error, Building, Queued, Cancelled, Cancelling, Success.
- `feed/entry/content/properties/StatusMessage` — подробное сообщение о состоянии (относится только к некоторым состояниям).
- `feed/entry/content/properties/Progress` — ход выполнения сборки (%).
- `feed/entry/content/properties/StartTime` — время начала сборки.
- `feed/entry/content/properties/EndTime` — время окончания сборки.
- `feed/entry/content/properties/ExecutionTime` — длительность сборки.
- `feed/entry/content/properties/ProgressStep` — сведения о текущем этапе выполняемой сборки.

Допустимые состояния сборки:
- Created — запись запроса сборки создана;
- Queued — запрос сборки инициирован и помещен в очередь;
- Building — сборка выполняется;
- Success — сборка успешно завершилась;
- Error — сборка завершилась сбоем;
- Cancelled — сборка отменена;
- Cancelling — сборка отменяется.


Допустимые значения типа сборки:
- Rank — сборка рейтинга;
- Recommendation — сборка рекомендаций.


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
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


###11\.6. Удаление сборки
Удаление сборки

Примечание. <br>Активную сборку удалить нельзя. Перед удалением активной сборки необходимо обновить модель до другой активной сборки.<br>Выполняющуюся сборку удалить нельзя. Сначала нужно отменить сборку, вызвав API <strong>Отмена сборки</strong>.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| buildId | Уникальный идентификатор сборки. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200

###11\.7. Отмена сборки
Отменяет выполняющуюся сборку.

| Метод HTTP | URI |
|:--------|:--------|
|ОТПРАВКА |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| buildId | Уникальный идентификатор сборки. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200

###11\.8. Получение параметров сборки
Извлекает параметры сборки.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| buildId | Уникальный идентификатор сборки. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200

Этот интерфейс API возвращает коллекцию элементов "ключ-значение". Каждый элемент представляет параметр и его значение:
- `feed/entry/content/properties/Key` — имя параметра сборки.
- `feed/entry/content/properties/Value` — значение параметра сборки.

В таблице ниже описываются значения, представленные каждым ключом.

|Ключ|Description (Описание)|Тип|Допустимое значение|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Число итераций, выполняемых моделью, сказывается на общей длительности вычисления и точности модели. Чем оно больше, тем выше будет точность, но вычисление займет больше времени.| Число | 10–50 |
| NumberOfModelDimensions | Число измерений связано с числом характеристик, которые модель попытается выявить в ваших данных. Увеличив число измерений, можно разделить результаты на кластеры меньшего размера. Однако если измерений слишком много, модели будет трудно установить взаимосвязи между элементами. | Число | 10-40 |
|ItemCutOffLowerBound| Определяет нижнюю границу элемента для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|ItemCutOffUpperBound| Определяет верхнюю границу элемента для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|UserCutOffLowerBound| Определяет нижнюю границу пользователя для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
|UserCutOffUpperBound| Определяет верхнюю границу пользователя для уплотнения. См. раздел, посвященный уплотнению данных по использованию, выше. | Число | 2 или более (0 — отключить уплотнение) |
| Описание | Описание сборки. | Строка | Любой текст, максимум 512 символов. |
| EnableModelingInsights | Позволяет вычислять показатели модели рекомендаций. | Логический | True или False |
| UseFeaturesInModel | Указывает, можно ли использовать характеристики для повышения эффективности модели рекомендаций. | Логический | True или False |
| ModelingFeatureList | Разделенный запятыми список названий характеристик, которые будут использоваться при сборке рекомендаций для повышения эффективности модели рекомендаций. | Строка | Названия характеристик, до 512 символов |
| AllowColdItemPlacement | Указывает, должны ли в рекомендации предоставляться неактивные элементы на основе сходства характеристик. | Логический | True или False |
| EnableFeatureCorrelation | Указывает, можно ли использовать характеристику в обосновании. | Логический | True или False |
| ReasoningFeatureList | Разделенный запятыми список названий характеристик, которые будут использоваться в тексте обоснования (например, в объяснении рекомендации). | Строка | Названия характеристик, до 512 символов |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12\. Рекомендации
###12\.1. Получение рекомендаций по элементам (для активной сборки)

Получение рекомендаций активной сборки типа Recommendation или Fbt по списку входящих элементов.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| itemIds | Разделенный запятыми список элементов, для которых предоставляются рекомендации. <br>Если активная сборка имеет тип FBT, можно отправить только один элемент. <br>Максимальная длина: 1024 |
| numberOfResults | Требуемое число результатов. <br>Максимум: 150. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Приведенный ниже пример ответа содержит 10 рекомендованных элементов.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

###12\.2. Получение рекомендаций по элементам (для определенной сборки)

Получение рекомендаций определенной сборки типа Recommendation или Fbt.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| itemIds | Разделенный запятыми список элементов, для которых предоставляются рекомендации. <br>Если активная сборка имеет тип FBT, можно отправить только один элемент. <br>Максимальная длина: 1024 |
| numberOfResults | Требуемое число результатов. <br>Максимум: 150. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false
| buildId | Идентификатор сборки, используемый для этого запроса рекомендаций. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Пример ответа см. в подразделе 12.1.

###12\.3. Получение рекомендаций FBT (для активной сборки)

Получение рекомендаций типа Fbt для активной сборки по входящему элементу.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| itemId | Элемент, для которого создаются рекомендации. <br>Максимальная длина: 1024 |
| numberOfResults | Требуемое число результатов. <br>Максимум: 150. |
| minimalScore | Минимальный рейтинг, который должен иметь набор часто встречающихся элементов для включения в возвращаемые результаты |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает по одной записи на рекомендуемый набор элементов (набор элементов, которые обычно покупаются вместе с входящим элементом). Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id1` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name1` — имя элемента.
- `Feed\entry\content\properties\Id2` — имя второго рекомендованного элемента (необязательно).
- `Feed\entry\content\properties\Name2` — имя второго элемента (необязательно).
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Приведенный ниже пример ответа содержит 3 рекомендованных набора элементов.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.4. Получение рекомендаций FBT (для определенной сборки)

Получение рекомендаций типа Fbt для определенной сборки.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| itemId | Элемент, для которого создаются рекомендации. <br>Максимальная длина: 1024 |
| numberOfResults | Требуемое число результатов. <br>Максимум: 150. |
| minimalScore | Минимальный рейтинг, который должен иметь набор часто встречающихся элементов для включения в возвращаемые результаты |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| buildId | Идентификатор сборки, используемый для этого запроса рекомендаций. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает по одной записи на рекомендуемый набор элементов (набор элементов, которые обычно покупаются вместе с входящим элементом). Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id1` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name1` — имя элемента.
- `Feed\entry\content\properties\Id2` — имя второго рекомендованного элемента (необязательно).
- `Feed\entry\content\properties\Name2` — имя второго элемента (необязательно).
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Пример ответа см. в подразделе 12.3.

###12\.5. Получение пользовательских рекомендаций (для активной сборки)

Получение пользовательских рекомендаций для сборки типа Recommendation, отмеченной как активная сборка.

API вернет список прогнозируемых элементов на основе журнала работы пользователя.

Примечания:
 1. Для сборки FBT нет пользовательских рекомендаций.
 2. Если активной сборкой является FBT, этот метод вернет ошибку.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| userId | Уникальный идентификатор пользователя |
| numberOfResults | Требуемое число результатов. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Пример ответа см. в подразделе 12.1.

###12\.6. Получение пользовательских рекомендаций со списком элементов (для активной сборки)

Получение пользовательских рекомендаций для сборки типа Recommendation, отмеченной как активная сборка, с дополнительным списком элементов.

API вернет список прогнозируемых элементов на основе журнала работы пользователя и предоставленных дополнительных элементов.

Примечания:
 1. Для сборки FBT нет пользовательских рекомендаций.
 2. Если активной сборкой является FBT, этот метод вернет ошибку.


| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| userId | Уникальный идентификатор пользователя |
| itemsIds | Разделенный запятыми список элементов, для которых предоставляются рекомендации. Максимальная длина: 1024 |
| numberOfResults | Требуемое число результатов. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Пример ответа см. в подразделе 12.1.

###12\.7. Получение пользовательских рекомендаций (для определенной сборки)

Получение пользовательских рекомендаций для определенной сборки типа Recommendation.

API вернет список прогнозируемых элементов на основе журнала работы пользователя (используемой в определенной сборке).

Примечание. Для сборки FBT нет пользовательских рекомендаций.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| userId | Уникальный идентификатор пользователя |
| numberOfResults | Требуемое число результатов. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| buildId | Идентификатор сборки, используемый для этого запроса рекомендаций. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Пример ответа см. в подразделе 12.1.


###12\.8. Получение пользовательских рекомендаций со списком элементов (для определенной сборки)

Получение пользовательских рекомендаций для определенной сборки типа Recommendation и списка дополнительных элементов.

API вернет список прогнозируемых элементов на основе журнала работы пользователя и дополнительного списка элементов.

Примечание. Для сборки FBT нет пользовательских рекомендаций.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| userId | Уникальный идентификатор пользователя |
| itemIds | Разделенный запятыми список элементов, для которых предоставляются рекомендации. Максимальная длина: 1024 |
| numberOfResults | Требуемое число результатов. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| buildId | Идентификатор сборки, используемый для этого запроса рекомендаций. |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200


Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень достоверности.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

Пример ответа см. в подразделе 12.1.

##13\. Журнал работы пользователей
После создания модели рекомендаций система позволит получить журнал пользователя (элементы, связанные с определенным пользователем), используемый для сборки. Этот API позволяет получать журналы пользователей

Примечание. В настоящий момент журнал пользователя доступен только для сборок рекомендаций.

###13\.1 Получения журнала пользователя
Получение списка элементов, используемых в активной сборке или сборке, указанной для определенного пользователя.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ | Получение журнала пользователя для активной сборки.<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>Получение журнала пользователя для определенной сборки `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>Пример:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели.|
| userId | Уникальный идентификатор пользователя.
| buildId | Необязательный параметр, который указывает, из какой сборки следует получать журнал пользователя
| версия\_API | 1\.0 |


**Ответ.**

Код состояния HTTP: 200

Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:
- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — недоступно.
- `Feed\entry\content\properties\Reasoning` — недоступно.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14\. Уведомления
Если в системе постоянно происходят сбои, модуль рекомендаций Машинного обучения Azure создает уведомления. Существует 3 типа уведомлений:
1.	Сбой сборки — срабатывает при каждом сбое сборки.
2.	Сбой обработки в системе получения данных — срабатывает в том случае, если при обработке событий использования происходит более 100 ошибок на модель в течение 5 минут.
3.	Сбой получения рекомендаций — срабатывает в том случае, если при обработке запросов рекомендаций происходит более 100 ошибок на модель в течение 5 минут.


###14\.1. Получение уведомлений
Извлекает все уведомления для всех моделей или только для одной из них.

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Получение всех уведомлений для всех моделей:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Пример получения уведомлений для определенной модели:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Необязательный параметр. Если он не указан, вы получите все уведомления для всех моделей. <br>Допустимое значение: уникальный идентификатор модели.|
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ.**

Код состояния HTTP: 200

OData XML

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###14\.2. Удаление уведомлений модели
Удаляет все прочитанные уведомления для модели.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

###14\.2. Удаление уведомлений пользователя
Удаляет все уведомления для всех моделей.

| Метод HTTP | URI |
|:--------|:--------|
|УДАЛИТЬ |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200




## 15\. Юридическая информация
Данный документ предоставляется "как есть". Сведения и мнения, содержащиеся в этом документе, включая URL-адреса и другие ссылки на веб-сайты, могут быть изменены без предварительного уведомления.<br><br>
Некоторые примеры, содержащиеся в данном документе, являются вымышленными и приводятся исключительно в демонстрационных целях. Никакой реальной связи нет и не предполагается.<br><br>
Настоящий документ не предоставляет юридических прав на интеллектуальную собственность в отношении продуктов корпорации Майкрософт. Вы можете скопировать и использовать данный документ для внутренних справочных целей.<br><br>
© Корпорация Майкрософт (Microsoft Corporation), 2015 г. Все права защищены.
 

<!---HONumber=AcomDC_0824_2016-->