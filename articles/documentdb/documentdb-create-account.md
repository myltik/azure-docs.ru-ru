---
title: "Как создать учетную запись Azure Cosmos DB | Документация Майкрософт"
description: "Создайте базу данных NoSQL с помощью Azure Cosmos DB. Следуя приведенным в статье инструкциям, вы создадите учетную запись Azure Cosmos DB, а также сможете создать собственную высокопроизводительную глобально масштабируемую базу данных NoSQL."
keywords: "создание базы данных"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 0e7f8488-7bb7-463e-b6fd-3ae91a02c03a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/17/2017
ms.author: mimig
redirect_url: https://aka.ms/acdbnetqa
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 13621d942f2880f4dd1523315f43099eca2607d8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-nosql-account-using-the-azure-portal"></a>Как создать учетную запись NoSQL для Azure Cosmos DB с помощью портала Azure
> [!div class="op_single_selector"]
> * [Портал Azure](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

Чтобы создать базу данных в Microsoft Azure Cosmos DB, вам потребуются:

* Учетная запись Azure. Если у вас ее нет, ее можно [создать бесплатно](https://azure.microsoft.com/free) .
* Создайте учетную запись Azure Cosmos DB.  

Вы можете создать учетную запись Azure Cosmos DB с помощью портала Azure, шаблонов Azure Resource Manager или интерфейса командной строки Azure (CLI). Из этой статьи вы узнаете, как создать учетную запись Azure Cosmos DB с помощью портала Azure. Сведения о том, как создать учетную запись с помощью Azure Resource Manager или интерфейса командной строки Azure, см. в статье об [автоматизации создания учетной записи базы данных Azure Cosmos DB](documentdb-automation-resource-manager-cli.md).

Раньше не работали с Azure Cosmos DB? Просмотрите это четырехминутное [видео](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) , в котором Скотт Хансельман (Scott Hanselman) рассказывает, как выполнять наиболее распространенные задачи на веб-портале.

1. Выполните вход на [портал Azure](https://portal.azure.com/).
2. В левой области навигации щелкните **Создать**, затем — **Базы данных** и выберите **Azure Cosmos DB**.

   ![Снимок экрана портала Azure, на котором выделено меню "Больше служб" и NoSQL (Azure Cosmos DB)](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  
3. В колонке **Новая учетная запись** укажите желаемую конфигурацию учетной записи Azure Cosmos DB.

    ![Снимок экрана новой колонки Azure Cosmos DB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

   * В поле **Идентификатор** введите имя для идентификации учетной записи Azure Cosmos DB.  После успешной проверки **идентификатора** в поле **Идентификатор** отображается зеленая галочка. Значение **идентификатора** становится именем узла в универсальном коде ресурса (URI). В **идентификаторе** могут использоваться только строчные буквы, цифры и знак "-". Его длина должна быть от 3 до 50 знаков. Обратите внимание, что к выбранному имени конечной точки добавляется *documents.azure.com*. Конечное имя будет использоваться в качестве имени конечной точки вашей учетной записи Azure Cosmos DB.
   * В разделе **API NoSQL** выберите нужную модель программирования.

     * **DocumentDB**. Этот API доступен в пакетах [SDK](documentdb-sdk-dotnet.md) для .NET, Java, Node.js, Python, JavaScript, а также через HTTP [REST](https://msdn.microsoft.com/library/azure/dn781481.aspx). Этот API для DocumentDB предоставляет программный доступ ко всем функциональным возможностям DocumentDB.
     * **MongoDB**. В DocumentDB также предлагается поддержка интерфейсов API для **MongoDB** [на уровне протокола](documentdb-protocol-mongodb.md). Выбрав API для MongoDB, вы сможете организовать взаимодействие с DocumentDB с помощью существующих пакетов SDK и [средств](documentdb-mongodb-mongochef.md) для MongoDB. Вы сможете [переместить](documentdb-import-data.md) существующие приложения MongoDB в базу данных DocumentDB, [не внося изменения в код](documentdb-connect-mongodb-account.md). В этом случае вы получите полностью управляемую базу данных как службу, неограниченные возможности масштабирования, глобальную репликацию и другие преимущества.
   * В поле **Подписка** выберите подписку Azure, которую требуется использовать для учетной записи Azure Cosmos DB. Если ваша учетная запись включает только одну подписку, эта учетная запись будет выбрана по умолчанию.
   * В разделе **Группа ресурсов** выберите или создайте группу ресурсов для своей учетной записи Azure Cosmos DB.  По умолчанию будет создана новая группа ресурсов. Дополнительные сведения см. в статье [Управление ресурсами Azure с помощью портала Azure](../azure-portal/resource-group-portal.md).
   * В поле **Расположение** укажите географическое расположение, где будет размещена учетная запись Azure Cosmos DB.
4. После настройки параметров учетной записи Azure Cosmos DB щелкните **Создать**. Чтобы узнать о состоянии развертывания, просмотрите информацию в центре уведомлений.  

   ![Быстрое создание баз данных. Снимок экрана центра уведомлений, на котором показано создание учетной записи Azure Cosmos DB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

   ![Снимок экрана центра уведомлений, на котором показано успешное создание учетной записи Azure Cosmos DB и ее развертывание в группе ресурсов. Уведомление создателя базы данных в Интернете](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)
5. После создания учетной записи Azure Cosmos DB она готова для использования с параметрами по умолчанию. Согласованность по умолчанию учетной записи Azure Cosmos DB настроена на уровне **сеанса**.  Для настройки уровня согласованности по умолчанию выберите пункт **Согласованность по умолчанию** в меню ресурсов. Дополнительные сведения об уровнях согласованности в Azure Cosmos DB см. [здесь](documentdb-consistency-levels.md).

   ![Снимок экрана: колонка "Группа ресурсов" — начало разработки приложения](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

   ![Снимок экрана: колонка "Уровень согласованности" — "Согласованность сеанса"](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create an Azure Cosmos DB account]: #Howto
[Next steps]: #NextSteps


## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у вас есть учетная запись Azure Cosmos DB, вы можете создать базу данных и коллекцию Azure Cosmos DB.

Коллекцию или базу данных можно создать одним из перечисленных ниже способов.

* С помощью портала Azure, как описано в статье о [создании коллекции Azure Cosmos DB на портале Azure](documentdb-create-collection.md).
* С помощью подробных руководств с примерами данных для [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) или [Python](documentdb-python-application.md).
* С помощью примера кода для [.NET](documentdb-dotnet-samples.md#database-examples), [Node.js](documentdb-nodejs-samples.md#database-examples) или [Python](documentdb-python-samples.md#database-examples) с сайта GitHub.
* С помощью пакетов SDK для [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md) и [REST](https://msdn.microsoft.com/library/azure/mt489072.aspx).

Создав базу данных и коллекцию, нужно в коллекцию [добавить документы](documentdb-view-json-document-explorer.md).

Когда в коллекции появятся документы, к ним можно [отправлять запросы](documentdb-sql-query.md#ExecutingSqlQueries), созданные с помощью [DocumentDB SQL](documentdb-sql-query.md). Запросы можно отправлять через [обозреватель запросов](documentdb-query-collections-query-explorer.md) на портале, с помощью [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из пакетов [SDK](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>Подробнее
Чтобы получить дополнительные сведения о службе Azure Cosmos DB, воспользуйтесь следующими ресурсами:

* [Azure Cosmos DB Introduction](../cosmos-db/introduction.md) (Обзор службы Azure Cosmos DB)

