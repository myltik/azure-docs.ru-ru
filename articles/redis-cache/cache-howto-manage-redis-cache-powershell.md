---
title: Управление кэшем Redis для Azure с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как осуществлять администрирование кэша Redis для Azure с помощью Azure PowerShell.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 38b2f57811b0e952d3020c06d39350918f2f0391
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Управление кэшем Redis для Azure с использованием Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Интерфейс командной строки Azure](cache-manage-cli.md)
> 
> 

В этом разделе демонстрируется выполнение таких обычных задач, как создание, обновление и масштабирование экземпляров кэша Redis для Azure, повторное создание ключей доступа и просмотр сведений о кэшах.  См. полный список [командлетов PowerShell для работы с кэшем Redis для Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Дополнительные сведения о классической модели развертывания см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов).

## <a name="prerequisites"></a>предварительным требованиям
Если вы уже установили Azure PowerShell, необходимо использовать Azure PowerShell 1.0.0 или более поздней версии. Установленную версию Azure PowerShell можно узнать в командной строке Azure PowerShell с помощью такой команды:

    Get-Module azure | format-table version


Сначала необходимо войти в Azure с помощью следующей команды.

    Connect-AzureRmAccount

Укажите адрес электронной почты и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Далее, если у вас есть несколько подписок Azure, необходимо задать лишь одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Чтобы использовать Windows PowerShell с диспетчером ресурсов Azure, необходимо следующее:

