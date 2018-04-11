---
title: Создание, изменение или удаление таблицы маршрутов Azure | Документация Майкрософт
description: Сведения о создании, изменении и удалении таблицы маршрутов.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: a7e45d6bccfd8113157eba63d311b6609bf35aaa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Создание, изменение и удаление таблицы маршрутов

Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. Если нужно изменить какой-либо из стандартных маршрутов Azure, это можно сделать с помощью создания таблицы маршрутов. Если вы не знакомы с маршрутизацией Azure, мы рекомендуем ознакомиться со статьей [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md), а также с руководством [Создание определяемого пользователем маршрута с помощью портала Azure](tutorial-create-route-table-portal.md), а затем приступить к выполнению шагов, описанных в этой статье.

## <a name="before-you-begin"></a>Перед началом работы

Перед выполнением действий, описанных в любом разделе этой статьи, выполните следующие задачи.

- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной учетной записи](https://azure.microsoft.com/free).
- При использовании портала перейдите по адресу https://portal.azure.com и войдите с использованием своей учетной записи Azure.
- При использовании команд PowerShell для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/powershell) или в PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 5.2.0. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.
- При использовании команд интерфейса командной строки Azure (CLI) для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/bash) или в интерфейсе командной строки на своем компьютере. Для этого руководства требуется Azure CLI версии не ниже 2.0.26. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). Если Azure CLI запущен локально, необходимо также выполнить командлет `az login`, чтобы создать подключение к Azure.

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

Количество таблиц маршрутов, которые можно создать в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Выберите **+ Create a resource** (+ Создать ресурс) в левом верхнем углу портала.
2. Выберите **Сетевые подключения**, а затем — **Таблица маршрутов**.
3. Введите **имя** таблицы маршрутов, выберите свою **подписку**, создайте **группу ресурсов** или выберите имеющуюся, щелкните **расположение**, а затем выберите **Создать**. Параметр **Отключить распространение маршрутов BGP** не позволяет локальным маршрутам распространяться через BGP в сетевые интерфейсы в любой подсети, с которой связана таблица маршрутов. Если ваша виртуальная сеть не подключена к шлюзу сети Azure (VPN или ExpressRoute), оставьте для параметра значение *Отключено*.

**Команды**

