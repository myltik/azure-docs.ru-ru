---
title: Создание, изменение или удаление группы безопасности сети Azure | Документация Майкрософт
description: Узнайте, как создать, изменить и удалить группу безопасности сети.
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
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 0e9a66cc52c25bf4d38fd27050a92196227a698c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
---
# <a name="create-change-or-delete-a-network-security-group"></a>Создание, изменение и удаление группы безопасности сети

Правила безопасности в группах безопасности сети позволяют фильтровать различный сетевой трафик, который может проходить из подсетей виртуальной сети и сетевых интерфейсов или в них. Если вы не знакомы с группами безопасности сети, прочитайте раздел [Безопасность сети](security-overview.md), чтобы получить дополнительные сведения о них, и выполните задания раздела [Руководство. Фильтрация сетевого трафика с помощью группы безопасности сети и PowerShell](tutorial-filter-network-traffic.md), чтобы получить опыт работы с группами безопасности сети.

## <a name="before-you-begin"></a>Перед началом работы

Перед выполнением действий, описанных в любом разделе этой статьи, выполните следующие задачи.

- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной учетной записи](https://azure.microsoft.com/free).
- При использовании портала перейдите по адресу https://portal.azure.com и войдите с использованием своей учетной записи Azure.
- При использовании команд PowerShell для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/powershell) или в PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Для работы с этим руководством требуется модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.
- При использовании команд интерфейса командной строки Azure (CLI) для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/bash) или в интерфейсе командной строки на своем компьютере. Для этого руководства требуется Azure CLI 2.0.28 или более поздней версии. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). Если Azure CLI запущен локально, необходимо также выполнить командлет `az login`, чтобы создать подключение к Azure.

## <a name="work-with-network-security-groups"></a>Работа с группами безопасности сети

