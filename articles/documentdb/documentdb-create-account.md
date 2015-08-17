<properties 
	pageTitle="Создание учетной записи базы данных — бесплатная пробная версия | Microsoft Azure" 
	description="Узнайте, как создать учетные записи базы данных с помощью портала интернет-служб для Azure DocumentDB, управляемой базы данных документов NoSQL для JSON. Получите бесплатную пробную версию сегодня."
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# Создание учетной записи базы данных DocumentDB на портале предварительной версии Azure

Для применения Microsoft Azure DocumentDB необходимо создать учетную запись базы данных DocumentDB с помощью портала предварительной версии Azure.

Не знакомы с DocumentDB? Посмотрите [это](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) четырехминутное видео Скотта Хансельмана (Scott Hanselman), чтобы узнать, как выполнять наиболее распространенные задачи на веб-портале.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Дальнейшие действия

Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

- Примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на GitHub.
- Портал предварительной версии, как описано в разделе [Создание базы данных DocumentDB с помощью портала предварительной версии Azure](documentdb-create-database.md).
- Подробные учебники: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) или [Python](documentdb-python-application.md).
- [Пакеты SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. 


После создания базы данных необходимо [добавить одну или несколько коллекций](documentdb-create-collection.md) в базе данных, а затем [добавить документы](documentdb-view-json-document-explorer.md) в коллекции.

После добавления документов в коллекции можно использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале предварительной версии, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Дополнительные сведения о DocumentDB см. в следующих разделах:

-	[О карте для DocumentDB](documentdb-learning-map.md)
-	[Модель ресурсов и понятия DocumentDB](documentdb-resources.md)

 

<!---HONumber=August15_HO6-->