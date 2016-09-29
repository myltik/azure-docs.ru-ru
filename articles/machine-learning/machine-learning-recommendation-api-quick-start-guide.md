<properties 
	pageTitle="Краткое руководство: API рекомендаций для машинного обучения | Microsoft Azure" 
	description="Рекомендации по Машинному обучению Azure. Краткое руководство по началу работы" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="luisca"/>

# Краткое руководство по API рекомендаций для машинного обучения

>[AZURE.NOTE] Начните использовать когнитивную службу API рекомендаций вместо этой версии. Когнитивная служба рекомендаций заменит эту службу, и все новые функции будут разрабатываться в ней. Она включает в себя новые возможности, такие как поддержка пакетной обработки, улучшенный обозреватель API, более четкое представление API, более согласованные процедуры регистрации и выставления счетов, и т. д. Дополнительные сведения см. в статье [Migrating to the new Cognitive Service](http://aka.ms/recomigrate) (Переход на новую когнитивную службу).


В этом документе описывается, как настроить вашу службу или приложение для использования службы рекомендаций машинного обучения Microsoft Azure. Дополнительную информацию об API рекомендаций можно найти в [коллекции](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Общая информация

Для использования службы рекомендаций машинного обучения Azure необходимо сделать следующее.

* Создать модель. Модель — это контейнер с данными об использовании, данными каталога и моделью рекомендаций.
* Импортировать данные каталога. Каталог содержит метаданные элементов.
* Импортировать данные об использовании. Данные об использовании можно загрузить двумя способами:
	* путем загрузки файла, содержащего данные об использовании;
	* путем отправки событий сбора данных. Обычно загружают файл с данными об использовании, чтобы создать исходную модель рекомендаций (программу начальной загрузки) и использовать ее, пока система не соберет достаточно данных с помощью формата сбора данных.
* Построить модель рекомендаций. Это асинхронная операция, при которой система рекомендаций принимает все данные об использовании и создает модель рекомендаций. Эта операция может занять несколько минут или часов в зависимости от размера данных и параметров конфигурации построения. При запуске сборки вы получите ее идентификатор. Используйте его для проверки завершения процесса сборки, прежде чем начать использовать рекомендации.
* Получить рекомендации. Получите рекомендации для определенного элемента или списка элементов.

Все описанные выше действия выполняются с помощью API рекомендаций машинного обучения Azure. Кроме того, пример приложения, который реализует каждое из этих действий, можно скачать из [коллекции](http://1drv.ms/1xeO2F3).

##Ограничения

* Максимальное число моделей на одну подписку — 10.
* Максимальное количество элементов в каталоге равно 100 000.
* Максимальное количество поддерживаемых точек использования — около 5 000 000. По мере поступления новых точек будут удаляться самые старые.
* Максимальный размер данных, которые можно отправить в POST (например, импорт данных каталога, импорт данных по использованию), составляет 200 МБ.
* Число транзакций в секунду для неактивной сборки модели рекомендаций — около 2. Для активной сборки модели рекомендаций это значение может достигать 20 транзакций в секунду.

##Интеграция

###Аутентификация
Micosoft Azure Marketplace поддерживает методы проверки подлинности Basic и OAuth. Ключи учетной записи можно легко найти, перейдя к ключам на сайте Marketplace в разделе [параметров учетной записи](https://datamarket.azure.com/account/keys).
####Обычная проверка подлинности
Добавьте заголовок авторизации.

	Authorization: Basic <creds>
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
	
Преобразование в Base64 (C#)

	var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
	var creds = Convert.ToBase64String(bytes);
	
Преобразование в Base64 (JavaScript)

	var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
	



###URI службы
Корневой код URI служб для интерфейсов API рекомендаций по Машинному обучению Azure находится [здесь](https://api.datamarket.azure.com/amla/recommendations/v2/).

Полный URI-код службы выражается с помощью элементов спецификации OData.

###Версия API
У каждого вызова API будет в конце параметр запроса, называемый apiVersion, который должен иметь значение 1.0.

###Идентификаторы с учетом регистра
Идентификаторы, возвращаемые любым интерфейсом API, учитывают регистр и должны использоваться в исходном виде при передаче в качестве параметров в последующих вызовах API. Например, регистр учитывается в идентификаторах моделей и каталогов.

###Создание модели
Создание запроса create model.

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

- `feed/entry/content/properties/id` — содержит идентификатор модели. **Примечание**. В идентификаторе модели учитывается регистр.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

В случае передачи нескольких файлов каталога в одну модель несколькими вызовами мы вставим только новые элементы каталога. Существующие элементы останутся с исходными значениями.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели (с учетом регистра) |
| filename | Текстовый идентификатор каталога.<br>Допустимы только буквы (A–Z, a–z), цифры (0–9), дефисы (-) и символы подчеркивания (\_).<br>Максимальная длина: 50 |
|	версия\_API | 1\.0 |
|||
| Текст запроса | Данные каталога. Формат:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Имя</th><th>Обязательный</th><th>Тип</th><th>Описание</th></tr><tr><td>Идентификатор элемента</td><td>Да</td><td>Буквенно-цифровой, максимальная длина: 50</td><td>Уникальный идентификатор элемента</td></tr><tr><td>Имя элемента</td><td>Да</td><td>Буквенно-цифровой, максимальная длина: 255</td><td>Имя элемента</td></tr><tr><td>Категория элемента</td><td>Да</td><td>Буквенно-цифровой, максимальная длина: 255</td><td>Категория, к которой принадлежит элемент (например, поваренные книги, драматические произведения и т. п.)</td></tr><tr><td>Описание</td><td>Нет</td><td>Буквенно-цифровой, максимальная длина: 4000</td><td>Описание элемента</td></tr></table><br>Максимальный размер файла: 200 МБ.<br><br>Пример:<br><pre>2406e770-769c-4189-89de-1c9283f93a96,Клара Каллан(Clara Callan),книга<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,Комната забвения: проза (Byzantium Book),книга<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Черная работа,книга<br>552a1940-21e4-4399-82bb-594b46d7ed54,Укрощение чудовищ,книга</pre> |


**Ответ**:

Код состояния HTTP: 200

- `Feed\entry\content\properties\LineCount` — количество принятых строк.
- `Feed\entry\content\properties\ErrorCount` — количество строк, которые не были вставлены из-за ошибки.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###Импорт данных об использовании

####Отправка файла
В этом разделе показано, как передать данные по использованию с помощью файла. Можно вызывать этот API несколько раз с данными об использовании. Для всех вызовов сохранятся все данные об использовании.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели (с учетом регистра) |
| filename | Текстовый идентификатор каталога.<br>Допустимы только буквы (A–Z, a–z), цифры (0–9), дефисы (-) и символы подчеркивания (\_).<br>Максимальная длина: 50 |
|	версия\_API | 1\.0 |
|||
| Текст запроса | Данные об использовании. Формат:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Имя</th><th>Обязательно</th><th>Тип</th><th>Описание</th></tr><tr><td>ИД пользователя</td><td>Да</td><td>Буквенно-цифровой</td><td>Уникальный идентификатор пользователя</td></tr><tr><td>ИД элемента</td><td>Да</td><td>Буквенно-цифровой, макс. длина 50</td><td>Уникальный идентификатор элемента</td></tr><tr><td>Время</td><td>Нет</td><td>Дата в формате: ГГГГ/ММ/ДДTЧЧ:ММ:СС (например, 2013/06/20T10:00:00)</td><td>Время даты</td></tr><tr><td>Событие</td><td>Нет, если данные переданы, необходимо указать дату</td><td>Один из следующих элементов:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>Максимальный размер файла 200 МБ.<br><br>Пример:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Ответ**:

Код состояния HTTP: 200

- `Feed\entry\content\properties\LineCount` — количество принятых строк.
- `Feed\entry\content\properties\ErrorCount` — количество строк, которые не были вставлены из-за ошибки.
- `Feed\entry\content\properties\FileId` — идентификатор файла.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
В этом разделе показано, как отправлять события в режиме реального времени в службу рекомендаций Машинного обучения Azure с веб-сайта.

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	версия\_API | 1\.0 |
|||
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
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
				<Count>3</Count>
			</PurchaseItems>
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

###Построение модели рекомендаций

| Метод HTTP | URI |
|:--------|:--------|
|ПУБЛИКАЦИЯ |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId |	Уникальный идентификатор модели (с учетом регистра) |
| userDescription | Текстовый идентификатор каталога. Обратите внимание: при использовании пробелов их необходимо заменить сочетанием символов %20 См. пример выше.<br>Максимальная длина: 50 |
| версия\_API | 1\.0 |
|||
| Текст запроса | НЕТ |

**Ответ**:

Код состояния HTTP: 200

Это асинхронный интерфейс API. В качестве ответа вы получите идентификатор сборки. Чтобы узнать, завершилась ли сборка, вызовите API «Get Builds Status of a Model» и найдите этот идентификатор сборки в ответе. Имейте в виду, что сборка может длиться от нескольких минут до нескольких часов в зависимости от размера данных.

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

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###Получение состояния сборки модели

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
|	modelId |	Уникальный идентификатор модели (с учетом регистра) |
|	onlyLastBuild |	Указывает, следует вернуть весь журнал сборок модели или только состояние последней сборки. |
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
- Rank — сборка рейтинга; (сведения о сборках рейтинга см. в документе "Документация по интерфейсу API рекомендаций для машинного обучения".)
- Recommendation — сборка рекомендаций.
- Fbt — сборка часто покупаемых вместе элементов.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###Получение рекомендаций

| Метод HTTP | URI |
|:--------|:--------|
|ПОЛУЧЕНИЕ |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| modelId | Уникальный идентификатор модели (с учетом регистра) |
| itemIds | Разделенный запятыми список элементов, для которых предоставляются рекомендации.<br>Макс. длина: 1024 |
| numberOfResults | Требуемое число результатов. |
| includeMetatadata | Для использования в будущем, всегда имеет значение false |
| версия\_API | 1\.0 |

**Ответ.**

Код состояния HTTP: 200

Ответ включает одну запись для каждого рекомендованного элемента. Каждая запись содержит следующие данные:

- `Feed\entry\content\properties\Id` — идентификатор рекомендованного элемента.
- `Feed\entry\content\properties\Name` — имя элемента.
- `Feed\entry\content\properties\Rating` — оценка рекомендации. Чем выше оценка, тем выше уровень доверия.
- `Feed\entry\content\properties\Reasoning` — обоснование рекомендации (например, объяснения).

OData XML

Приведенный ниже пример ответа содержит 10 рекомендованных элементов.

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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
Вы можете обновить описание модели или активный идентификатор сборки.
*Активный идентификатор сборки* — каждая сборка каждой модели имеет собственный идентификатор. Активный идентификатор сборки — это первая успешная сборка каждой новой модели. Как только у вас будет активный идентификатор сборки и вы выполните дополнительные сборки для той же модели, следует явно задать его в качестве идентификатора сборки по умолчанию, если необходимо. Если при получении рекомендаций не указать, какой идентификатор сборки нужно использовать, автоматически будет использоваться идентификатор по умолчанию.

Этот механизм позволяет при наличии действующей модели рекомендаций в рабочей среде выполнять сборку новых моделей и тестировать их перед выпуском.

| Метод HTTP | URI |
|:--------|:--------|
|ОТПРАВКА |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Пример:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|	Имя параметра |	Допустимые значения |
|:--------			|:--------								|
| id | Уникальный идентификатор модели (с учетом регистра) |
| версия\_API | 1\.0 |
|||
| Текст запроса | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Обратите внимание, что теги XML Description и ActiveBuildId необязательны. Если вы не хотите задавать Description или ActiveBuildId, удалите весь тег. |

**Ответ**:

Код состояния HTTP: 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
	</feed>

##Юридическая информация
Данный документ предоставляется «как есть». Сведения и мнения, представленные в данном документе, включая URL-адреса и ссылки на другие веб-сайты, могут быть изменены без предварительного уведомления.
Некоторые примеры, содержащиеся в данном документе, являются вымышленными и приводятся исключительно в демонстрационных целях. Любое сходство с реальными ситуациями случайно.
Настоящий документ не предоставляет юридических прав на интеллектуальную собственность в отношении продуктов корпорации Майкрософт. Вы можете скопировать и использовать данный документ для внутренних справочных целей. 
© Корпорация Майкрософт, 2014. Все права защищены.
 

<!---HONumber=AcomDC_0914_2016-->