Можно создать группу безопасности сети, [просмотреть все](#view-all-network-security-groups) группы, [просмотреть сведения о](#view-details-of-a-network-security-group) группе, а также [изменить](#change-a-network-security-group) или [удалить](#delete-a-network-security-group) группу безопасности сети. Вы также можете [создать или удалить связь](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) группы безопасности сети с сетевым интерфейсом или подсетью.

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Количество групп безопасности сети, которые можно создать в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Выберите **+ Create a resource** (+ Создать ресурс) в левом верхнем углу портала.
2. Выберите **Сеть**, а затем — **Группа безопасности сети**.
3. Введите **имя** группы безопасности сети, выберите свою **подписку**, создайте **группу ресурсов** или выберите имеющуюся, выберите **расположение**, а затем щелкните **Создать**. 

**Команды**

- Azure CLI: [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Просмотр всех групп безопасности сети

В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его. В списке будут указаны группы безопасности сети, имеющиеся в вашей подписке.

**Команды**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Просмотр сведений о группе безопасности сети

1. В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
2. Выберите из списка группу безопасности сети, о которой нужно просмотреть сведения. В разделе **Параметры** можно просмотреть **правила безопасности для входящего трафика** и **правила безопасности для исходящего трафика**, **сетевые интерфейсы** и **подсети**, связанные с группой безопасности сети. Можно также включить или отключить **журналы диагностики** и просмотреть **действующие правила безопасности**. Чтобы узнать больше, ознакомьтесь с разделами [Аналитика журналов для групп безопасности сети](virtual-network-nsg-manage-log.md) и [Устранение проблем с группами безопасности сети на портале Azure](virtual-network-nsg-troubleshoot-portal.md).
3. Дополнительные сведения о перечисленных общих параметрах Azure доступны в следующих статьях:
    *   [Журнал действий](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Управление доступом (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Теги](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Блокировки](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Сценарий автоматизации](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Команды**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Изменение группы безопасности сети

1. В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
2. Выберите группу безопасности сети для изменения. Наиболее распространенные виды изменений — [добавление](#create-a-security-rule) или [удаление](#delete-a-security-rule) правила безопасности и [создание или удаление связи группы безопасности сети с подсетью или сетевым интерфейсом](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Команды**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Создайте или удалите связь группы безопасности сети с подсетью или сетевым интерфейсом.

Чтобы создать или удалить связь группы безопасности сети с сетевым интерфейсом, ознакомьтесь с разделом [Создание, изменение и удаление группы безопасности сети](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Чтобы создать или удалить связь группы безопасности сети с подсетью, ознакомьтесь с разделом [Изменение параметров подсети](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Удаление группы безопасности сети

Если группа безопасности сети связана с какой-либо подсетью или сетевыми интерфейсами, ее невозможно удалить. [Удалите связи](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) группы безопасности сети со всеми связанными с ней подсетями и сетевыми интерфейсами, прежде чем пытаться удалить эту группу.

1. В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
2. Выберите из списка группу безопасности сети для удаления.
3. Выберите **Удалить**, а затем — **Да**.

**Команды**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Работа с правилами безопасности

Группа безопасности сети содержит ноль или больше правил безопасности. Можно создать правило безопасности, [просмотреть все](#view-all-security-rules) правила, [просмотреть сведения о](#view-details-of-a-security-rule) правиле, а также [изменить](#change-a-security-rule) или [удалить](#delete-a-security-rule) правило безопасности.

### <a name="create-a-security-rule"></a>Создание правила безопасности

Количество правил на группу безопасности сети, которые можно создать в одном расположении и одной подписке Azure, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
2. Выберите из списка группу безопасности сети, в которую нужно добавить правило безопасности.
3. В разделе **Параметры** выберите **Правила безопасности для входящего трафика**. Будет отображено несколько существующих правил. Некоторые из них добавлены не вами. При создании группы безопасности сети в ней создается несколько правил безопасности по умолчанию. Ознакомьтесь с дополнительными сведениями о [правилах безопасности по умолчанию](security-overview.md#default-security-rules).  Правила безопасности по умолчанию невозможно удалить, но их можно переопределить с помощью правил с более высоким приоритетом.
4. <a name = "security-rule-settings"></a>Выберите **+ Добавить**.  Выберите или добавьте значения перечисленных ниже параметров, а затем щелкните **ОК**.
    
    |Параметр  |Значение  |Сведения  |
    |---------|---------|---------|
    |Источник     | Выберите **Любой**, **IP-адреса** или **Service Tag** (Тег службы).        | При выборе параметра **IP-адреса** необходимо указать значение поля **Диапазоны исходных IP-адресов или CIDR**. Можно указать одно значение или список разделенных запятыми значений. Пример нескольких значений: 10.0.0.0/16, 192.188.1.1. Число значений, которые можно указать, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). При выборе параметра **Service Tag** (Тег службы) необходимо выбрать один тег службы. Тег службы — это предопределенный идентификатор категории IP-адресов. Дополнительные сведения о доступных тегах служб и том, что они представляют, приведены в разделе [Теги служб](security-overview.md#service-tags).        |
    |Диапазоны исходных портов     | Укажите один порт, например 80, диапазон портов, например 1024–65535, или список разделенных запятыми отдельных портов либо диапазонов портов, например 80, 1024–65535. Введите звездочку, чтобы разрешить передачу трафика через любой порт. | Порты и диапазоны указывают, передача трафика через какие порты разрешена или запрещена правилом. Число портов, которые можно указать, ограничено. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).  |
    |Место назначения     | Выберите **Любой**, **IP-адреса** или **Виртуальная сеть**.        | При выборе параметра **IP-адреса** необходимо указать значение поля **Диапазоны IP-адресов назначения или CIDR**. Как и в полях **Источник** и **Диапазоны исходных IP-адресов или CIDR**, можно указать один или несколько адресов или их диапазонов, однако число указываемых значений ограничено. Выбор параметра **Виртуальная сеть**, который является тегом службы, означает, что будет разрешен трафик для всех IP-адресов в диапазоне адресов виртуальной сети.        |
    |Диапазоны портов назначения     | Укажите одно значение или список разделенных запятыми значений. | Как и в поле **Диапазоны исходных портов**, можно указать один или несколько портов и их диапазонов, однако число указываемых значений ограничено. |
    |Протокол     | Выберите **Любой**, **TCP** или **UDP**.        |         |
    |Действие     | Выберите **Разрешить** или **Запретить**.        |         |
    |Приоритет     | Введите значение в диапазоне 100–4096, уникальное для всех правил безопасности в группе безопасности сети. |Правила обрабатываются в порядке приоритета. Чем меньше число, тем выше приоритет. Рекомендуется оставить промежуток между номерами приоритетов при создании правил, например 100, 200, 300. Это упростит добавление правил в будущем, когда вам может потребоваться правило с более высоким или низким приоритетом, чем у существующих правил.         |
    |ИМЯ     | Уникальное имя правила в пределах группы безопасности сети.        |  Оно может содержать до 80 знаков. Это имя должно начинаться с буквы или цифры, оканчиваться буквой, цифрой или символом подчеркивания и может содержать только буквы, цифры, символы подчеркивания, точки и дефисы.       |
    |ОПИСАНИЕ     | Необязательное описание.        |         |

    Невозможно указать [группу безопасности приложений](#work-with-application-security-groups) для параметра **Источник** или **Назначение** с помощью портала. Однако это можно сделать с помощью PowerShell или Azure CLI. Параметры **правил безопасности для исходящего трафика** аналогичны, поэтому они не представлены отдельно.

**Команды**

- Azure CLI: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Просмотр всех правил безопасности

Группа безопасности сети содержит ноль или больше правил. Узнать больше о сведениях при просмотре правил можно из статьи [Безопасность сети](security-overview.md).

1. В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
2. Выберите из списка группу безопасности сети, правила которой нужно просмотреть.
3. В разделе **Параметры** выберите **Правила безопасности для входящего трафика** или **Правила безопасности для исходящего трафика**.

Список содержит все созданные вами правила и [правила безопасности по умолчанию](security-overview.md#default-security-rules) группы безопасности сети.

**Команды**

- Azure CLI: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Просмотр сведений о правиле безопасности

1. В поле поиска в верхней части портала введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
2. Выберите из списка группу безопасности сети, сведения о правиле безопасности которой нужно просмотреть.
3. В разделе **Параметры** выберите **Правила безопасности для входящего трафика** или **Правила безопасности для исходящего трафика**.
4. Выберите правило, сведения о котором нужно просмотреть. Подробное описание всех параметров приведено в разделе [Правила безопасности по умолчанию](#security-rule-settings).

**Команды**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Изменение правила безопасности

1. Выполните инструкции из раздела [Просмотр сведений о правиле безопасности](#view-details-of-a-security-rule).
2. Измените необходимые параметры, а затем щелкните **Сохранить**. Подробное описание всех параметров приведено в разделе [Правила безопасности по умолчанию](#security-rule-settings).

**Команды**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Удаление правила безопасности

1. Выполните инструкции из раздела [Просмотр сведений о правиле безопасности](#view-details-of-a-security-rule).
2. Выберите **Удалить**, а затем — **Да**.

**Команды**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>Работа с группами безопасности приложений

Группа безопасности приложений содержит ноль или более сетевых интерфейсов. Дополнительные сведения см. в разделе [Группы безопасности приложений](security-overview.md#application-security-groups). С группами безопасности приложений невозможно работать на портале, но можно использовать PowerShell или Azure CLI. Все сетевые интерфейсы внутри группы безопасности приложений должны находиться в одной виртуальной сети. Первый сетевой интерфейс, добавленный в группу безопасности приложений, определяет, в какой виртуальной сети должны будут находиться все последующие сетевые интерфейсы. Чтобы узнать больше о добавлении сетевого интерфейса в группу безопасности приложений, ознакомьтесь с разделом [Создание, изменение или удаление сетевых интерфейсов](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Создание группы безопасности приложений

- Azure CLI: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Просмотр всех групп безопасности приложений

- Azure CLI: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Просмотр сведений об определенной группе безопасности приложений

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Изменение группы безопасности приложений

Хотя для существующей группы безопасности приложений можно изменить некоторые параметры, такие как теги и разрешения, невозможно изменить ее имя или расположение.

- Azure CLI: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: соответствующий командлет PowerShell отсутствует.

### <a name="delete-an-application-security-group"></a>Удаление группы безопасности приложений

Невозможно удалить группу безопасности приложений, если она содержит какие-либо сетевые интерфейсы. Необходимо удалить все сетевые интерфейсы из группы безопасности приложений, изменив параметры сетевых интерфейсов или удалив эти сетевые интерфейсы. Дополнительные сведения см. в разделах [Создание, изменение или удаление сетевых интерфейсов](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) или [Удаление сетевого интерфейса](virtual-network-network-interface.md#delete-a-network-interface).

**Команды**

- Azure CLI: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Разрешения

Для выполнения задач с группами безопасности сети, правилами безопасности и группами безопасности приложений вашей учетной записи должна быть назначена роль [участника сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательская](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) роль, которой назначены соответствующие разрешения, перечисленные в следующей таблице.

|Операция                                                       |   Имя операции                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/ruleTables/read                              |   Получение группы безопасности сети.                              |
|Microsoft.Network/ruleTables/write                             |   Создание или обновление группы безопасности сети.                 |
|Microsoft.Network/ruleTables/delete                            |   Удаление группы безопасности сети.                           |
|Microsoft.Network/ruleTables/join/action                       |   Присоединение группы безопасности сети.                             |
|Microsoft.Network/ruleTables/rules/read                       |   Получение правила.                                    |
|Microsoft.Network/ruleTables/rules/write                      |   Создание или обновление правила.                       |
|Microsoft.Network/ruleTables/rules/delete                     |   Удаление правила.                                 |
|Microsoft.Network/networkInterfaces/effectiveruleTable/action  |   Получение сетевой группы безопасности, связанной с сетевым интерфейсом.  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Получить следующий прыжок из виртуальной машины                  |

Операция *присоединения группы безопасности сети* требуется для привязывания группы безопасности сети к подсети.
