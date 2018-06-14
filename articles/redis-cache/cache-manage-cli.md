---
title: Управление кэшем Redis для Azure с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как установить Azure CLI на любой платформе, как его использовать для подключения к учетной записи Azure и как с его помощью создать кэш Redis и управлять им.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: fdb0989af2215166b69f10474a0d22aab7b4d593
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911284"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Как создать кэш Redis для Azure и управлять им с помощью интерфейса командной строки Azure (Azure CLI)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Интерфейс командной строки Azure](cache-manage-cli.md)
>
>

Интерфейс CLI Azure позволяет управлять инфраструктурой Azure с любой платформы. В этой статье показано, как создавать экземпляры кэша Redis для Azure и управлять ими с помощью Azure CLI.

> [!NOTE]
> Эта статья относится к предыдущей версии Azure CLI. Последние примеры сценариев Azure CLI 2.0 см. в статье [Примеры Azure CLI для виртуальных машин Windows](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
Для создания экземпляров кэша Redis для Azure и управления ими с помощью Azure CLI необходимо выполнить следующие действия.

* Необходимо иметь учетную запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.
* [Установка Azure CLI](../cli-install-nodejs.md).
* Подключите установленный интерфейс Azure CLI к личной либо рабочей или учебной учетной записи Azure, затем выполните вход из Azure CLI с помощью команды `azure login` . Чтобы разобраться в различиях и сделать правильный выбор, изучите статью [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](/cli/azure/authenticate-azure-cli).
* Перед выполнением любой из указанных ниже команд переключите Azure CLI в режим диспетчера ресурсов, выполнив команду `azure config mode arm`. Дополнительные сведения см. в разделе [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Свойства кэша Redis
При создании и обновлении экземпляров кэша Redis используются следующие свойства.

| Свойство | Switch | ОПИСАНИЕ |
| --- | --- | --- |
| name |-n, --name |Имя кэша Redis. |
| resource group |-g, --resource-group |Имя группы ресурсов. |
| location |-l, --location |Расположение для создания кэша. |
| size |-z, --size |Размер кэша Redis. Допустимые значения: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Номер SKU Redis. Должен иметь одно из этих значений: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Свойство EnableNonSslPort кэша Redis. Добавьте этот флаг, если хотите включить для кэша порт без SSL. |
| Конфигурация Redis |-c, --redis-configuration |Конфигурация Redis. Введите строку ключей и значений конфигурации в формате JSON. Формат:"{"":"","":""}" |
| Конфигурация Redis |-f, --redis-configuration-file |Конфигурация Redis. Введите путь к файлу, содержащему ключи и значения. Формат записи в файле: {"":"","":""} |
| Число сегментов |-r, --shard-count |Число сегментов, которые будут созданы при создании кэша уровня "Премиум" с включенной кластеризацией. |
| Виртуальная сеть |-v, --virtual-network |При размещении кэша в виртуальной сети определяет точный идентификатор ресурса ARM виртуальной сети, в которой будет развернут кэш Redis. Пример формата: /subscriptions/{идентификатор_подписки}/resourceGroups/{имя_группы_ресурсов}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| key type |-t, --key-type |Тип обновляемого ключа. Допустимые значения: [Primary, Secondary]. |
| StaticIP |-p, --static-ip <static-ip> |При размещении кэша в виртуальной сети определяет уникальный IP-адрес подсети для кэша. Если IP-адрес не указан, он автоматически выбирается из подсети. |
| Подсеть |t, --subnet <subnet> |При размещении кэша в виртуальной сети определяет имя подсети, в которой будет развернут кэш. |
| Виртуальная сеть |-v, --virtual-network <virtual-network> |При размещении кэша в виртуальной сети определяет точный идентификатор ресурса ARM виртуальной сети, в которой будет развернут кэш Redis. Пример формата: /subscriptions/{идентификатор_подписки}/resourceGroups/{имя_группы_ресурсов}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Подписка |-s, --subscription |Идентификатор подписки. |

## <a name="see-all-redis-cache-commands"></a>Просмотр всех команд кэша Redis
Для просмотра всех команд кэша Redis и их параметров используйте команду `azure rediscache -h` .

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
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
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

## <a name="create-a-redis-cache"></a>Создание кэша Redis
Чтобы создать кэш Redis, используйте следующую команду:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache create -h` .

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Удаление существующего кэша Redis
Чтобы удалить кэш Redis, используйте следующую команду:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache delete -h` .

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

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Вывод списка всех кэшей Redis в подписке или группе ресурсов
Чтобы вывести список всех кэшей Redis в подписке или группе ресурсов, используйте следующую команду:

    azure rediscache list [options]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache list -h` .

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

## <a name="show-properties-of-an-existing-redis-cache"></a>Отображение свойств существующего кэша Redis
Чтобы отобразить свойства существующего кэша Redis, используйте следующую команду:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache show -h` .

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

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Изменение параметров существующего кэша Redis
Чтобы изменить параметры существующего кэша Redis, используйте следующую команду:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache set -h` .

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Обновление ключа аутентификации для существующего кэша Redis
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
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Вывод первичного и вторичного ключей существующего кэша Redis
Чтобы вывести первичный и вторичный ключи существующего кэша Redis, используйте следующую команду:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Чтобы получить дополнительные сведения об этой команде, выполните команду `azure rediscache list-keys -h` .

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
