<properties
	pageTitle="Создание учетной записи DocumentDB | Microsoft Azure"
	description="Создайте базу данных NoSQL в Azure DocumentDB. Узнайте, как создать учетную записи DocumentDB, которая позволит создавать высокопроизводительные глобально масштабируемые базы данных NoSQL." 
	keywords="создание базы данных"
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
	ms.date="08/11/2016"
	ms.author="mimig"/>

# Создание учетной записи базы данных DocumentDB с помощью портала Azure

> [AZURE.SELECTOR]
- [Портал Azure](documentdb-create-account.md)
- [Azure CLI и ARM](documentdb-automation-resource-manager-cli.md)

Чтобы создать базу данных в Microsoft Azure DocumentDB, вам потребуются:

- Учетная запись Azure. Если у вас ее нет, [создайте бесплатную учетную запись](https://azure.microsoft.com/free).
- Учетная запись DocumentDB.

Учетную запись DocumentDB можно создать с помощью портала Azure, шаблонов Azure Resource Manager или интерфейса командной строки Azure (Azure CLI). В этой статье показано, как создать учетную запись базы данных на портале Azure. Чтобы создать учетную запись базы данных с помощью диспетчера ресурсов Azure или Azure CLI, см. статью [Автоматизация создания учетной базы данных DocumentDB](documentdb-automation-resource-manager-cli.md).

Не знакомы с DocumentDB? Посмотрите [это](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) четырехминутное видео Скотта Хансельмана (Scott Hanselman), чтобы узнать, как выполнять наиболее распространенные задачи на веб-портале.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Дальнейшие действия

Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

- Портал Azure, как описано в разделе [Создание базы данных DocumentDB с помощью портала Azure](documentdb-create-database.md).
- Примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на GitHub.
- Подробные учебники: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) или [Python](documentdb-python-application.md).
- [Пакеты SDK для DocumentDB](documentdb-sdk-dotnet.md). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API.


После создания базы данных необходимо [добавить одну или несколько коллекций](documentdb-create-collection.md) в базе данных, а затем [добавить документы](documentdb-view-json-document-explorer.md) в коллекции.

После добавления документов в коллекции вы можете использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](documentdb-sdk-dotnet.md).

Дополнительные сведения о DocumentDB см. в следующих разделах:

-	[Схема обучения для DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Модель ресурсов и понятия DocumentDB](documentdb-resources.md)

<!---HONumber=AcomDC_0817_2016-->