---
title: "Автоматизация управления DocumentDB (Azure CLI 2.0) | Документация Майкрософт"
description: "Учетными записями базы данных DocumentDB можно управлять с помощью Azure CLI 2.0. DocumentDB — это облачная база данных NoSQL для данных JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b286a93d7cc5f962f969e877b2f487e56cbb1a95
ms.lasthandoff: 03/30/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Автоматизация управления учетными записями Azure DocumentDB с помощью Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Портал Azure](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

В этом руководстве содержатся команды предварительной версии DocumentDB, доступные в Azure CLI 2.0, которые используются для автоматизации управления учетными записями баз данных DocumentDB. Здесь также приведены команды для управления ключами учетных записей и изменения порядка при отработке отказа в [межрегиональных учетных записях баз данных][scaling-globally]. Обновление учетной записи базы данных позволит изменить политики согласованности, а также добавить или удалить регионы. Чтобы управлять своей учетной записью базы данных DocumentDB между несколькими регионами, можно использовать [Azure Powershell](documentdb-manage-account-with-powershell.md), [REST API поставщика ресурсов][rp-rest-api] или [портал Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Приступая к работе

Чтобы настроить среду разработки с помощью Azure CLI 2.0, воспользуйтесь инструкциями, указанными в статье [How to install and configure Azure CLI 2.0][install-az-cli2] (Как установить и настроить Azure CLI 2.0).

Войдите в учетную запись Azure, выполнив команду и действия, указанные на экране.

    az login

Если у вас нет существующей [группы ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups), создайте ее:

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

В параметре `<resourcegrouplocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Примечания

* Чтобы получить полный список доступных команд, выполните команду az documentdb -h или просмотрите [страницу справки][az-documentdb-ref].
* Выполните команду az documentdb <command> - h, чтобы получить подробный список обязательных и необязательных параметров каждой команды.

## <a id="create-documentdb-account-cli"></a> Создание учетной записи базы данных DocumentDB

Эта команда позволяет создать учетную запись базы данных DocumentDB. Настройте новую учетную запись для использования в одном или [нескольких регионах][scaling-globally] и добавьте определенную [политику согласованности](documentdb-consistency-levels.md). 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
                                    To enable portal access, include 104.42.195.92.
    --kind                        : The type of DocumentDB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

Примеры: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Примечания
* В качестве расположений используйте регионы, в которых база данных DocumentDB общедоступна. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-cli"></a> Обновление учетной записи базы данных DocumentDB

Эта команда позволяет обновить свойства учетной записи базы данных DocumentDB. К ним относятся политика согласованности и расположения учетной записи базы данных.

> [!NOTE]
> Кроме того, эта команда позволяет добавлять или удалять регионы, но не изменять приоритеты при отработке отказа. Сведения об изменении приоритетов при отработке отказа см. [ниже](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

Примеры: 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> Удаление или добавление региона из учетной записи базы данных DocumentDB

Чтобы добавить или удалить регионы в имеющейся учетной записи базы данных DocumentDB, используйте команду [update](#update-documentdb-account-cli) с флагом `--locations`. В приведенном ниже примере показано, как создать учетную запись, а затем добавлять и удалять в ней регионы.

Пример:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Удаление учетной записи базы данных DocumentDB

Эта команда позволяет удалить имеющуюся учетную запись базы данных DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Получение свойств учетной записи базы данных DocumentDB

Эта команда позволяет получить свойства имеющейся учетной записи базы данных DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Вывод списка ключей учетной записи

При создании учетной записи DocumentDB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи DocumentDB. Предоставляя два ключа, DocumentDB позволяет вам повторно создавать ключи без перерывов в работе учетной записи DocumentDB. Ключи только для чтения, используемые для выполнения проверки подлинности операций чтения, также доступны. Доступны два ключа для чтения и записи (первичный и вторичный), а также два ключа только для чтения (первичный и вторичный).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Повторное создание ключей учетных записей

Вам следует периодически менять ключи доступа для своей учетной записи DocumentDB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи DocumentDB с помощью одного ключа, одновременно повторно создавая второй ключ.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Пример:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Изменение приоритета при отработке отказа в учетной записи базы данных DocumentDB

Для межрегиональных учетных записей баз данных можно изменить приоритет при отработке отказа в разных регионах, в которых они используются. Дополнительные сведения об отработке отказа в учетной записи базы данных DocumentDB см. в статье [Глобальное распространение данных с помощью DocumentDB](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Пример:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Дальнейшие действия
Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

* С помощью портала Azure, как описано в статье [Создание коллекции и базы данных DocumentDB на портале Azure](documentdb-create-collection.md).
* примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на портале GitHub;
* [Пакеты SDK для DocumentDB](documentdb-sdk-dotnet.md). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API.

Создав базу данных, в нее нужно [добавить хотя бы одну коллекцию](documentdb-create-collection.md), а затем в коллекцию [добавить документы](documentdb-view-json-document-explorer.md).


Добавив документы в коллекцию, [DocumentDB SQL](documentdb-sql-query.md) можно использовать для [выполнения запросов](documentdb-sql-query.md#ExecutingSqlQueries) к документам с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Дополнительные сведения о DocumentDB можно получить из следующих ресурсов:

* [Схема обучения для DocumentDB.](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Модель ресурсов и понятия DocumentDB](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

