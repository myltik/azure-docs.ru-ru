---
title: "Автоматизация управления Azure DocumentDB с помощью Powershell | Документация Майкрософт"
description: "Сведения об управлении учетными записями базы данных DocumentDB с помощью Azure PowerShell. DocumentDB — это облачная база данных NoSQL для данных JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 8078f9822b392af09e00e9bf1e448e0a51994e11
ms.openlocfilehash: 15707a71500424e4776adc80491af95b57bea222
ms.lasthandoff: 02/27/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-powershell"></a>Автоматизация управления учетными записями Azure DocumentDB с помощью Azure Powershell
> [!div class="op_single_selector"]
> * [Портал Azure](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

В этом руководстве содержатся команды Azure Powershell, используемые для автоматизации управления учетными записями баз данных DocumentDB. Здесь также приведены команды для управления ключами учетных записей и изменения порядка при отработке отказа в [межрегиональных учетных записях баз данных][scaling-globally]. Вы можете изменить политики согласованности, а также добавить или удалить регионы в учетной записи базы данных. Чтобы управлять своей учетной записью базы данных DocumentDB между несколькими регионами, можно использовать [Azure CLI](documentdb-automation-resource-manager-cli.md), [REST API поставщика ресурсов][rp-rest-api] или [портал Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Приступая к работе

Следуйте инструкциям из статьи [Приступая к работе с командлетами Azure PowerShell][powershell-install-configure], чтобы установить Powershell и подключиться к учетной записи Azure Resource Manager через Powershell.

### <a name="notes"></a>Примечания

* Чтобы выполнять следующие команды без запроса пользовательского подтверждения, добавьте в команду флаг `-Force`.
* Все следующие команды синхронные.

## <a name="a-idcreate-documentdb-account-powershella-create-a-documentdb-database-account"></a><a id="create-documentdb-account-powershell"></a> Создание учетной записи базы данных DocumentDB

Эта команда позволяет создать учетную запись базы данных DocumentDB. Настройте новую учетную запись для использования в одном или [нескольких регионах][scaling-globally] и добавьте определенную [политику согласованности](documentdb-consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` — имя расположения региона для записи учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть равно 0. Каждая учетная запись базы данных должна иметь только один регион для записи.
* `<read-region-location>` — имя расположения региона для чтения учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть больше 0. Каждая учетная запись базы данных может иметь несколько регионов для чтения.
* `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье [Поддержка брандмауэра DocumentDB](documentdb-firewall-support.md).
* `<default-consistency-level>` — уровень согласованности по умолчанию учетной записи DocumentDB. Дополнительные сведения см. в статье [Уровни согласованности в DocumentDB](documentdb-consistency-levels.md).
* `<max-interval>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое время устаревания (в секундах). Допустимый диапазон — 1–100.
* `<max-staleness-prefix>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое количество устаревших запросов. Допустимый диапазон — 1–2 147 483 647.
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<resource-group-location>` — расположение группы ресурсов Azure, в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя создаваемой учетной записи базы данных DocumentDB. В нем могут использоваться только строчные буквы, цифры и символ "-", а его длина должна составлять от 3 до 50 символов.

Пример: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -PropertyObject $DocumentDBProperties

### <a name="notes"></a>Примечания
* В предыдущем примере создается учетная запись базы данных с двумя регионами. Кроме того, можно также создать учетную запись базы данных с одним регионом (регион для записи со значением приоритета отработки отказа 0) или с несколькими регионами (более двух). Дополнительные сведения см. в разделе [Масштабирование по всей планете][scaling-globally].
* В качестве расположений используйте регионы, в которых база данных DocumentDB общедоступна. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

## <a name="a-idupdate-documentdb-account-powershella-update-a-documentdb-database-account"></a><a id="update-documentdb-account-powershell"></a> Обновление учетной записи базы данных DocumentDB

Эта команда позволяет обновить свойства учетной записи базы данных DocumentDB. К ним относятся политика согласованности и расположения учетной записи базы данных.

> [!NOTE]
> Кроме того, эта команда позволяет добавлять или удалять регионы, но не изменять приоритеты при отработке отказа. Сведения об изменении приоритетов при отработке отказа см. [ниже](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` — имя расположения региона для записи учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть равно 0. Каждая учетная запись базы данных должна иметь только один регион для записи.
* `<read-region-location>` — имя расположения региона для чтения учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть больше 0. Каждая учетная запись базы данных может иметь несколько регионов для чтения.
* `<default-consistency-level>` — уровень согласованности по умолчанию учетной записи DocumentDB. Дополнительные сведения см. в статье [Уровни согласованности в DocumentDB](documentdb-consistency-levels.md).
* `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье [Поддержка брандмауэра DocumentDB](documentdb-firewall-support.md).
* `<max-interval>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое время устаревания (в секундах). Допустимый диапазон — 1–100.
* `<max-staleness-prefix>`. При использовании согласованности с ограниченным устареванием это значение указывает допустимое количество устаревших запросов. Допустимый диапазон — 1–2 147 483 647.
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<resource-group-location>` — расположение группы ресурсов Azure, в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя обновляемой учетной записи базы данных DocumentDB.

Пример: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -PropertyObject $DocumentDBProperties

## <a name="a-iddelete-documentdb-account-powershella-delete-a-documentdb-database-account"></a><a id="delete-documentdb-account-powershell"></a> Удаление учетной записи базы данных DocumentDB

Эта команда позволяет удалить имеющуюся учетную запись базы данных DocumentDB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя удаляемой учетной записи базы данных DocumentDB.

Пример:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idget-documentdb-properties-powershella-get-properties-of-a-documentdb-database-account"></a><a id="get-documentdb-properties-powershell"></a> Получение свойств учетной записи базы данных DocumentDB

Эта команда позволяет получить свойства имеющейся учетной записи базы данных DocumentDB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя учетной записи базы данных DocumentDB.

Пример:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idupdate-tags-powershella-update-tags-of-a-documentdb-database-account"></a><a id="update-tags-powershell"></a> Обновление тегов учетной записи базы данных DocumentDB

Следующая команда позволяет установить [теги ресурсов Azure][azure-resource-tags] для учетной записи базы данных DocumentDB.

> [!NOTE]
> Эту команду можно выполнять вместе с командами создания или обновления. Для этого необходимо добавить флаг `-Tags` с соответствующим параметром.

Пример:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a name="a-idlist-account-keys-powershella-list-account-keys"></a><a id="list-account-keys-powershell"></a> Вывод ключей учетной записи

При создании учетной записи DocumentDB служба создает два главных ключа доступа, которые можно использовать для проверки подлинности при доступе к учетной записи DocumentDB. Предоставляя два ключа, DocumentDB позволяет вам повторно создавать ключи без перерывов в работе учетной записи DocumentDB. Ключи только для чтения, используемые для выполнения проверки подлинности операций чтения, также доступны. Доступны два ключа для чтения и записи (первичный и вторичный), а также два ключа только для чтения (первичный и вторичный).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя учетной записи базы данных DocumentDB.

Пример:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idregenerate-account-key-powershella-regenerate-account-key"></a><a id="regenerate-account-key-powershell"></a> Повторное создание ключей учетных записей

Вам следует периодически менять ключи доступа для своей учетной записи DocumentDB, чтобы повысить уровень безопасности соединений. Назначается два ключа доступа, что позволяет поддерживать подключения к учетной записи DocumentDB с помощью одного ключа, одновременно повторно создавая второй ключ.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя учетной записи базы данных DocumentDB.
* `<key-kind>` — один из четырех типов ключей, которые можно повторно создать: Primary, Secondary, PrimaryReadonly, SecondaryReadonly.

Пример:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a name="a-idmodify-failover-priority-powershella-modify-failover-priority-of-a-documentdb-database-account"></a><a id="modify-failover-priority-powershell"></a> Изменение приоритета при отработке отказа в учетной записи базы данных DocumentDB

Для межрегиональных учетных записей баз данных можно изменить приоритет при отработке отказа в разных регионах, в которых они используются. Дополнительные сведения об отработке отказа в учетной записи базы данных DocumentDB см. в статье [Глобальное распространение данных с помощью DocumentDB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` — имя расположения региона для записи учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть равно 0. Каждая учетная запись базы данных должна иметь только один регион для записи.
* `<read-region-location>` — имя расположения региона для чтения учетной записи базы данных. Значение приоритета отработки отказа для этого расположения должно быть больше 0. Каждая учетная запись базы данных может иметь несколько регионов для чтения.
* `<resource-group-name>` — имя [группы ресурсов Azure][azure-resource-groups], в которую входит новая учетная запись базы данных DocumentDB.
* `<database-account-name>` — имя учетной записи базы данных DocumentDB.

Пример:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/en-us/azure/powershell-install-configure
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[distribute-data-globally]: https://docs.microsoft.com/en-us/azure/documentdb/documentdb-distribute-data-globally
[azure-resource-groups]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/
