---
title: "Автоматизация управления Azure Cosmos DB (Azure CLI 2.0) | Документация Майкрософт"
description: "Использование Azure CLI 2.0 для создания учетных записей Azure Cosmos DB и управления ими. Azure Cosmos DB представляет собой высокодоступную глобально распределенную базу данных."
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Создание учетной записи для базы данных Azure Cosmos DB с помощью Azure CLI

В этом руководстве содержатся команды предварительной версии, доступные в Azure CLI 2.0, которые используются для автоматизации управления учетными записями баз данных Azure Cosmos DB. Здесь также приведены команды для управления ключами учетных записей и изменения порядка при отработке отказа в [межрегиональных учетных записях баз данных][scaling-globally]. Обновление учетной записи базы данных позволит изменить политики согласованности, а также добавить или удалить регионы. Чтобы управлять своей учетной записью базы данных Azure Cosmos DB между несколькими регионами, можно использовать [Azure Powershell](documentdb-manage-account-with-powershell.md), [REST API поставщика ресурсов][rp-rest-api] или [портал Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Приступая к работе

Чтобы настроить среду разработки с помощью Azure CLI 2.0, воспользуйтесь инструкциями, указанными в статье [How to install and configure Azure CLI 2.0][install-az-cli2] (Как установить и настроить Azure CLI 2.0).

Войдите в учетную запись Azure, выполнив команду и действия, указанные на экране.

```azurecli
az login
```

Если у вас нет существующей [группы ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups), создайте ее:

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

В параметре `<resourcegrouplocation>` должен быть указан один из регионов, в которых служба Azure Cosmos DB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Примечания

* Чтобы получить полный список доступных команд, выполните команду az documentdb -h или просмотрите [страницу справки][az-documentdb-ref].
* Выполните "az documentdb &lt;команда&gt; -h", чтобы получить подробный список обязательных и необязательных параметров каждой команды.

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>Регистрация подписки для использования базы данных Azure Cosmos DB

Эта команда регистрирует подписку для использования базы данных Azure Cosmos DB с помощью интерфейса командной строки.

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a> Создание учетной записи базы данных Azure Cosmos DB

Эта команда позволяет создать учетную запись базы данных Azure Cosmos DB. Настройте новую учетную запись для использования в одном или [нескольких регионах][scaling-globally] и добавьте определенную [политику согласованности](documentdb-consistency-levels.md).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
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

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

Примеры: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Примечания 
* В качестве расположений используйте регионы, в которых база данных Azure Cosmos DB общедоступна. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).
* Для включения доступа к порталу добавьте в список значений аргумента ip-range-filter IP-адрес портала Azure в вашем регионе, как указано в разделе [Настройка политики контроля доступа на основе IP-адресов](documentdb-firewall-support.md#configure-ip-policy).

## <a id="update-documentdb-account-cli"></a> Обновление учетной записи базы данных Azure Cosmos DB

Эта команда позволяет обновить свойства учетной записи базы данных Azure Cosmos DB. К ним относятся политика согласованности и расположения учетной записи базы данных.

> [!NOTE]
> Кроме того, эта команда позволяет добавлять или удалять регионы, но не изменять приоритеты при отработке отказа. Сведения об изменении приоритетов при отработке отказа см. [ниже](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
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

## <a id="add-remove-region-documentdb-account-cli"></a> Удаление или добавление региона из учетной записи базы данных Azure Cosmos DB

Чтобы добавить или удалить регионы в имеющейся учетной записи базы данных Azure Cosmos DB, используйте команду [update](#update-documentdb-account-cli) с флагом `--locations`. В приведенном ниже примере показано, как создать учетную запись, а затем добавлять и удалять в ней регионы.

Пример:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Удаление учетной записи базы данных Azure Cosmos DB

Эта команда позволяет удалить имеющуюся учетную запись базы данных Azure Cosmos DB.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Получение свойств учетной записи базы данных Azure Cosmos DB

Эта команда позволяет получить свойства имеющейся учетной записи базы данных Azure Cosmos DB.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Вывод списка ключей учетной записи

При создании учетной записи Azure Cosmos DB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи Azure Cosmos DB. Предоставляя два ключа, Azure Cosmos DB позволяет вам повторно создавать ключи без перерывов в работе учетной записи Azure Cosmos DB. Ключи только для чтения, используемые для выполнения проверки подлинности операций чтения, также доступны. Доступны два ключа для чтения и записи (первичный и вторичный), а также два ключа только для чтения (первичный и вторичный).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> Вывод строк подключения

Для учетных записей MongoDB строку подключения приложения MongoDB к учетной записи базы данных можно получить с помощью следующей команды:

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Пример:

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Повторное создание ключей учетных записей

Вам следует периодически менять ключи доступа для своей учетной записи Azure Cosmos DB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи Azure Cosmos DB с помощью одного ключа, одновременно повторно создавая второй ключ.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Пример:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Изменение приоритета при отработке отказа в учетной записи базы данных Azure Cosmos DB

Для межрегиональных учетных записей баз данных можно изменить приоритет при отработке отказа в разных регионах, в которых они используются. Дополнительные сведения об отработке отказа в учетной записи базы данных Azure Cosmos DB см. в статье [Как работает глобальное распределение данных в Azure Cosmos DB?](documentdb-distribute-data-globally.md)

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Пример:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в статье о [подключении и создании запросов с помощью .NET](../cosmos-db/create-documentdb-dotnet.md).
* Дополнительные сведения см. в статье о [подключении и создании запросов с помощью .NET Core](../cosmos-db/create-documentdb-dotnet-core.md).
* Дополнительные сведения см. в статье о [подключении и создании запросов с помощью Node.js и приложения MongoDB](../cosmos-db/create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

