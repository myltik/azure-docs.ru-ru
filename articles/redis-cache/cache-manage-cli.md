<properties 
	pageTitle="Как создать кэш Redis для Azure и управлять им с помощью интерфейса командной строки Azure (Azure CLI)" 
	description="Узнайте, как установить Azure CLI на любой платформе, как его использовать для подключения к учетной записи Azure и как с его помощью создать кэш Redis и управлять им." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="sdanie"/>

# Как создать кэш Redis для Azure и управлять им с помощью интерфейса командной строки Azure (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Интерфейс CLI Azure позволяет управлять инфраструктурой Azure с любой платформы. В этой статье показано, как создавать экземпляры кэша Redis для Azure и управлять ими с помощью Azure CLI.

## Предварительные требования

Для создания экземпляров кэша Redis для Azure и управления ими с помощью Azure CLI необходимо выполнить следующие действия.

-	Необходимо иметь учетную запись Azure. Если ее нет, можно создать [бесплатную пробную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за пару минут.
-	[Установка Azure CLI](../xplat-cli-install.md).
-	Подключите установленный интерфейс Azure CLI к личной либо рабочей или учебной учетной записи Azure, затем выполните вход из Azure CLI с помощью команды `azure login`. Чтобы разобраться в различиях и сделать правильный выбор, изучите статью [Подключение к подписке Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md).
-	Перед выполнением любой из указанных ниже команд переключите Azure CLI в режим диспетчера ресурсов, выполнив команду `azure config mode arm`. Дополнительные сведения см. в подразделе [Настройка режима диспетчера ресурсов Azure](../virtual-machines/xplat-cli-azure-resource-manager.md#setting-the-azure-resource-manager-mode).

## Свойства кэша Redis

При создании и обновлении экземпляров кэша Redis используются следующие свойства.

| Свойство | Switch | Описание |
|------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n, --name | Имя кэша Redis. |
| resource group | -g, --resource-group | Имя группы ресурсов. |
| location | -l, --location | Расположение для создания кэша. |
| size | -z, --size | Размер кэша Redis. Допустимые значения: [C0, C1, C2, C3, C4, C5, C6]. |
| sku | -x, --sku | Номер SKU Redis. Должен иметь одно из этих значений: [Basic, Standard]. |
| MaxMemoryPolicy | -m, --max-memory-policy | Свойство MaxMemoryPolicy кэша Redis. Допустимые значения: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]. |
| EnableNonSslPort | -e, --enable-non-ssl-port | Свойство EnableNonSslPort кэша Redis. Добавьте этот флаг, если хотите включить для кэша порт без SSL. |
| подписку | -s, --subscription | Идентификатор подписки. |
| key type | -t, --key-type | Тип обновляемого ключа. Допустимые значения: [Primary, Secondary]. |

## Просмотр всех команд кэша Redis

Для просмотра всех команд кэша Redis и их параметров используйте команду `azure rediscache -h`.

	C:\>azure rediscache -h
	help:    Commands to manage your Azure Redis Cache(s)
	help:
	help:    Create a Redis Cache
	help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Delete an existing Redis Cache
	help:      rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    List all Redis Caches within your Subscription or Resource Group
	help:      rediscache list [options]
	help:
	help:    Show properties of an existing Redis Cache
	help:      rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Change settings of an existing Redis Cache
	help:      rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Renew the authentication key for an existing Redis Cache
	help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help  output usage information
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Создание кэша Redis

Чтобы создать кэш Redis, используйте следующую команду:

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache create -h`.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -l, --location <location>                    Location to create cache.
	help:      -z, --size <size>                            Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6]
	help:      -x, --sku <sku>                              Redis SKU. Should be one of : [Basic, Standard]
	help:      -m, --max-memory-policy <max-memory-policy>  MaxMemoryPolicy property of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -e, --enable-non-ssl-port                    EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -s, --subscription <id>                      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Удаление существующего кэша Redis

Чтобы удалить кэш Redis, используйте следующую команду:

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache delete -h`.

	C:\>azure rediscache delete -h
	help:    Delete an existing Redis Cache
	help:
	help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Вывод списка всех кэшей Redis в подписке или группе ресурсов

Чтобы вывести список всех кэшей Redis в подписке или группе ресурсов, используйте следующую команду:

	azure rediscache list [options]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache list -h`.

	C:\>azure rediscache list -h
	help:    List all Redis Caches within your Subscription or Resource Group
	help:
	help:    Usage: rediscache list [options]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Отображение свойств существующего кэша Redis

Чтобы отобразить свойства существующего кэша Redis, используйте следующую команду:

	azure rediscache show [--name <name> --resource-group <resource-group>]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache show -h`.

	C:\>azure rediscache show -h
	help:    Show properties of an existing Redis Cache
	help:
	help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Изменение параметров существующего кэша Redis

Чтобы изменить параметры существующего кэша Redis, используйте следующую команду:

	azure rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache set -h`.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -m, --max-memory-policy <max-memory-policy>  Max Memory Policy of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -s, --subscription <subscription>            the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Обновление ключа аутентификации для существующего кэша Redis

Чтобы обновить ключ аутентификации для существующего кэша Redis, используйте следующую команду:

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Укажите `Primary` или `Secondary` для `key-type`.

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache renew-key -h`.

	C:\>azure rediscache renew-key -h
	help:    Renew the authentication key for an existing Redis Cache
	help:
	help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
	help:      -t, --key-type <key-type>              type of key to renew
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Вывод первичного и вторичного ключей существующего кэша Redis

Чтобы вывести первичный и вторичный ключи существующего кэша Redis, используйте следующую команду:

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache list-keys -h`.

	C:\>azure rediscache list-keys -h
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:
	help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<!---HONumber=AcomDC_1203_2015-->