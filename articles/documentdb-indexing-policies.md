<properties 
	pageTitle="Политики индексации DocumentDB | Azure" 
	description="В этом документе объясняется, как работает индексирование для базы данных DocumentDB и как настроить политику индексирования." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="may be required" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/23/2015" 
	ms.author="mimig"/>


Политики индексации DocumentDB
============================

DocumentDB является базой данных без использования каких-либо схем. Она не подразумевает и не требует указания каких-либо схем для документов JSON. Это позволяет быстро определять и выполнять итерации на моделях прикладных данных. По мере добавления документов в коллекцию DocumentDB автоматически индексирует все свойства документов, чтобы они стали доступны для выполнения вами запросов. Автоматическая индексация также позволяет сохранять разнородные типы документов.

Автоматическая индексация документов обеспечена возможностью записи, оптимизирована, с нее снята блокировка и предусмотрены методы обслуживания структурированных индексов протокола. DocumentDB поддерживает устойчивый объем быстрых операций записи, при этом обслуживая согласованные запросы.

Подсистема индексации DocumentDB рассчитана на поддержку следующего:

·         Эффективные, расширенные иерархические и реляционные запросы без определений схем или индексов.

·         Согласованные результаты запросов при обработке устойчивого объема записи. В случае высоких нагрузок записи данных с согласованными запросами индекс обновляется постепенно, эффективно и оперативно при обработке устойчивого объема записи.

·         Эффективность хранения. для повышения эффективности затрат накладные расходы дискового пространства при индексировании являются ограниченными и предсказуемыми.

·         Несколько владельцев. Обновления индекса выполняются в рамках бюджета системных ресурсов, выделяемых на каждую коллекцию DocumentDB. 

Для большинства приложений вы можете использовать автоматическую политику индексации по умолчанию, так как она обеспечивает максимальную гибкость и оптимальный компромисс между производительностью и эффективностью хранения. С другой стороны, задав пользовательскую политику индексирования, вы сможете дискретно настраивать степень компромисса между производительностью запросов, скоростью записи и дополнительными затратами ресурсов на хранение индекса.  

Например, за счет исключения из индексации некоторых документов или путей в документах вы сможете уменьшить как объем памяти в хранилище, используемой для индексирования, так и временные затраты на вставку данных при обслуживании индекса. Вы можете изменить тип индекса, чтобы он точнее соответствовал запросам по диапазону, или повысить точность индекса в байтах для улучшения производительности запросов. В этой статье описываются различные параметры конфигурации индексирования, доступные в
DocumentDB, а также как настроить политику индексирования для рабочих нагрузок.

<a id="HowWorks"></a>Принцип индексирования в DocumentDB
-----------------------------

Индексирование в DocumentDB основано на том преимуществе, что грамматика JSON дает возможность документам быть **представленными в виде деревьев**. Чтобы представить документ JSON в виде дерева, требуется создать фиктивный корневой узел, который будет являться родителем остальные реальных узлов в подчиненном документе. Каждая метка, включая и индексы массива в документе JSON, становится узлом дерева. На рисунке ниже показан пример документа JSON и его соответствующее представление в виде дерева.

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


