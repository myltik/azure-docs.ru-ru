---
title: Автоматизация управления Azure Cosmos DB с помощью PowerShell | Документация Майкрософт
description: Сведения об управлении учетными записями базы данных Azure Cosmos DB с помощью Azure PowerShell.
services: cosmos-db
author: dmakwana
manager: kfile
editor: ''
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 833202353901fb9822b756f54fbcbcc155533108
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611968"
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Создание учетной записи Azure Cosmos DB с помощью PowerShell

В этом руководстве содержатся команды Azure PowerShell, используемые для автоматизации управления учетными записями баз данных Azure Cosmos DB. Здесь также приведены команды для управления ключами учетных записей и изменения порядка при отработке отказа в [межрегиональных учетных записях баз данных][scaling-globally]. Вы можете изменить политики согласованности, а также добавить или удалить регионы в учетной записи базы данных. Чтобы управлять своей учетной записью базы данных Azure Cosmos DB между несколькими регионами, можно использовать [Azure CLI](cli-samples.md), [REST API поставщика ресурсов][rp-rest-api] или [портал Azure](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Приступая к работе

Следуйте инструкциям из статьи [Приступая к работе с командлетами Azure PowerShell][powershell-install-configure], чтобы установить PowerShell и подключиться к учетной записи Azure Resource Manager через PowerShell.

### <a name="notes"></a>Заметки

* Чтобы выполнять следующие команды без запроса пользовательского подтверждения, добавьте в команду флаг `-Force`.
* Все следующие команды синхронные.

## <a id="create-documentdb-account-powershell"></a>Создание учетной записи Azure Cosmos DB

Эта команда позволяет создать учетную запись базы данных Azure Cosmos DB. Настройте новую учетную запись для использования в одном или [нескольких регионах][scaling-globally] и добавьте определенную [политику согласованности](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` — имя расположения региона для записи учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть равно 0. Каждая учетная запись базы данных должна иметь только один регион для записи.
* `<read-region-location>` — имя расположения региона для чтения учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть больше 0. Каждая учетная запись базы данных может иметь несколько регионов для чтения.
* `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье о [поддержке брандмауэра Azure Cosmos DB](firewall-support.md).
* `<default-consistency-level>` — уровень согласованности по умолчанию учетной записи Azure Cosmos DB. Дополнительные сведения см. в статье о [настраиваемых уровнях согласованности в Azure Cosmos DB](consistency-levels.md).
* `<max-interval>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое время устаревания (в секундах). Допустимый диапазон — 1–100.
* `<max-staleness-prefix>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое количество устаревших запросов. Допустимый диапазон — 1–2 147 483 647.
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<resource-group-location>` — расположение группы ресурсов Azure, в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя создаваемой учетной записи базы данных Azure Cosmos DB. В нем могут использоваться только строчные буквы, цифры и символ "-", а его длина должна составлять от 3 до 50 символов.

Пример: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Заметки
* В предыдущем примере создается учетная запись базы данных с двумя регионами. Кроме того, можно также создать учетную запись базы данных с одним регионом (регион для записи со значением приоритета отработки отказа 0) или с несколькими регионами (более двух). Дополнительные сведения см. в разделе [Масштабирование по всей планете][scaling-globally].
* В качестве расположений используйте регионы, в которых база данных Azure Cosmos DB общедоступна. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Обновление учетной записи базы данных Azure Cosmos DB

Эта команда позволяет обновить свойства учетной записи базы данных Azure Cosmos DB. К ним относятся политика согласованности и расположения учетной записи базы данных.

> [!NOTE]
> Кроме того, эта команда позволяет добавлять или удалять регионы, но не изменять приоритеты при отработке отказа. Сведения об изменении приоритетов при отработке отказа см. [ниже](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` — имя расположения региона для записи учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть равно 0. Каждая учетная запись базы данных должна иметь только один регион для записи.
* `<read-region-location>` — имя расположения региона для чтения учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть больше 0. Каждая учетная запись базы данных может иметь несколько регионов для чтения.
* `<default-consistency-level>` — уровень согласованности по умолчанию учетной записи Azure Cosmos DB. Дополнительные сведения см. в статье о [настраиваемых уровнях согласованности в Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье о [поддержке брандмауэра Azure Cosmos DB](firewall-support.md).
* `<max-interval>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое время устаревания (в секундах). Допустимый диапазон — 1–100.
* `<max-staleness-prefix>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое количество устаревших запросов. Допустимый диапазон — 1–2 147 483 647.
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<resource-group-location>` — расположение группы ресурсов Azure, в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя обновляемой учетной записи базы данных Azure Cosmos DB.

Пример: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Удаление учетной записи базы данных Azure Cosmos DB

Эта команда позволяет удалить имеющуюся учетную запись базы данных Azure Cosmos DB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя удаляемой учетной записи базы данных Azure Cosmos DB.

Пример:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Получение свойств учетной записи базы данных Azure Cosmos DB

Эта команда позволяет получить свойства имеющейся учетной записи базы данных Azure Cosmos DB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя учетной записи базы данных Azure Cosmos DB.

Пример:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a>Обновление тегов учетной записи базы данных Azure Cosmos DB

Указанная ниже команда позволяет установить [теги ресурсов Azure][azure-resource-tags] для учетной записи базы данных Azure Cosmos DB.

> [!NOTE]
> Эту команду можно выполнять вместе с командами создания или обновления. Для этого необходимо добавить флаг `-Tags` с соответствующим параметром.

Пример:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Вывод ключей учетной записи

При создании учетной записи Azure Cosmos DB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи Azure Cosmos DB. Предоставляя два ключа, Azure Cosmos DB позволяет вам повторно создавать ключи без перерывов в работе учетной записи Azure Cosmos DB. Ключи только для чтения, используемые для выполнения проверки подлинности операций чтения, также доступны. Доступны два ключа для чтения и записи (первичный и вторичный), а также два ключа только для чтения (первичный и вторичный).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя учетной записи базы данных Azure Cosmos DB.

Пример:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Вывод строк подключения

Для учетных записей MongoDB строку подключения приложения MongoDB к учетной записи базы данных можно получить с помощью следующей команды:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя учетной записи базы данных Azure Cosmos DB.

Пример:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Повторное создание ключей учетных записей

Вам следует периодически менять ключи доступа для своей учетной записи Azure Cosmos DB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи Azure Cosmos DB с помощью одного ключа, одновременно повторно создавая второй ключ.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя учетной записи базы данных Azure Cosmos DB.
* `<key-kind>` — один из четырех типов ключей, которые можно повторно создать: Primary, Secondary, PrimaryReadonly, SecondaryReadonly.

Пример:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Изменение приоритета при отработке отказа в учетной записи базы данных Azure Cosmos DB

Для межрегиональных учетных записей баз данных можно изменить приоритет при отработке отказа в разных регионах, в которых они используются. Дополнительные сведения об отработке отказа в учетной записи базы данных Azure Cosmos DB см. в статье [Как работает глобальное распределение данных в Azure Cosmos DB?][distribute-data-globally]

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` — имя расположения региона для записи учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть равно 0. Каждая учетная запись базы данных должна иметь только один регион для записи.
* `<read-region-location>` — имя расположения региона для чтения учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть больше 0. Каждая учетная запись базы данных может иметь несколько регионов для чтения.
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных Azure Cosmos DB.
* `<database-account-name>` — имя учетной записи базы данных Azure Cosmos DB.

Пример:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в статье о [подключении и создании запросов с помощью .NET](create-sql-api-dotnet.md).
* Дополнительные сведения см. в статье о [подключении и создании запросов с помощью Node.js и приложения MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
