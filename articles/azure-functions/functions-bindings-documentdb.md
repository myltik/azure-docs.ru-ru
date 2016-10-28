<properties
	pageTitle="Привязки Azure DocumentDB в функциях Azure | Microsoft Azure"
	description="Узнайте, как использовать привязки Azure DocumentDB в функциях Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Привязки Azure DocumentDB в функциях Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки Azure DocumentDB в функциях Azure.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a> Входная привязка для Azure DocumentDB

Входные привязки могут загрузить документ из коллекции DocumentDB и передать его непосредственно в привязку. Идентификатор документа можно определить по триггеру, который вызвал функцию. В функции C# любые изменения, внесенные в запись, будут автоматически отправляться обратно в коллекцию после успешного выхода из функции.

#### Файл function.json для входной привязки DocumentDB

Файл *function.json* содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для документа.
- `type` — для этого свойства необходимо задать значение documentdb.
- `databaseName` — база данных, содержащая документ.
- `collectionName` — коллекция, содержащая документ.
- `id` — идентификатор документа, который нужно получить. Это свойство поддерживает привязки, аналогичные {queueTrigger}, в которых в качестве идентификатора документа будет использоваться значение строки сообщения очереди.
- `connection` — строка параметра приложения, установленная в конечную точку учетной записи DocumentDB. При выборе учетной записи на вкладке "Интеграция" будет создан параметр приложения с именем в формате yourAccount\_DOCUMENTDB. Если параметр приложения нужно создать вручную, фактическую строку подключения необходимо записать в следующем формате: AccountEndpoint=<конечная точка для уч. записи>;AccountKey=<первичный ключ доступа>;.
- direction — для этого свойства нужно задать значение *in*.

Пример файла *function.json*:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Пример входного кода Azure DocumentDB для триггера очереди C#
 
Используя пример файла function.json выше, входная привязка DocumentDB извлечет документ с идентификатором, который соответствует строке сообщения очереди, и передаст его в параметр document. Если этот документ не найден, параметр document будет иметь значение null. При выходе из функции документу присваивается новое значение text.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Пример входного кода Azure DocumentDB для триггера очереди F#

Используя пример файла function.json выше, входная привязка DocumentDB извлечет документ с идентификатором, который соответствует строке сообщения очереди, и передаст его в параметр document. Если этот документ не найден, параметр document будет иметь значение null. При выходе из функции документу присваивается новое значение text.

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- "This has changed."

Вам потребуется `project.json` файл, в котором используется NuGet для определения пакетов `FSharp.Interop.Dynamic` и `Dynamitey` как зависимостей пакета, следующим образом:

	{
	  "frameworks": {
	    "net46": {
	      "dependencies": {
	        "Dynamitey": "1.0.2",
	        "FSharp.Interop.Dynamic": "3.0.0"
	      }
	    }
	  }
	}

С помощью NuGet файл извлекает зависимости и создает на них ссылки в сценарии.

#### Пример входного кода Azure DocumentDB для триггера очереди Node.js
 
Используя приведенный выше пример файла function.json, входная привязка DocumentDB извлечет документ с идентификатором, который соответствует строке сообщения очереди, и передаст его в свойство привязки `documentIn`. В функциях Node.js обновленные документы не отправляются обратно в коллекцию. Тем не менее для поддержки обновлений входную привязку можно передать непосредственно в выходную привязку DocumentDB с именем `documentOut`. В этом примере кода значение свойства text входного документа обновляется и устанавливается в качестве выходного документа.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

## <a id="docdboutput"></a> Выходные привязки для Azure DocumentDB

Функции могут записать документ JSON в базу данных Azure DocumentDB с помощью выходной привязки для **документов Azure DocumentDB**. Дополнительные сведения об Azure DocumentDB см. в статье о [DocumentDB](../documentdb/documentdb-introduction.md) и в [руководстве по началу работы](../documentdb/documentdb-get-started.md).

#### Файл function.json для выходной привязки DocumentDB

Файл function.json содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для нового документа.
- `type` — для этого свойства необходимо задать значение *documentdb*.
- `databaseName` — база данных, содержащая коллекцию, в которой будет создан документ.
- `collectionName` — коллекция, в которой будет создан документ.
- `createIfNotExists` — логическое значение, указывающее, нужно ли создавать коллекцию, если она не существует. Значение по умолчанию — *false*. Это вызвано тем, что коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection` — строка **параметра приложения**, установленная в конечную точку учетной записи DocumentDB. При выборе учетной записи на вкладке **Интеграция** будет создан параметр приложения с именем в формате `yourAccount_DOCUMENTDB`. Если параметр приложения нужно создать вручную, строку подключения необходимо указать в формате `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`.
- `direction` — для этого свойства необходимо задать значение *out*.
 
Пример файла function.json:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Пример выходного кода Azure DocumentDB для триггера очереди Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

Выходной документ:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Пример выходного кода Azure DocumentDB для триггера очереди F#

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### Пример выходного кода Azure DocumentDB для триггера очереди C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Пример выходного кода Azure DocumentDB для настройки имени файла

Если нужно задать имя документа в функции, просто установите значение `id`. Например, если содержимое JSON для сотрудника передано в очередь, как в следующем примере:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

В функции триггера очереди можно использовать следующий код C#:
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Или эквивалентный код F#:

	open FSharp.Interop.Dynamic
	open Newtonsoft.Json

	type Employee = {
	    id: string
	    name: string
	    employeeId: string
	    address: string
	}

	let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
	    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
	    let employee = JObject.Parse(myQueueItem)
	    employeeDocument <-
	        { id = sprintf "%s-%s" employee?name employee?employeeId
	          name = employee?name
	          employeeId = employee?id
	          address = employee?address }

Выходные данные примера:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->