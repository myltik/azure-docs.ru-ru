<properties 
	pageTitle="Как создать базу данных в DocumentDB | Microsoft Azure" 
	description="Узнайте, как создать базу данных с помощью портала веб-служб для Azure DocumentDB, базу данных документов NoSQL для JSON. Получите бесплатную пробную версию сегодня." 
	keywords="Как создать базу данных" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# Как создать базу данных для DocumentDB

Чтобы использовать Microsoft Azure DocumentDB, необходимы [учетная запись DocumentDB](documentdb-create-account.md), база данных, коллекция и документы. В этом разделе описывается создание базы данных для DocumentDB на портале Microsoft Azure.

![Снимок экрана, на котором показано, как создать базу данных, содержащий кнопку «Обзор», элемент «Учетные записи DocumentDB» в колонке «Обзор» и учетную запись DocumentDB в колонке «Учетные записи DocumentDB»](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  На панели быстрых переходов в разделе [Портал Azure](https://portal.azure.com/) щелкните **Учетные записи DocumentDB**. 

2.  В колонке **Учетные записи DocumentDB** выберите учетную запись, в которую следует добавить базу данных NoSQL DocumentDB. При отсутствии учетных записей необходимо [создать учетную запись DocumentDB](documentdb-create-account.md).

3. В колонке **Учетная запись DocumentDB** щелкните **Добавить базу данных**.

4. В колонке **Добавление базы данных** введите идентификатор новой базы данных. После проверки имени в поле **идентификатора** отображается зеленая галочка.

5. Нажмите **ОК** в нижней части экрана, чтобы создать базу данных.

7. Теперь новая база данных появится в разделе **Базы данных** колонки **Учетная запись DocumentDB**.
 
	![Снимок экрана: новая база данных в колонке "Учетная запись DocumentDB"](./media/documentdb-create-database/docdb-database-creation-7.png)

## Другие способы создания базы данных DocumentDB

Базы данных необязательно создавать на портале: для этого также можно использовать [пакеты SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Пример кода C#, показывающий, как создать базу данных с помощью пакета SDK для DocumentDB .NET, см. файле [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) в проекте DatabaseManagement, который находится в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) на [GitHub.com](https://github.com).

## Дальнейшие действия

Теперь, когда вы знаете, как создать базу данных для DocumentDB, пора [создать коллекцию](documentdb-create-collection.md).

После создания коллекции можно [добавить документы JSON](../documentdb-view-json-document-explorer.md) с помощью обозревателя документов на портале, [импортировать документы](documentdb-import-data.md) в коллекцию с помощью средства миграции данных DocumentDB или использовать один из [пакетов SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) для выполнения операций CRUD. DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. Примеры кода .NET, показывающие, как создать, удалить и обновить документы, см. в файле [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) в проекте DocumentManagement в репозитории azure-documentdb-net на GitHub.com.

После добавления документов в коллекции вы можете использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=AcomDC_1203_2015-->