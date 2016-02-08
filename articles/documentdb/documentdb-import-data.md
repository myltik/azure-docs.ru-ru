<properties 
	pageTitle="Средства миграции базы данных — преобразование CSV в JSON | Microsoft Azure" 
	description="Узнайте, как использовать средства миграции данных DocumentDB с открытым исходным кодом для импорта данных в DocumentDB. MongoDB, SQL, хранилище таблиц, DynamoDB и преобразование CSV в JSON." 
	keywords="csv в json, средства миграции базы данных, преобразование csv в json"
	services="documentdb" 
	authors="andrewhoh" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015" 
	ms.author="anhoh"/>

# Импорт данных в DocumentDB — средство миграции базы данных

В этой статье показано, как использовать средство переноса данных DocumentDB для импорта данных в [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) из различных источников, включая JSON-файлы, CSV-файлы, SQL, MongoDB, табличное хранилище Azure, Amazon DynamoDB и коллекции DocumentDB.

Ознакомившись с данной статьей, вы сможете ответить на следующие вопросы.

-	Как импортировать данные файла JSON в DocumentDB?
-	Как импортировать данные CSV-файла в DocumentDB?
-	Как импортировать данные SQL Server в DocumentDB?
-	Как импортировать данные MongoDB в DocumentDB?
-	Как импортировать данные из табличного хранилища Azure в DocumentDB?
-	Как импортировать данные из Amazon DynamoDB в DocumentDB?
-	Как импортировать данные из HBase в DocumentDB?
-	Как переносить данные между коллекциями DocumentDB?

##<a id="Prerequisites"></a>Предварительные требования ##

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

- платформа [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) или более поздняя версия.

##<a id="Overviewl"></a>Обзор средства миграции данных DocumentDB ##

Средство миграции данных DocumentDB — это решение с открытым исходным кодом, которое импортирует данные в DocumentDB из различных источников, таких как:

- файлы JSON;
- MongoDB
- SQL Server
- СЫМ-файлы;
- табличное хранилище Azure;
- Amazon DynamoDB
- HBase
- коллекции DocumentDB.

Хотя средство импорта предоставляет графический интерфейс пользователя (dtui.exe), им также можно управлять из командной строки (dt.exe). К слову, существует параметр для вывода соответствующей команды после настройки импорта в пользовательском интерфейсе. Табличный источник данных (например, SQL Server или CSV-файлы) можно преобразовать так, чтобы создать иерархические связи (вложенные документы) во время импорта. Читайте дальше, чтобы узнать о доступных источниках, примерах команд для импорта из каждого источника, возможных целевых объектах и просмотре результатов импорта.


##<a id="Install"></a>Установка средства миграции данных DocumentDB ##

