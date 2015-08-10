<properties 
	pageTitle="Создание базы данных NoSQL DocumentDB | Microsoft Azure" 
	description="Узнайте, как создать управляемые базы данных с помощью портала интернет-служб для Azure DocumentDB, базы данных документов NoSQL для JSON. Получите бесплатную пробную версию сегодня." 
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
	ms.topic="get-started-article" 
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# Создание базы данных DocumentDB на портале предварительной версии Azure

Чтобы использовать Microsoft Azure DocumentDB, необходимы [учетная запись DocumentDB](documentdb-create-account.md), база данных, коллекция и документы. В этом разделе описывается создание базы данных DocumentDB на портале предварительной версии Microsoft Azure.

Базы данных необязательно создавать на портале предварительной версии. Их также можно создать с помощью [пакетов SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Пример кода C#, показывающий, как создать базу данных с помощью пакета SDK для DocumentDB .NET, см. файле [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) в проекте DatabaseManagement, который находится в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) на [GitHub.com](https://github.com).

1.  На [портале предварительной версии Azure](https://portal.azure.com/) нажмите кнопку **Просмотреть все**.

2.  В колонке **Обзор** щелкните **Учетные записи DocumentDB**.

3.  В колонке **Учетные записи DocumentDB** выберите учетную запись, в которую следует добавить базу данных DocumentDB. При отсутствии учетных записей необходимо [создать учетную запись DocumentDB](documentdb-create-account.md).
    
    ![Снимок экрана c кнопкой "Обзор", учетными записями DocumentDB в колонке "Обзор" и учетной записью DocumentDB в колонке "Учетные записи DocumentDB"](./media/documentdb-create-database/docdb-database-creation-1-3.png)

4. В колонке **Учетная запись DocumentDB** щелкните **Добавить базу данных**.

5. В колонке **Добавление базы данных** введите идентификатор новой базы данных. После проверки имени в поле идентификатора отображается зеленая галочка.

6. Нажмите **ОК** в нижней части экрана, чтобы создать базу данных.

	![Снимок экрана: выделенная кнопка "Добавить базу данных" в колонке "Учетная запись DocumentDB", поле "Идентификатор" в колонке "Добавление базы данных" и кнопка "ОК"](./media/documentdb-create-database/docdb-database-creation-4-6.png)

8. Теперь новая база данных появится в разделе **Базы данных** колонки **Учетная запись DocumentDB**.
 
	![Снимок экрана: новая база данных в колонке "Учетная запись DocumentDB"](./media/documentdb-create-database/docdb-database-creation-7.png)

## Дальнейшие действия

Следующий шаг после создания базы данных DocumentDB — [создание коллекции](documentdb-create-collection.md).

После создания коллекции можно [добавить документы](../documentdb-view-json-document-explorer.md) с помощью обозревателя документов на портале предварительной версии, [импортировать документы](documentdb-import-data.md) в коллекцию с помощью средства миграции данных DocumentDB или использовать один из [пакетов SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) для выполнения операций CRUD. DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. Примеры кода .NET, показывающие, как создать, удалить и обновить документы, см. в файле [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) в проекте DocumentManagement в репозитории azure-documentdb-net на GitHub.com.

После добавления документов в коллекции можно использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале предварительной версии, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=July15_HO5-->