<properties 
	pageTitle="Создание коллекции базы данных DocumentDB | Microsoft Azure" 
	description="Узнайте, как создать коллекции с помощью портала интернет-служб для Azure DocumentDB, управляемой базы данных документов NoSQL для JSON. Получите бесплатную пробную версию сегодня." 
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
	ms.date="09/28/2015" 
	ms.author="mimig"/>

# Создание коллекции DocumentDB на портале предварительной версии Azure

Чтобы использовать Microsoft Azure DocumentDB, необходимы [учетная запись DocumentDB](documentdb-create-account.md), [база данных](documentdb-create-database.md), коллекция и документы. В этом разделе описывается создание коллекции DocumentDB на портале предварительной версии Azure.

![Снимок экрана, на котором изображены учетные записи DocumentDB на панели быстрых переходов, учетная запись в колонке "Учетные записи DocumentDB" и база данных в колонке учетной записи DocumentDB в группе связанных элементов "База данных"](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  На панели быстрых переходов в разделе [Портал предварительной версии Azure](https://portal.azure.com/) щелкните **Учетные записи DocumentDB**. 

2.  В колонке **Учетные записи DocumentDB** выберите учетную запись, в которую необходимо добавить коллекцию. При отсутствии учетных записей необходимо [создать учетную запись DocumentDB](documentdb-create-account.md).

3. Для выбранной учетной записи прокрутите колонку **Учетная запись DocumentDB** вниз до группы связанных элементов **Базы данных**, а затем выберите базу данных, в которую необходимо добавить коллекцию.
    
4. В колонке **База данных** щелкните **Добавить коллекции**.

	![Снимок экрана, на котором изображены кнопка "Добавить коллекцию" в колонке "База данных", параметры в колонке "Добавление коллекции" и кнопка OK](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. В колонке **Добавление коллекции** введите идентификатор новой коллекции. После проверки имени в поле идентификатора отображается зеленая галочка.

6. Выберите ценовую категорию для новой коллекции. Каждая создаваемая коллекция — это платная сущность. Дополнительные сведения о доступных уровнях производительности см. в разделе [Уровни производительности в DocumentDB](documentdb-performance-levels.md).

7. Выберите одну из указанных ниже **Политик индексации**.

	- **По умолчанию**. Эта политика лучше всего подходит при выполнении запросов равенства строк и использовании предложения ORDER BY, диапазона и запросов равенства для чисел. Эта политика использует меньший объем хранилища для служебных данных при индексации, чем политика **Диапазон**.
	- **Хэш**. Эта политика лучше всего подходит при выполнении запросов равенства для чисел и строк. Эта политика имеет низкий индекс служебных данных хранилища.
	- **Диапазон**. Эта политика лучше всего подходит при использовании предложения ORDER BY, диапазона и запросов равенства на числах и строках. Эта политика использует больший объем хранилища для служебных данных при индексации, чем политики **По умолчанию** и **Хэш**.

	Дополнительные сведения о политиках индексации см. в статье [Политики индексации DocumentDB](documentdb-indexing-policies.md).

8. Нажмите **ОК** в нижней части экрана, чтобы создать коллекцию.


9. Новая коллекция теперь отображается в разделе **Коллекции** колонки **База данных**.
 
	![Снимок экрана новой коллекции в колонке "База данных"](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Другие способы создания коллекции DocumentDB

Коллекции необязательно создавать на портале предварительной версии. Их также можно создать с помощью [пакетов SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Пример кода C#, показывающий, как создать коллекцию с помощью пакета SDK для DocumentDB .NET, см. файле [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) в проекте CollectionManagement, который находится в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) на [GitHub.com](https://github.com).

## Дальнейшие действия

Следующий шаг после создания коллекция — добавление или импорт документов в коллекцию. Добавить документы в коллекцию можно несколькими способами.

- [Добавить документы](../documentdb-view-json-document-explorer.md) можно с помощью обозревателя документов на портале предварительной версии.
- Вы можете [импортировать документы и данные](documentdb-import-data.md) с помощью средства миграции данных DocumentDB, которое позволяет импортировать JSON- и CSV-файлы, а также данные из SQL Server, MongoDB, табличного хранилища Azure и других коллекций DocumentDB. 
- Либо можно добавить документы с помощью одного из [пакетов SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. В файле [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) в проекте DocumentManagement в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) на [GitHub.com](https://github.com), показаны операции CRUD в документах с помощью пакета SDK для DocumentDB .NET.

После добавления документов в коллекции можно использовать [DocumentDB SQL](documentdb-sql-query.md) для [выполнения запросов](documentdb-sql-query.md#executing-queries) для документов с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале предварительной версии, интерфейса [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=Oct15_HO3-->