Например, свойство JSON {"headquarters": "Belgium"} свойство в вышеприведенном примере соответствует пути /"headquarters"/"Belgium". Массив JSON {"exports": [{"city": "Moscow"}, {"city": Athens"}]} соответствует путям /"exports"/0/"city"/"Moscow" и /"exports"/1/"city"/"Athens".

**Примечание** Представление пути размывает границу между структурой или схемой и значениями экземпляра в документах, поэтому
DocumentDB действительно свободна от схем.

В DocumentDB документы организованы в коллекции, запросы к которым можно выполнять с помощью SQL или обрабатывать в ходе одиночной транзакции.
Каждую коллекцию можно настроить в соответствии с собственной политикой индексации, выраженной с помощью указания путей. В следующем разделе мы рассмотрим, как настроить алгоритм индексирования коллекции DocumentDB.

<a id="ConfigPolicy"></a>Конфигурация политики индексирования коллекции
-------------------------------------------

На следующем примере показано, как задать пользовательскую политику индексирования во время создания коллекции с помощью API REST в DocumentDB. Пример представляет собой политику индексации, выраженную в виде путей, типов индекса и степеней точности.


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "name":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/\"nonIndexedContent\"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**Примечание:** Политика индексирования коллекции должна быть задана во время создания. Изменение политики индексирования после создания коллекции не допускается, но оно будет поддерживаться в последующих версиях DocumentDB.

**Примечание:** По умолчанию DocumentDB индексирует все пути в документах согласно хеш-индексу. Внутренний путь метки времени Timestamp (\_ts) сохраняется с индексом диапазона.

### Автоматическое индексирование

Вы можете выбрать, хотите ли вы, чтобы коллекция автоматически индексировала все документы, или нет. По умолчанию все документы автоматически индексируются, но вы можете отключить эту функцию. При выключенном индексировании документы могут быть доступны только через свои собственные ссылки или запросы, использующие идентификатор.

При выключенном автоматическом индексировании вы все равно можете выборочно добавлять в индекс только некоторые документы. И наоборот, вы можете оставить включенным автоматическое индексирование и выборочно указать, какие документы необходимо исключить.
Настройка включения или выключения индексирования полезна, когда у вас есть только подмножество документов, к которым требуется выполнить запрос.

Вы можете настроить политику по умолчанию, задав значение свойства автоматизации равным true или false. Чтобы изменить его для одного документа, можно задать заголовок запроса x-ms-indexingdirective во время вставки или замены документа.

Так, следующий пример показывает, как добавить документ непосредственно с помощью [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) и свойства [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Name = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### Режимы индексирования

Вы можете выбрать между синхронными (**согласованными**) и асинхронными (**отложенными**) обновлениями индекса. По умолчанию, индекс обновляется синхронно при каждой операции вставки, замены или удаления документа в коллекции. Это позволяет выполнять запросы, обеспечивая тот же уровень согласованности, что и при чтении документа, без каких-либо задержек, чтобы индекс "успевал" обновиться.

Поскольку DocumentDB оптимизирован для операций записи и поддерживает устойчивые объемы записей документов вместе с синхронным обслуживанием индекса, вы можете настроить определенные коллекции для отложенного обновления своего индекса. Отложенное индексирование отлично подходит для сценариев, при которых происходит запись данных пачками, а вы хотите уменьшить нагрузку при выполнении работы, необходимой для индексирования содержания в течение более длительного периода времени. Это позволяет эффективно использовать отведенную пропускную способность и обслуживать запросы на запись в часы пик с минимальной задержкой.  При включенном отложенном индексировании результаты запросов будут в конечном итоге согласованы независимо от уровня согласованности, настроенного в учетной записи базы данных.                                                                                     

В следующем примере показано, как создать коллекцию DocumentDB с помощью
.NET SDK при согласованном автоматическом индексировании при всех вставках документа.


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Name ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### Типы индексов и степени точности

Тип или схема, используемая для записей индекса, оказывает прямое влияние на хранение и производительность индекса. При схеме с использованием более высокой точности запросы, как правило, более быстрые. В то же время, при этом увеличивается дополнительный расход ресурса для хранения индекса. Выбор пониженной точности означает, что потребуется обработать больше документов во время выполнения запроса, но дополнительные расходы на хранение будут меньше.

Степень точности индекса для значений по любому из путей может варьироваться от 3 до 7 байт. 
Поскольку один и тот же путь могут иметь числовые и строковые значения в различных документах, ими можно управлять по отдельности. В .NET SDK эти значения соответствуют свойствам [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) и
[StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx).

Существует два вида поддерживаемых типов индекса - хэш и диапазон. Выбор типа индекса **Хэш** позволяет создавать эффективные запросы равенства. Для большинства случаев использования хэш-индексов не требуется более высокая точность, чем разрядность по умолчанию, т. е. 3 байта.

Выбор типа индекса **Диапазон** позволяет выполнять диапазонные запросы (с использованием >, <, >=, <=, !=). Для путей, которые имеют большие диапазоны значений, рекомендуется использовать повышенную точность с разрядностью в 6 байт. Распространенный вариант использования, при котором требуется более высокая степень точности индекса диапазона, - временные метки, которые хранятся как время эпохи.

Если ваш случай использования не требуется эффективных диапазонных запросов, то по умолчанию хэш-индексы обеспечивают наилучший компромисс между хранением и производительностью. Обратите внимание, что для поддержки диапазонных запросов вам необходимо задать политику настраиваемого индекса.

> [AZURE.NOTE] Диапазонные индексы поддерживаются только для числовых значений.
  
В следующем примере показывается, как повысить точность для диапазонных индексов в коллекции с использованием .NET SDK. Обратите внимание, что при этом используется специальный путь "/" - это объясняется в следующем разделе.


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Name = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### Пути индекса

В документах вы можете выбрать, какие пути необходимо включить или исключить из индексирования. Такой подход может обеспечить повышение производительности при записи и понижение требований к хранилищу индекса при сценариях, когда шаблоны запросов известны заранее.

Пути индекса начинаются с корневой (/) и, как правило, заканчиваются оператором подстановочного знака ?, обозначающего, что существует несколько возможных значений префикса. Например, чтобы обслуживать SELECT * из семейства F WHERE
F.familyName = "Andersen", необходимо включить путь индекса для
/"familyName"/? в политике индекса в коллекции.                                                      

В путях индекса можно также использовать оператор подстановочного знака * для задания алгоритма пути рекурсивно по префиксу. Например,
/"payload"/* можно использовать для исключения из индексирования всего, что находится по свойству payload.

Вот распространенные шаблоны задания путей индекса:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Путь</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Случай описания или использования</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    Путь коллекции по умолчанию. Рекурсивный и применяется ко всему дереву документа.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Путь индекса, необходимый для обслуживания следующих запросов (с типами хэш, диапазон, соответственно):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Путь индекса для всех путей по заданной метке. Указывается только для исключения из индексирования.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    Путь индекса, используемый во время выполнения запроса для удаления документов, которые не имеют указанного пути.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Путь индекса, необходимый для обслуживания запросов (с типами хэш, диапазон, соответственно):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] При задании пути пользовательского индекса необходимо выбрать правило индексирования по умолчанию для всего дерева документов, указав специальный путь "/". 

В следующем примере показана настройка конкретного пути с диапазонным индексированием и пользовательским значением точности 7 байт:


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Name = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/\"CreatedTimestamp\"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


DocumentDB возвращает ошибку, когда в запросе используется оператор диапазона, но в нем нет диапазонного индекса, сопоставленного с запрашиваемым путем и нет никаких других фильтров, которые можно обслуживать из индекса. Но эти запросы все же можно выполнять без диапазонного индекса с помощью заголовка  x-ms-documentdb-allow-scans header в API-интерфейсе REST или параметре
AllowScanInQueryrequest с помощью .NET SDK.                                                                                  

Следующий пример исключает поддерево путей из индексирования с помощью
подстановочного знака "*".

	var excluded = new DocumentCollection { Name = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/\" nonIndexedContent\"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


Настройка производительности
------------------

При оценке различных конфигураций политики индексирования следует оценить последствия политики для хранилища и пропускной способности с помощью API-интерфейсов DocumentDB.

Чтобы оценить квоты хранения и использования коллекции, запустите запрос HEAD или GET для ресурсов коллекции и выберите заголовки запросов x-ms-request-quota и the x-ms-request-usage. В пакете .NET SDK свойства [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) и [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) во вкладке [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) содержат соответствующие значения.


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


Для оценки расходов на индексирование в каждой операции записи (создание, обновление или удаление), выберите заголовок x-ms-request-charge (или его эквивалент [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) в свойствах на вкладке [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) в пакете .NET
SDK), чтобы определить количество единиц запросов на эти операции.


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=47-->