* Windows PowerShell версии 3.0 или 4.0. Чтобы найти версию Windows PowerShell, введите `$PSVersionTable` и убедитесь, что для `PSVersion` указано значение 3.0 или 4.0. Сведения об установке совместимой версии см. в разделе [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

    Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета `New-AzureRmRedisCache` , введите:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Подключение к другим облакам
Среда Azure по умолчанию — это `AzureCloud`, представляющая экземпляр глобального облака Azure. Чтобы подключиться к другому экземпляру, используйте команду `Connect-AzureRmAccount`, заменив параметр `-Environment` или -`EnvironmentName` на нужную вам среду или имя среды.

Чтобы просмотреть список доступных сред, выполните командлет `Get-AzureRmEnvironment` .

### <a name="to-connect-to-the-azure-government-cloud"></a>Подключение к облаку Azure Government
Чтобы подключиться к облаку Azure Government, используйте одну из следующих команд.

    Connect-AzureRmAccount -EnvironmentName AzureUSGovernment

или

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Чтобы создать кэш в облаке Azure Government, используйте одно из следующих расположений.

* Правительство штата Вирджиния
* Правительство штата Айова

Дополнительные сведения об облаке Azure для государственных организаций см. на странице [Microsoft Azure для государственных организаций](https://azure.microsoft.com/features/gov/) и в [руководстве для разработчиков Microsoft Azure для государственных организаций](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Подключение к облаку Azure China
Чтобы подключиться к облаку Azure China, используйте одну из следующих команд.

    Connect-AzureRmAccount -EnvironmentName AzureChinaCloud

или

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Чтобы создать кэш в облаке Azure China, используйте одно из следующих расположений.

* Восток Китая
* Север Китая

Дополнительные сведения об облаке Azure для Китая см. на странице [AzureChinaCloud для Azure под управлением 21Vianet в Китае](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Подключение к Microsoft Azure для Германии
Чтобы подключиться к Microsoft Azure для Германии, используйте одну из следующих команд.

    Connect-AzureRmAccount -EnvironmentName AzureGermanCloud


или

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

Чтобы создать кэш в Microsoft Azure для Германии, используйте одно из следующих расположений.

* Центральная Германия
* Северо-восточная Германия

Дополнительные сведения о Microsoft Azure для Германии см. [здесь](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Свойства, используемые в командлетах PowerShell кэша Redis для Azure
Приведенная ниже таблица содержит свойства и описания параметров, часто используемых при создании экземпляров кэша Redis для Azure с помощью Azure PowerShell и управления такими экземплярами.

| Параметр | ОПИСАНИЕ | значение по умолчанию |
| --- | --- | --- |
| ИМЯ |Имя кэша | |
| Расположение |Расположение кэша | |
| ResourceGroupName |Имя группы ресурсов, в которой необходимо создать кэш | |
| Размер |Размер кэша. Допустимые значения: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 МБ, 1 ГБ, 2,5 ГБ, 6 ГБ, 13 ГБ, 26 ГБ, 53 ГБ |1 ГБ |
| ShardCount |Число сегментов, которые будут созданы при создании кэша уровня Premium с включенной кластеризацией. Допустимые значения: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Определяет SKU кэша. Допустимые значения: Basic, Standard, Premium |Стандартная |
| RedisConfiguration |Задает параметры конфигурации кластера Redis. Подробные сведения о каждом параметре представлены в таблице [Свойства RedisConfiguration](#redisconfiguration-properties) . | |
| EnableNonSslPort |Определяет, включен ли порт без SSL. |Ложь |
| MaxMemoryPolicy |Этот параметр устарел, вместо него используется параметр RedisConfiguration. | |
| StaticIP |При размещении кэша в виртуальной сети определяет уникальный IP-адрес подсети для кэша. Если IP-адрес не указан, он автоматически выбирается из подсети. | |
| Подсеть |При размещении кэша в виртуальной сети определяет имя подсети, в которой будет развернут кэш. | |
| Виртуальная сеть |При размещении кэша в виртуальной сети определяет идентификатор ресурса виртуальной сети, в которой будет развернут кэш. | |
| KeyType |Определяет, какой ключ доступа будет создаваться повторно при обновлении ключей доступа. Допустимые значения: Primary, Secondary | |

### <a name="redisconfiguration-properties"></a>Свойства RedisConfiguration
| Свойство | ОПИСАНИЕ | Ценовые категории |
| --- | --- | --- |
| rdb-backup-enabled |Указывает на то, включен ли параметр [Сохраняемость данных Redis](cache-how-to-premium-persistence.md) . |Только "Премиум" |
| rdb-storage-connection-string |Строка подключения к учетной записи хранения для параметра [Сохраняемость данных Redis](cache-how-to-premium-persistence.md) |Только "Премиум" |
| rdb-backup-frequency |Указывает частоту резервного копирования для параметра [Сохраняемость данных Redis](cache-how-to-premium-persistence.md) |Только "Премиум" |
| maxmemory-reserved |Определяет [объем памяти, зарезервированный](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) для процессов, не связанных с кэшем. |"Стандартный" и "Премиум" |
| maxmemory-policy |Определяет [политику вытеснения](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) для кэша. |Все ценовые категории |
| notify-keyspace-events |Настраивает [уведомления пространства ключей](cache-configure.md#keyspace-notifications-advanced-settings) |"Стандартный" и "Премиум" |
| hash-max-ziplist-entries |Настраивает [оптимизацию памяти](http://redis.io/topics/memory-optimization) для небольших сводных данных. |"Стандартный" и "Премиум" |
| hash-max-ziplist-value |Настраивает [оптимизацию памяти](http://redis.io/topics/memory-optimization) для небольших сводных данных. |"Стандартный" и "Премиум" |
| set-max-intset-entries |Настраивает [оптимизацию памяти](http://redis.io/topics/memory-optimization) для небольших сводных данных. |"Стандартный" и "Премиум" |
| zset-max-ziplist-entries |Настраивает [оптимизацию памяти](http://redis.io/topics/memory-optimization) для небольших сводных данных. |"Стандартный" и "Премиум" |
| zset-max-ziplist-value |Настраивает [оптимизацию памяти](http://redis.io/topics/memory-optimization) для небольших сводных данных. |"Стандартный" и "Премиум" |
| databases |Определяет количество баз данных. Это свойство можно настроить только в момент создания кэша. |"Стандартный" и "Премиум" |

## <a name="to-create-a-redis-cache"></a>Создание кэша Redis
Новые экземпляры кэша Redis для Azure создаются с помощью командлета [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

> [!IMPORTANT]
> Когда кэш Redis создается в подписке с использованием портала Azure впервые, портал регистрирует пространство имен `Microsoft.Cache` для этой подписки. Если первый кэш Redis создается в подписке с использованием PowerShell, необходимо зарегистрировать пространство имен с помощью представленной ниже команды, иначе команды `New-AzureRmRedisCache` и `Get-AzureRmRedisCache` завершатся неудачей.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Чтобы увидеть список доступных параметров свойства `New-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы создать кэш с параметрами по умолчанию, выполните следующую команду:

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name` и `Location` — обязательные параметры; остальные указываются по желанию и имеют значения по умолчанию. При выполнении предыдущей команды создается экземпляр кэша Redis для Azure SKU Standard с заданным именем, расположением и группой ресурсов; размер экземпляра — 1 ГБ, порт без SSL отключен.

Для создания кэша уровня Premium укажите размер P1 (6-60 ГБ), P2 (13-130 ГБ), (26-260 ГБ), P3 и P4 (53-530 ГБ). Чтобы включить кластеризацию, укажите количество сегментов с помощью параметра `ShardCount` . В следующем примере создается кэш P1 уровня Premium с 3 сегментами. Размер кэша P1 уровня Premium — 6 ГБ, и, поскольку мы указали три сегмента, общий размер составляет 18 ГБ (3 x 6 ГБ).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Чтобы указать значения для параметра `RedisConfiguration`, включите их в фигурные скобки (`{}`) как пары "ключ-значение" типа `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. В следующем примере создается кэш уровня Standard размером 1 ГБ с политикой максимальной памяти `allkeys-random` и уведомлениями пространства ключей, настроенными с помощью `KEA`. Дополнительные сведения см. в разделах [Уведомления пространства ключей (дополнительные параметры)](cache-configure.md#keyspace-notifications-advanced-settings) и [Политики памяти](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Настройка параметров баз данных в процессе создания кэша
Параметр `databases` можно настроить только при создании кэша. В следующем примере создается кэш Premium P3 (26 ГБ) на 48 баз данных, использующих командлет [New AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Дополнительные сведения о свойстве `databases` см. в разделе [Конфигурация сервера Redis по умолчанию](cache-configure.md#default-redis-server-configuration). Дополнительные сведения о создании кэша с помощью командлета [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) см. в предыдущем разделе [Создание кэша Redis](#to-create-a-redis-cache).

## <a name="to-update-a-redis-cache"></a>Обновление кэша Redis
Экземпляры кэша Redis для Azure обновляются с помощью командлета [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Чтобы увидеть список доступных параметров свойства `Set-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Командлет `Set-AzureRmRedisCache` можно использовать для обновления таких свойств, как значения `Size`, `Sku`, `EnableNonSslPort` и `RedisConfiguration`. 

Следующая команда обновляет политику максимальной памяти кэша Redis с именем myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Масштабирование кэша Redis
`Set-AzureRmRedisCache` можно использовать для масштабирования экземпляров кэша Redis для Azure при изменении свойств `Size`, `Sku` или `ShardCount`. 

> [!NOTE]
> Масштабирование кэша с помощью PowerShell подчиняется тем же ограничениям и правилам, что и масштабирование кэша на портале Azure. Вы можете выполнить масштабирование до другой ценовой категории со следующими ограничениями.
> 
> * Перейти с более высокой ценовой категории на более низкую нельзя.
> * Ценовую категорию кэша **Премиум** нельзя изменить на категорию **Стандартный** или **Базовый**.
> * Ценовую категорию кэша **Стандартный** нельзя изменить на категорию **Базовый**.
> * Вы можете выполнить масштабирование кэша с уровня **Базовый** до уровня **Стандартный**, но вам не удастся одновременно с этим изменить размер кэша. Если требуется изменить размер, можно выполнить последующую операцию масштабирования до нужного размера.
> * Ценовую категорию кэша **Базовый** нельзя изменить сразу на уровень **Премиум**. Необходимо сначала перейти с категории **Базовый** на категорию **Стандартный**, а затем — с категории **Стандартный** на категорию **Премиум**.
> * Вам не удастся выполнить масштабирование с большего размера до размера **C0 (250 МБ)** .
> 
> Дополнительные сведения см. в статье [Масштабирование кэша Redis для Azure](cache-how-to-scale.md).
> 
> 

В следующем примере демонстрируется масштабирование кэша с именем `myCache` в кэш размером 2,5 ГБ. Обратите внимание, что команда будет работать в кэше уровня "Базовый" или "Стандартный".

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

После запуска этой команды будет возвращено состояние кэша (аналогично вызову `Get-AzureRmRedisCache`). Обратите внимание на то, что параметр `ProvisioningState` имеет значение `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

По завершении операции масштабирования значение параметра `ProvisioningState` изменяется на `Succeeded`. Если масштабирование необходимо повторить, например изменить уровень кэша с уровня "Базовый" на уровень "Стандартный", а затем скорректировать его размер, нужно дождаться завершения предыдущей операции. В противном случае возникнет ошибка следующего вида:

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Получение сведений о кэше Redis
Вы можете получить сведения об использовании кэша с помощью командлета [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Чтобы увидеть список доступных параметров свойства `Get-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы получить сведения обо всех кэшах в текущей подписке, выполните командлет `Get-AzureRmRedisCache` без параметров.

    Get-AzureRmRedisCache

Чтобы получить сведения обо всех кэшах в определенной группе ресурсов, выполните командлет `Get-AzureRmRedisCache` с параметром `ResourceGroupName`.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Чтобы получить сведения о конкретном кэше, выполните командлет `Get-AzureRmRedisCache` с параметром `Name`, содержащим имя кэша, и параметром `ResourceGroupName` с группой ресурсов, содержащей этот кэш.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Получение ключей доступа для кэша Redis
Для получения ключей доступа к кэшу вы можете использовать командлет [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Чтобы увидеть список доступных параметров свойства `Get-AzureRmRedisCacheKey`и их описания, выполните следующую команду:

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы получить ключи к кэшу, вызовите командлет `Get-AzureRmRedisCacheKey` и передайте имя кэша и имя группы ресурсов, содержащей этот кэш.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Повторное создание ключей доступа для кэша Redis
Для повторного создания ключей доступа к кэшу вы можете использовать командлет [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Чтобы увидеть список доступных параметров свойства `New-AzureRmRedisCacheKey`и их описания, выполните следующую команду:

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Чтобы повторно создать первичный или вторичный ключ кэша, вызовите командлет `New-AzureRmRedisCacheKey`, передайте имя и группу ресурсов и укажите значение `Primary` или `Secondary` для параметра `KeyType`. В приведенном ниже примере создается вторичный ключ доступа для кэша.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Удаление кэша Redis
Для удаления кэша Redis используйте командлет [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Чтобы увидеть список доступных параметров свойства `Remove-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

В приведенном ниже примере удаляется кэш с именем `myCache` .

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Импорт кэша Redis
Импортировать данные в кэш Redis для Azure можно с помощью командлета `Import-AzureRmRedisCache` .

> [!IMPORTANT]
> Функция импорта и экспорта доступна только для кэшей категории [Премиум](cache-premium-tier-intro.md). Дополнительные сведения об импорте и экспорте см. в статье [Импорт и экспорт данных в кэше Redis для Azure](cache-how-to-import-export-data.md).
> 
> 

Чтобы увидеть список доступных параметров свойства `Import-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Приведенная ниже команда импортирует данные из большого двоичного объекта, указанного в универсальном коде ресурса (URI) SAS, в кэш Redis для Azure.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Экспорт кэша Redis
Экспортировать данные из кэша Redis для Azure можно с помощью командлета `Export-AzureRmRedisCache` .

> [!IMPORTANT]
> Функция импорта и экспорта доступна только для кэшей категории [Премиум](cache-premium-tier-intro.md). Дополнительные сведения об импорте и экспорте см. в статье [Импорт и экспорт данных в кэше Redis для Azure](cache-how-to-import-export-data.md).
> 
> 

Чтобы увидеть список доступных параметров свойства `Export-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Приведенная ниже команда экспортирует данные из экземпляра кэша Redis для Azure в контейнер, указанный универсальным кодом ресурса (URI) SAS.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Перезапуск кэша Redis
Перезапустить экземпляр кэша Redis для Azure можно с помощью командлета `Reset-AzureRmRedisCache` .

> [!IMPORTANT]
> Функция перезагрузки доступна только для кэшей [уровня "Премиум"](cache-premium-tier-intro.md). Дополнительные сведения о перезапуске кэша см. в разделе [Reboot](cache-administration.md#reboot) статьи "Администрирование кэша Redis для Azure".
> 
> 

Чтобы увидеть список доступных параметров свойства `Reset-AzureRmRedisCache`и их описания, выполните следующую команду:

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Перезапустить оба узла указанного кэша можно с помощью такой команды:

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об использовании Windows PowerShell с Azure см. в следующих ресурсах:

* [Документация по командлету кэша Redis для Azure на MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765): узнайте, как использовать командлеты в модуле диспетчера ресурсов Azure.
* [Развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): узнайте, как создавать группы ресурсов и управлять ими на портале Azure.
* [Блог Azure](http://blogs.msdn.com/windowsazure): узнайте о новых возможностях в Azure.
* [Блог Windows PowerShell](http://blogs.msdn.com/powershell): узнайте о новых возможностях в Windows PowerShell.
* [Блог "Hey, Scripting Блог](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества Windows PowerShell.