- Azure CLI: [az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Просмотр таблиц маршрутов

В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их. В списке будут содержаться таблицы маршрутов, имеющиеся в вашей подписке.

**Команды**

- Azure CLI: [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Просмотр сведений о таблице маршрутов

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите в списке таблицу маршрутов, о которой нужно просмотреть сведения. В разделе **параметров** можно просмотреть **маршруты** в таблице маршрутов и **подсети**, с которыми связана таблица маршрутов.
3. Дополнительные сведения об общих параметрах Azure см. ниже:
    *   [Журнал действий](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Управление доступом (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Теги](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Блокировки](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Сценарий автоматизации](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Команды**

- Azure CLI: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Изменение таблицы маршрутов

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите таблицу маршрутов, которую нужно изменить. Наиболее распространенными видами изменений являются [добавление](#create-a-route) и [удаление](#delete-a-route) маршрутов, [связывание](#associate-a-route-table-to-a-subnet) или [отмена связи](#dissociate-a-route-table-from-a-subnet) таблиц маршрутов с подсетями.

**Команды**

- Azure CLI: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

Подсеть может иметь не более одной таблицы маршрутов, связанной с ней. Таблица маршрутов может быть не связана с подсетями или связана с несколькими. Так как таблицы маршрутов не связаны с виртуальными сетями, вам необходимо связать таблицу маршрутов с каждой соответствующей подсетью. Весь трафик, исходящий из подсети, направляется на основе маршрутов, созданных в таблицах маршрутов, [стандартных маршрутах](virtual-networks-udr-overview.md#default) и маршрутах, распространяемых из локальной сети, если виртуальная сеть подключена к шлюзу виртуальной сети Azure (ExpressRoute или VPN при использовании BGP со шлюзом VPN). Вы можете связать таблицы маршрутов только с теми подсетями в виртуальной сети, которые находятся в том же расположении и той же подписке Azure, что и таблица маршрутов.

1. В поле поиска в верхней части портала введите *виртуальные сети*. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
2. В списке виртуальных сетей выберите сеть, содержащую подсеть, с которой нужно связать таблицу маршрутов.
3. В разделе **параметров** выберите **Подсети**.
4. Выберите подсеть, с которой нужно связать таблицу маршрутов.
5. Выберите **таблицу маршрутов**, с которой нужно связать подсеть, а затем нажмите кнопку **Сохранить**.

**Команды**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Отмена связи таблицы маршрутов с подсетью

При отмене связи таблицы маршрутов с подсетью Azure направляет трафик на основе своих [стандартных маршрутов](virtual-networks-udr-overview.md#default).

1. В поле поиска в верхней части портала введите *виртуальные сети*. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
2. Выберите виртуальную сеть, содержащую подсеть, с которой нужно отменить связь.
3. В разделе **параметров** выберите **Подсети**.
4. Выберите подсеть, с которой нужно отменить связь.
5. Выберите **таблицу маршрутов**, затем — **Нет** и нажмите кнопку **Сохранить**.

**Команды**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Удаление таблицы маршрутов

Таблицу маршрутов нельзя удалить, если она связана с какими-либо подсетями. [Отмените связь](#dissociate-a-route-table-from-a-subnet) таблицы маршрутов со всеми подсетями, а затем попытайтесь удалить ее.

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите **...** в правой области таблицы маршрутов, которую нужно удалить.
3. Выберите **Удалить**, а затем — **Да**.

**Команды**

- Azure CLI: [az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Создание маршрута

Количество маршрутов, которые можно создать для одной таблицы маршрутов в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите в списке таблицу маршрутов, в которую нужно добавить маршрут.
3. В разделе **параметров** выберите **Маршруты**.
4. Щелкните **+ Добавить**.
5. Введите уникальное **имя** маршрута в таблице маршрутов.
6. Введите **префикс адреса** в нотации CIDR, куда нужно направить трафик. Префикс не может повторяться в нескольких маршрутах в таблице маршрутов, хотя может содержаться в другом префиксе. Например, если вы определили 10.0.0.0/16 как префикс в одном маршруте, вы по-прежнему можете определить другой маршрут с префиксом адреса 10.0.0.0/24. Azure выбирает маршрут трафика на основе совпадения с самым длинным префиксом. Дополнительные сведения о том, как Azure выбирает маршруты, см. в разделе [Как Azure выбирает маршрут](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Выберите **Тип следующего прыжка**. Подробное описание всех типов следующих прыжков см. в разделе [Как Azure выбирает маршрут](virtual-networks-udr-overview.md).
8. Введите IP-адрес для **адреса следующего прыжка**. Ввести адрес можно, только если для параметра **Тип следующего прыжка** выбран *Виртуальный модуль*.
9. Нажмите кнопку **ОК**. 

**Команды**

- Azure CLI: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Просмотр маршрутов

Таблица маршрутов содержит несколько маршрутов или не содержит их вообще. Узнать больше о сведениях при просмотре маршрутов можно с помощью статьи [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md).

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите в списке таблицу маршрутов, для которой нужно просмотреть маршруты.
3. В разделе **параметров** выберите **Маршруты**.

**Команды**

- Azure CLI: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Просмотр сведений о маршруте

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите таблицу маршрутов, в которой нужно просмотреть сведения о маршруте.
3. Выберите **Маршруты**.
4. Выберите маршрут, сведения о котором нужно просмотреть.

**Команды**

- Azure CLI: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Изменение маршрута

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите таблицу маршрутов, для которой нужно изменить маршрут.
3. Выберите **Маршруты**.
4. Выберите маршрут, который нужно изменить.
5. Измените имеющиеся параметры на новые, а затем нажмите кнопку **Сохранить**.

**Команды**

- Azure CLI: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Удаление маршрута

1. В поле поиска в верхней части портала введите *таблицы маршрутов*. Когда **таблицы маршрутов** появятся в результатах поиска, выберите их.
2. Выберите таблицу маршрутов, для которой нужно удалить маршрут.
3. Выберите **Маршруты**.
4. В списке маршрутов выберите **...** в правой области маршрута, который нужно удалить.
5. Выберите **Удалить**, а затем — **Да**.

**Команды**

- Azure CLI: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Просмотр действующих маршрутов

Действующие маршруты для каждого сетевого интерфейса, подключенного к виртуальной машине, представляют собой комбинацию созданных таблиц маршрутов, стандартных маршрутов Azure и всех маршрутов, распространенных из локальных сетей с помощью BGP через шлюз виртуальной сети Azure. Понимание принципа работы действующих маршрутов для сетевого интерфейса необходимо для устранения неполадок, связанных с маршрутизацией. Вы можете просмотреть действующие маршруты для любого сетевого интерфейса, подключенного к работающей виртуальной машине.

1. В поле поиска в верхней области портала введите имя виртуальной машины, для которой нужно просмотреть действующие маршруты. Если вы не знаете ее имя, введите в поле поиска *виртуальные машины*. Когда **виртуальные машины** появятся в результатах поиска, щелкните результаты и выберите в списке виртуальную машину.
2. В разделе **параметров** выберите **Сеть**.
3. Выберите имя сетевого интерфейса.
4. В разделе **Поддержка и устранение неполадок** выберите **Фактические маршруты**.
5. Просмотрите список действующих маршрутов, чтобы определить, есть ли соответствующий маршрут, по которому нужно направить трафик. Дополнительные сведения о типах следующих прыжков в этом списке см. в статье [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md).

**Команды**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Проверка маршрутизации между двумя конечными точками

Вы можете определить тип следующего прыжка между виртуальной машиной и IP-адресом другого ресурса Azure, локальным ресурсом или ресурсом в Интернете. Определение маршрутизации Azure эффективно при устранении неполадок, связанных с маршрутизацией. Для проверки маршрутизации у вас должен быть установлен Наблюдатель за сетями. Если у вас его нет, создайте его, выполнив задачи, описанные в статье [Создание экземпляра Наблюдателя за сетями Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. В поле поиска в верхней области портала введите *наблюдатель за сетями*. Когда в результатах поиска появится **Наблюдатель за сетями**, выберите его.
2. В разделе **Средства диагностики сети** выберите **Следующий прыжок**.
3. Выберите свою **подписку** и **группу ресурсов** исходной виртуальной машины, исходящий трафик которой нужно проверить.
4. Выберите **виртуальную машину**, **сетевой интерфейс**, подключенный к виртуальной машине, и **исходный IP-адрес**, назначенный сетевому интерфейсу, исходящий трафик которого нужно проверить.
5. Введите **конечный IP-адрес**, куда направляется трафик, который нужно проверить.
6. Выберите **Следующий прыжок**.
7. После короткого периода ожидания вернутся сведения, из которых вы узнаете тип следующего прыжка и идентификатор маршрута, который направляет трафик. Дополнительную информацию о типах следующих прыжков, возвращенных в сведениях, см. в статье [Маршрутизация трафика в виртуальной сети](virtual-networks-udr-overview.md).

**Команды**

- Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 
 
## <a name="permissions"></a>Разрешения

Для выполнения задач в таблицах маршрутов и маршрутах учетной записи должна быть назначена одна из ролей: [участника сети](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользователя](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json), в которой назначены соответствующие разрешения, перечисленные в следующей таблице.

|Операция                                                       |   Имя операции                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/routeTables/read                              |   Получить таблицу маршрутизации                              |
|Microsoft.Network/routeTables/write                             |   Создать или обновить таблицу маршрутизации                 |
|Microsoft.Network/routeTables/delete                            |   Удалить таблицу маршрутизации                           |
|Microsoft.Network/routeTables/join/action                       |   Присоединить таблицу маршрутизации                             |
|Microsoft.Network/routeTables/routes/read                       |   Получить маршрут                                    |
|Microsoft.Network/routeTables/routes/write                      |   Создать или обновить маршрут                       |
|Microsoft.Network/routeTables/routes/delete                     |   Удалить маршрут                                 |
|Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Получить таблицу эффективных маршрутов сетевого интерфейса  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Получить следующий прыжок из виртуальной машины                  |

Операция *присоединения таблицы маршрутизации* нужна, чтобы связать таблицу маршрутов с подсетью.
