<properties 
	pageTitle="Создание учетной записи базы данных (кроме БД SQL) — бесплатная пробная версия | Microsoft Azure" 
	description="Узнайте, как создать учетные записи базы данных с помощью средства создания интернет-баз данных для Azure DocumentDB, управляемой базы данных документов NoSQL для JSON. Получите бесплатную пробную версию сегодня."
	keywords="Бесплатная пробная версия, средство создания оперативных баз данных, создать базу данных, создание базы данных, documentdb, azure, Microsoft azure"
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
	ms.date="12/03/2015" 
	ms.author="mimig"/>

# Создание учетной записи базы данных DocumentDB на портале Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

Чтобы использовать Microsoft Azure DocumentDB, необходимо создать учетную запись базы данных DocumentDB с помощью портала Azure, шаблонов диспетчера ресурсов Azure или интерфейса командной строки Azure (CLI). В этой статье показано, как создать учетную запись базы данных на портале Azure. Чтобы создать учетную запись базы данных с помощью диспетчера ресурсов Azure или Azure CLI, см. статью [Автоматизация создания учетной базы данных DocumentDB](documentdb-automation-resource-manager-cli.md).

Не знакомы с DocumentDB? Посмотрите [это](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) четырехминутное видео Скотта Хансельмана (Scott Hanselman), чтобы узнать, как выполнять наиболее распространенные задачи на веб-портале.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Дальнейшие действия

Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

- Примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на GitHub.
- Портал Azure, как описано в разделе [Создание базы данных DocumentDB с помощью портала Azure](documentdb-create-database.md).
- Подробные учебники: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) или [Python](documentdb-python-application.md).
- [Пакеты SDK для DocumentDB](documentdb-sdk-dotnet.md). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. 


После создания базы данных необходимо [добавить одну или несколько коллекций](documentdb-create-collection.md) в базе данных, а затем [добавить документы](documentdb-view-json-document-explorer.md) в коллекции.

После добавления документов в коллекции вы можете использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](documentdb-sdk-dotnet.md).

Дополнительные сведения о DocumentDB см. в следующих разделах:

-	[Схема обучения для DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Модель ресурсов и понятия DocumentDB](documentdb-resources.md)

 

<!---HONumber=AcomDC_1210_2015-->