Исходный код средства миграции доступен на портале GitHub в [этом репозитории](https://github.com/azure/azure-documentdb-datamigrationtool), а скомпилированная версия доступна в [Центре загрузки Майкрософт](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Вы можете скомпилировать решение или просто скачать и извлечь скомпилированную версию в каталог по своему усмотрению. Затем запустите:

- **Dtui.exe** — версия средства с графическим интерфейсом;
- **Dt.exe** — версия средства с командной строкой.

##<a id="JSON"></a>Импорт файлов JSON ##

Параметр импорта файлов JSON позволяет импортировать файлы JSON с одним или несколькими документами или файлы JSON, каждый из которых содержит массив документов JSON. Во время добавления папок, содержащих JSON-файлы для импорта, вы можете выполнить рекурсивный поиск файлов во вложенных папках.

![Снимок экрана: параметры исходного файла JSON — средства миграции базы данных](./media/documentdb-import-data/jsonsource.png)

Ниже приведены некоторые примеры команд для импорта файлов JSON.

	#Import a single JSON file	
	dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory of JSON files
	dt.exe /s:JsonFile /s.Files:C:\TESessions*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory (including sub-directories) of JSON files
	dt.exe /s:JsonFile /s.Files:C:\LastFMMusic***.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionTier:S3

	#Import a directory (single), directory (recursive), and individual JSON files
	dt.exe /s:JsonFile /s.Files:C:\Tweets*.*;C:\LargeDocs***.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionTier:S3

	#Import a single JSON file and partition the data across 4 collections
	dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionTier:S3

##<a id="MongoDB"></a>Импорт из MongoDB ##

Параметр импорта из MongoDB позволяет импортировать данные из отдельной коллекции MongoDB, фильтровать документы с помощью запроса, а также изменять структуру документа с помощью проекции.

![Снимок экрана: параметры источника MongoDB — documentdb и mongodb](./media/documentdb-import-data/mongodbsource.png)

Строка подключения представляется в стандартном формате MongoDB:

	mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру MongoDB, указанному в строке подключения.

Введите имя коллекции, из которой будут импортированы данные. При необходимости можно указать или предоставить файл для запроса (например {pop: {$gt:5000}}) и проекцию (например {loc:0}) для фильтрации и обработки и импортируемых данных.

Ниже приведены некоторые примеры команд для импорта данных из MongoDB.

	#Import all documents from a MongoDB collection
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionTier:S3

	#Import documents from a MongoDB collection which match the query and exclude the loc field
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionTier:S3

##<a id="MongoDBExport"></a>Импортировать файлы экспорта MongoDB ##

Параметр импорта JSON-файлов экспорта MongoDB позволяет импортировать файлы JSON, созданные с помощью служебной программы mongoexport.

![Снимок экрана: параметры источника экспорта MongoDB — documentdb и mongodb](./media/documentdb-import-data/mongodbexportsource.png)

При добавлении папок, содержащих JSON-файлы экспорта MongoDB, вы можете выполнить рекурсивный поиск файлов во вложенных папках.

Ниже приведен пример команды для импорта данных из JSON-файлов экспорта MongoDB.

	dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionTier:S3

##<a id="SQL"></a>Импорт из SQL Server ##

Параметр импорта из источника SQL позволяет импортировать данные из отдельной базы данных SQL Server и фильтровать записи для импорта с помощью запроса. Кроме того, можно изменить структуру документа, указав разделитель вложения (подробнее об этом чуть позже).

![Снимок экрана: параметры источника SQL — средства миграции базы данных](./media/documentdb-import-data/sqlexportsource.png)

Формат строки подключения — это стандартный формат строки подключения SQL.

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру SQL Server, указанному в строке подключения.

Свойство разделителя вложения используется для создания иерархических связей (вложенных документов) во время импорта. Рассмотрим следующий запрос SQL:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Он возвращает следующие результаты (показаны частичные результаты):

![Снимок экрана: результаты запроса SQL](./media/documentdb-import-data/sqlqueryresults.png)

Обратите внимание на псевдонимы, например Address.AddressType и Address.Location.StateProvinceName. Если указать разделитель вложения ".", средство импорта создает вложенные документы Address и Address.Location во время импорта. Ниже приведен пример полученного документа в DocumentDB:

*{
  "id": "956",
  "Name": "Finer Sales and Service",
  "Address": {
    "AddressType": "Main Office",
    "AddressLine1": "#500-75 O'Connor Street",
    "Location": {
      "City": "Ottawa",
      "StateProvinceName": "Ontario"
    },
    "PostalCode": "K4B 1S2",
    "CountryRegionName": "Canada"
  }
}*
 
Ниже приведены некоторые примеры команд для импорта данных из SQL Server:

	#Import records from SQL which match a query	
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionTier:S3

	#Import records from sql which match a query and create hierarchical relationships
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionTier:S3

##<a id="CSV"></a>Импорт CSV-файлов — преобразование CSV в JSON ##

Параметр импорта из CSV-файла позволяет импортировать один или несколько CSV-файлов. Во время добавления папок, содержащих CSV-файлы для импорта, вы можете выполнить рекурсивный поиск файлов во вложенных папках.

![Снимок экрана: параметры источника CSV — преобразование CSV в JSON](media/documentdb-import-data/csvsource.png)

Как и для источника SQL, свойство разделителя вложения можно использовать для создания иерархических связей (вложенных документов) во время импорта. Рассмотрим следующую строку заголовков и строки данных CSV:

![Снимок экрана: примеры записей CSV — преобразование CSV в JSON](./media/documentdb-import-data/csvsample.png)

Обратите внимание на псевдонимы, например DomainInfo.Domain_Name и RedirectInfo.Redirecting. Если указать разделитель вложения ".", средство импорта создает вложенные документы DomainInfo и RedirectInfo во время импорта. Ниже приведен пример полученного документа в DocumentDB:

*{
  "DomainInfo": {
    "Domain_Name": "ACUS.GOV",
    "Domain_Name_Address": "http://www.ACUS.GOV"
  },
  "Federal Agency": "Administrative Conference of the United States",
  "RedirectInfo": {
    "Redirecting": "0",
    "Redirect_Destination": ""
  },
  "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"
}*

Средство импорта попытается определить сведения о типе для значений, которые не заключены в кавычки и находятся в CSV-файлах (значения, заключенные в кавычки, считаются строками). Типы определяются в таком порядке: номер, дата и время, логическое значение.

Следует отметить еще кое-что об импорте CSV-файлов.

1.	По умолчанию значения, не заключенные в кавычки, очищаются от табуляции и пробелов, а значения, заключенные в кавычки, остаются в нетронутом виде. Вы можете переопределить это поведение, установив флажок «Обрезать значения, заключенные в кавычки» или воспользовавшись параметром командной строки /s.TrimQuoted.

2.	По умолчанию объект null, не заключенный в кавычки, считается значением null. Вы можете переопределить это поведение (то есть сделать так, чтобы объект null, не заключенный в кавычки, считался строкой null). Для этого установите флажок «Считать объект NULL, не заключенный в кавычки, строкой» или настройте параметр командной строки /s.NoUnquotedNulls.


Далее показан пример команды для импорта CSV:

	dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionTier:S3

##<a id="AzureTableSource"></a>Импорт из табличного хранилища Azure ##

Параметр импорта из табличного хранилища Azure позволяет импортировать данные из отдельной таблицы хранилища Azure и фильтровать сущности таблицы для импорта при необходимости.

![Снимок экрана: параметры источника табличного хранилища Azure](./media/documentdb-import-data/azuretablesource.png)

Для строки подключения табличного хранилища Azure используется следующий формат:

	DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру табличного хранилища Azure, указанному в строке подключения.

Введите имя таблицы Azure, из которой будут импортированы данные. При необходимости можно указать [фильтра](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Параметр импорта из табличного хранилища Azure предоставляет следующие дополнительные параметры:

1. Включить внутренние поля 
	2. All — включить все внутренние поля (PartitionKey, RowKey и Timestamp)
	3. None — исключить все внутренние поля
	4. RowKey — включить только поле RowKey
3. Выбор столбцов
	1. Фильтры табличного хранилища Azure не поддерживают проекции. Если вы хотите импортировать только определенные свойства сущности таблицы Azure, их необходимо добавить в список "Выбор столбцов". Все другие свойства сущностей будут игнорироваться.

Ниже приведен пример команды для импорта данных из табличного хранилища Azure.

	dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionTier:S3

##<a id="DynamoDBSource"></a>Импорт из Amazon DynamoDB ##

Параметр импортера источника Amazon DynamoDB позволяет выполнять импорт из отдельной таблицы Amazon DynamoDB и при необходимости фильтровать сущности для импорта. Чтобы максимально упростить настройку импорта, представлено несколько шаблонов.

![Снимок экрана: параметры источника DynamoDB Amazon — средства миграции базы данных](./media/documentdb-import-data/dynamodbsource1.png)

![Снимок экрана: параметры источника DynamoDB Amazon — средства миграции базы данных](./media/documentdb-import-data/dynamodbsource2.png)

Формат строки подключения Amazon DynamoDB выглядит следующим образом:

	ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру Amazon DynamoDB, указанному в строке подключения.

Ниже приведен пример команды для импорта данных из Amazon DynamoDB:

	dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:catalogCollection /t.CollectionTier:S3

##<a id="BlobImport"></a>Импорт файлов из хранилища больших двоичных объектов Azure##

JSON-файл, файл экспорта MongoDB и параметры импорта источника файла CSV позволяют импортировать из хранилища больших двоичных объектов Azure один или несколько файлов. Чтобы выбрать файлы для импорта, просто предоставьте регулярное выражение после указания URL-адреса или ключа учетной записи для контейнера больших двоичных объектов.

![Снимок экрана: параметры исходного файла больших двоичных объектов](./media/documentdb-import-data/blobsource.png)

Ниже приведен пример команды для импорта JSON-файлов из хранилища больших двоичных объектов Azure:

	dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:doctest

##<a id="DocumentDBSource"></a>Импорт из DocumentDB ##

Параметр импортера источников DocumentDB позволяет импортировать данные из коллекций DocumentDB и, если нужно, фильтровать документы с помощью запроса.

![Снимок экрана: параметры источника DocumentDB](./media/documentdb-import-data/documentdbsource.png)

Для строки подключения DocumentDB используется следующий формат:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру DocumentDB, указанному в строке подключения.

Чтобы импортировать данные из одной коллекции DocumentDB, введите ее имя. Чтобы выполнить импорт из нескольких коллекций DocumentDB, введите регулярное выражение, соответствующее имени одной коллекции либо именам нескольких (например, collection01 | collection02 | collection03). При необходимости можно указать или предоставить файл для запроса для фильтрации и обработки и импортируемых данных.

> [AZURE.NOTE] Так как в поле коллекции можно вводить регулярные выражения, то, если вы импортируете данные из одной коллекции, имя которой содержит символы регулярного выражения, их нужно экранировать соответствующим образом.

Параметр импорта DocumentDB предоставляет следующие дополнительные параметры:

1. «Включить внутренние поля»: указывает, следует ли включать системные свойства документа DocumentDB в экспорт (например, \_rid, \_ts).
2. "Число повторных попыток в случае сбоя": указывает количество повторных попыток подключения к DocumentDB при временном сбое (например, прерывания сетевого подключения).
3. "Интервал повтора": указывает время ожидания между повторными попытками подключения к DocumentDB при временном сбое (например, прерывания сетевого подключения).
4. "Режим подключения": указывает режим подключения для DocumentDB. Доступны варианты: DirectTcp, DirectHttps и Gateway. Режимы прямого подключения быстрее, а режим шлюза более удобен для брандмауэра, так как использует только порт 443.

![Снимок экрана: дополнительные параметры источника DocumentDB](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP] Средство импорта по умолчанию использует режим подключения DirectTcp. При возникновении проблем с брандмауэром перейдите на режим шлюза, так как он использует только порт 443.


Ниже приведены некоторые примеры команд для импорта данных из DocumentDB:

	#Migrate data from one DocumentDB collection to another DocumentDB collections
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionTier:S3

	#Migrate data from multiple DocumentDB collections to a single DocumentDB collection
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionTier:S3

	#Export a DocumentDB collection to a JSON file
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionTier:S3

##<a id="HBaseSource"></a>Импорт из HBase ##

Параметр импортера источника HBase позволяет импортировать данные из таблицы HBase и фильтровать данные при необходимости. Чтобы максимально упростить настройку импорта, представлено несколько шаблонов.

![Снимок экрана: параметры источника HBase](./media/documentdb-import-data/hbasesource1.png)

![Снимок экрана: параметры источника HBase](./media/documentdb-import-data/hbasesource2.png)

Формат строки подключения HBase Stargate выглядит следующим образом:

	ServiceURL=<server-address>;Username=<username>;Password=<password>

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру HBase, указанному в строке подключения.

Ниже приведен пример команды для импорта данных из HBase:

	dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:hbaseimport

##<a id="DocumentDBBulkTarget"></a>Импорт в DocumentDB (массовый импорт) ##

Средство массового импорта DocumentDB позволяет импортировать данные из любого доступного источника, используя хранимую процедуру DocumentDB для повышения эффективности. Это средство поддерживает импорт в одну коллекцию DocumentDB, а также сегментированный импорт, в рамках которого данные секционируются по нескольким коллекциям DocumentDB. Дополнительные сведения о секционировании данных в DocumentDB см. [здесь](documentdb-partition-data.md). Кроме того, это средство создает, выполняет и удаляет хранимые процедуры из целевых коллекций.

![Снимок экрана: параметры массового импорта DocumentDB](./media/documentdb-import-data/documentdbbulk.png)

Для строки подключения DocumentDB используется следующий формат:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру DocumentDB, указанному в строке подключения.

Чтобы импортировать данные в одну коллекцию, укажите имя этой коллекции и нажмите кнопку «Добавить». Чтобы импортировать данные в несколько коллекций, укажите имя каждой коллекции отдельно или, чтобы указать несколько коллекций, воспользуйтесь таким синтаксисом: *collection_prefix*[start index - end index]. Когда вы указываете несколько коллекций с помощью приведенного выше синтаксиса, учитывайте следующие факторы.

1. Поддерживаются только шаблоны имени диапазона целых чисел. Например, если указать [0–3], будут указаны такие коллекции: collection0, collection1, collection2, collection3.
2. Вы можете использовать сокращенный синтаксис: если ввести collection[3], будет отображен тот же набор коллекций, что и на этапе 1.
3. Вы можете указать несколько подстановок. Например, коллекция [0–1] [0–9] создаст 20 имен коллекций с нулем в начале (collection01… 02… 03).

Указав имена коллекций, выберите нужную ценовую категорию коллекций (S1, S2 или S3). Для наилучшей производительности выберите S3. Дополнительные сведения об уровнях производительности DocumentDB см. [здесь](documentdb-performance-levels.md).

> [AZURE.NOTE] Параметр уровня производительности применяется только для создания коллекции. Если указанная коллекция уже существует, ее ценовая категория не будет изменена.

Если нужно импортировать данные в несколько коллекций, средство импорта поддерживает сегментирование на основе хэша. Укажите в этом сценарии свойство документа, которое нужно использовать в качестве ключа секции (если не указать ключ секции, сегментирование документов по целевым коллекциям будет происходить произвольным образом).

При необходимости можно указать, какое поле источника импорта следует использовать в качестве свойства идентификатора документа DocumentDB во время импорта (обратите внимание, что если документы не содержат это свойство, средство импорта создаст GUID для свойства идентификатора).

Во время импорта доступны ряд дополнительных параметров. Во-первых, хотя средство и предоставляет хранимую процедуру массового импорта по умолчанию (BulkInsert.js), вы можете указать собственную хранимую процедуру:

 ![Снимок экрана: параметр хранимой процедуры массового импорта DocumentDB](./media/documentdb-import-data/bulkinsertsp.png)

Кроме того, при импорте типов даты (например, из SQL Server или MongoDB) можно выбрать три параметра импорта:

 ![Снимок экрана: параметры импорта даты и времени импорта DocumentDB](./media/documentdb-import-data/datetimeoptions.png)

-	Строка: сохраняется как строковое значение.
-	Эпоха: сохраняется как век числовое значение эпохи.
-	Оба: сохраняются строковое значение и числовое значение эпохи. Этот параметр создает вложенный документ, например "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 } 


Средство массового импорта DocumentDB предоставляет следующие дополнительные параметры:

1. "Размер пакета": по умолчанию средство использует размер пакета 50. При импорте больших документов рекомендуется уменьшить размер пакета. И наоборот, при импорте небольших документов рекомендуется увеличить размер пакета.
2. "Максимальный размер скрипта (в байтах)": по умолчанию средство использует максимальный размер скрипта, равный 960 КБ
3. "Отключить автоматическое создание идентификатора": если каждый импортируемый документ содержит поле идентификатора, то выбор этого параметра может повысить производительность. Документы без поля уникального идентификатора не будут импортированы.
4. "Число повторных попыток в случае сбоя": указывает количество повторных попыток подключения к DocumentDB при временном сбое (например, прерывания сетевого подключения).
5. "Интервал повтора": указывает время ожидания между повторными попытками подключения к DocumentDB при временном сбое (например, прерывания сетевого подключения).
6. "Режим подключения": указывает режим подключения для DocumentDB. Доступны варианты: DirectTcp, DirectHttps и Gateway. Режимы прямого подключения быстрее, а режим шлюза более удобен для брандмауэра, так как использует только порт 443.

![Снимок экрана: дополнительные параметр массового импорта DocumentDB](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP] Средство импорта по умолчанию использует режим подключения DirectTcp. При возникновении проблем с брандмауэром перейдите на режим шлюза, так как он использует только порт 443.

##<a id="DocumentDBSeqTarget"></a>Импорт в DocumentDB (последовательный импорт записей) ##

Средство последовательного импорта записей DocumentDB позволяет импортировать данные из любых доступных источников по одной записи. Этот параметр можно выбрать при импорте в существующую коллекцию, для которой достигнута квота хранимых процедур. Это средство поддерживает импорт в одну коллекцию DocumentDB, а также сегментированный импорт, в рамках которого данные секционируются по нескольким коллекциям DocumentDB. Дополнительные сведения о секционировании данных в DocumentDB см. [здесь](documentdb-partition-data.md).

![Снимок экрана: параметры последовательного импорта записей DocumentDB](./media/documentdb-import-data/documentdbsequential.png)

Для строки подключения DocumentDB используется следующий формат:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Используйте команду Verify, чтобы проверить доступ к экземпляру DocumentDB, указанному в строке подключения.

Чтобы импортировать данные в одну коллекцию, укажите имя этой коллекции и нажмите кнопку «Добавить». Чтобы импортировать данные в несколько коллекций, укажите имя каждой коллекции отдельно или, чтобы указать несколько коллекций, воспользуйтесь таким синтаксисом: *collection_prefix*[start index - end index]. Когда вы указываете несколько коллекций с помощью приведенного выше синтаксиса, учитывайте следующие факторы.

1. Поддерживаются только шаблоны имени диапазона целых чисел. Например, если указать [0–3], будут указаны такие коллекции: collection0, collection1, collection2, collection3.
2. Вы можете использовать сокращенный синтаксис: если ввести collection[3], будет отображен тот же набор коллекций, что и на этапе 1.
3. Вы можете указать несколько подстановок. Например, коллекция [0–1] [0–9] создаст 20 имен коллекций с нулем в начале (collection01… 02… 03).

Указав имена коллекций, выберите нужную ценовую категорию коллекций (S1, S2 или S3). Для наилучшей производительности выберите S3. Дополнительные сведения об уровнях производительности DocumentDB см. [здесь](documentdb-performance-levels.md).

> [AZURE.NOTE] Параметр уровня производительности применяется только для создания коллекции. Если указанная коллекция уже существует, ее ценовая категория не будет изменена.

Если нужно импортировать данные в несколько коллекций, средство импорта поддерживает сегментирование на основе хэша. Укажите в этом сценарии свойство документа, которое нужно использовать в качестве ключа секции (если не указать ключ секции, сегментирование документов по целевым коллекциям будет происходить произвольным образом).

При необходимости можно указать, какое поле источника импорта следует использовать в качестве свойства идентификатора документа DocumentDB во время импорта (обратите внимание, что если документы не содержат это свойство, средство импорта создаст GUID для свойства идентификатора).

Во время импорта доступны ряд дополнительных параметров. При импорте типов даты (например, из SQL Server или MongoDB) можно выбрать три параметра импорта:

 ![Снимок экрана: параметры импорта даты и времени импорта DocumentDB](./media/documentdb-import-data/datetimeoptions.png)

-	Строка: сохраняется как строковое значение.
-	Эпоха: сохраняется как век числовое значение эпохи.
-	Оба: сохраняются строковое значение и числовое значение эпохи. Этот параметр создает вложенный документ, например "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 } 

Средство последовательного импорта записей DocumentDB предоставляет следующие дополнительные параметры:

1. "Количество параллельных запросов": по умолчанию средство использует 2 параллельных запроса. При импорте небольших документов рекомендуется увеличить число параллельных запросов. Обратите внимание, что если задать слишком большое число, производительность импорта может снизиться.
2. "Отключить автоматическое создание идентификатора": если каждый импортируемый документ содержит поле идентификатора, то выбор этого параметра может повысить производительность. Документы без поля уникального идентификатора не будут импортированы.
3. "Число повторных попыток в случае сбоя": указывает количество повторных попыток подключения к DocumentDB при временном сбое (например, прерывания сетевого подключения).
4. "Интервал повтора": указывает время ожидания между повторными попытками подключения к DocumentDB при временном сбое (например, прерывания сетевого подключения).
5. "Режим подключения": указывает режим подключения для DocumentDB. Доступны варианты: DirectTcp, DirectHttps и Gateway. Режимы прямого подключения быстрее, а режим шлюза более удобен для брандмауэра, так как использует только порт 443.

![Снимок экрана: дополнительные параметры последовательного импорта записей DocumentDB](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP] Средство импорта по умолчанию использует режим подключения DirectTcp. При возникновении проблем с брандмауэром перейдите на режим шлюза, так как он использует только порт 443.

##<a id="IndexingPolicy"></a>Указание политики индексирования при создании коллекций DocumentDB ##

Если вы разрешили средству миграции создавать коллекции во время импорта, можно указать политику индексирования коллекций. В разделе дополнительных параметров массового импорта DocumentDB и параметров последовательной записи DocumentDB перейдите в раздел "Политика индексации".

![Снимок экрана: дополнительные параметры политики индексации DocumentDB](./media/documentdb-import-data/indexingpolicy1.png)

С помощью дополнительного параметра политики индексации можно выбрать файл политики индексации, вручную ввести политику индексации или выбрать из набора шаблонов по умолчанию (щелкнув правой кнопкой в текстовом поле политики индексации).

Шаблоны политик, предоставляемые средством:

- По умолчанию. Эта политика лучше всего подходит при выполнении запросов равенства строк и использовании предложения ORDER BY, диапазона и запросов равенства для чисел. Эта политика имеет более низкий индекс служебных данных хранилища, чем "Диапазон".
- Хэш. Эта политика лучше всего подходит при выполнении запросов равенства для чисел и строк. Эта политика имеет низкий индекс служебных данных хранилища.
- Диапазон. Эта политика лучше всего подходит при использовании предложения ORDER BY, диапазона и запросов равенства на числах и строках. Эта политика имеет более высокий индекс служебных данных хранилища, чем "По умолчанию" или "Хэш".


![Снимок экрана: дополнительные параметры политики индексации DocumentDB](./media/documentdb-import-data/indexingpolicy2.png)

> [AZURE.NOTE] Если не указать политику индексации, будет применена политика по умолчанию. Дополнительные сведения о политике индексации DocumentDB можно узнать [здесь](documentdb-indexing-policies.md).


## Экспорт в файл JSON

Средство экспорта JSON DocumentDB позволяет экспортировать любые доступные источники в JSON-файл, содержащий массив документов JSON. Средство автоматически выполняет экспорт или же вы можете просмотреть результирующую команду миграции и выполнить ее самостоятельно. Результирующий JSON-файл может храниться локально или в хранилище больших двоичных объектов Azure.

![Снимок экрана: параметры экспорта в локальный файл DocumentDB JSON](./media/documentdb-import-data/jsontarget.png)

![Снимок экрана: параметр экспорта в хранилище больших двоичных объектов Azure DocumentDB JSON](./media/documentdb-import-data/jsontarget2.png)

При необходимости можно настроить результирующий JSON, что приведет к увеличению размера полученного документа, но при этом содержимое станет более удобным для чтения.

	Standard JSON export
	[{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
	
	Prettified JSON export
	[
 	{
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
	}]
	
## Расширенная конфигурация

На экране расширенной конфигурации укажите расположение файла журнала, в который нужно записывать сообщения об ошибках. К этой странице применяются такие правила.

1.	Если имя файла не указано, все сообщения об ошибках возвращаются на страницу результатов.
2.	Если указано имя файла, но не указан каталог, то файл будет создан (или перезаписан) в текущем каталоге среды.
3.	Если выбрать существующий файл, то файл будет перезаписан. Добавить его нельзя.

	![Снимок экрана расширенной конфигурации](./media/documentdb-import-data/AdvancedConfiguration.png)

## Подтверждение параметров импорта и просмотр командной строки

1. Указав сведения об источнике и конечном объекте и настроив расширенную конфигурацию, просмотрите сводку миграции и, если нужно, просмотрите или скопируйте получившуюся команду миграции (копирование команды полезно для автоматизации операций импорта).

	![Снимок экрана: окно сводки](./media/documentdb-import-data/summary.png)

	![Снимок экрана: окно сводки](./media/documentdb-import-data/summarycommand.png)

2. После проверки источника и назначения нажмите кнопку **Импорт**. Затраченное время, число передаваемых объектов и сведения об ошибках (если вы не указали имя файла при настройке расширенной конфигурации) обновляются в процессе импорта. После завершения вы можете экспортировать результаты (например, для обработки ошибок импорта).

	![Снимок экрана: параметры экспорта JSON DocumentDB](./media/documentdb-import-data/viewresults.png)

3. Можно также запустить новую операцию импорта, сохранив существующие параметры (например, строку подключения, источник и назначение и т. д.) или сбросить все значения.

	![Снимок экрана: параметры экспорта JSON DocumentDB](./media/documentdb-import-data/newimport.png)

## Дальнейшие действия

- Для получения дополнительных сведений о DocumentDB щелкните [здесь](http://azure.com/docdb).


 

<!---HONumber=AcomDC_0128_2016-->