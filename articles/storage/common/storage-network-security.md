---
title: Настройка брандмауэров службы хранилища Azure и виртуальных сетей | Документация Майкрософт
description: Настройте многоуровневую сетевую безопасность для своей учетной записи.
services: storage
documentationcenter: ''
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 52d904e7a7e8e5d520d2abd799ef0ae7e99b9894
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192882"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Настройка брандмауэров службы хранилища Azure и виртуальных сетей
Служба хранилища Azure предоставляет модель многоуровневой безопасности, которая обеспечивает безопасность учетных записей хранения, открывая к ним доступ только по определенным разрешенным сетям.  При настройке правил сети к учетной записи хранения могут получать доступ только приложения из разрешенных сетей.  Однако и из разрешенной сети доступ к учетной записи хранения имеют только приложения с надлежащим уровнем авторизации (допустимый ключ доступа или токен SAS).

> [!IMPORTANT]
> Включение правил брандмауэра для учетной записи хранения заблокирует доступ для входящих запросов к данным, в том числе из других служб Azure.  Сюда входит использование портала, запись журналов и т. д.  Повторно включить функции для соответствующих служб можно с помощью раздела [Исключения](#Exceptions), представленного ниже.  Для доступа к порталу это необходимо будет сделать на компьютере, размещенном в пределах настроенной доверенной границы (на основе протокола IP или виртуальной сети).
>

## <a name="scenarios"></a>Сценарии
В учетных записях хранения можно по умолчанию настроить запрет доступа для трафика (включая интернет-трафик) из всех сетей.  Вы можете разрешить доступ для трафика из конкретных виртуальных сетей Azure, что позволит вам создать границу для приложений в виде безопасной сети.  Кроме того, можно разрешить доступ для общедоступных диапазонов IP-адресов в Интернете, позволив устанавливать подключения из конкретных интернет-клиентов или локальных клиентов.

В службе хранилища Azure сетевые правила применяются ко всем сетевым протоколам, включая REST и SMB.  Для доступа к данным с помощью таких инструментов, как портал Azure, приложение "Обозреватель службы хранилища Azure" и AZCopy, требуются явные правила сети, предоставляющие доступ.

Правила сети могут применяться к имеющимся учетным записям хранения, а также во время создания учетных записей хранения.

После этого правила сети применяются ко всем запросам.  Токены SAS, позволяющие получить доступ к конкретной службе IP-адресов, **ограничивают** доступ владельца токена, но не предоставляют доступ к каким-либо новым ресурсам, не указанным в настроенных сетевых правилах. 

Правила сети **не** распространяются на трафик дисков виртуальной машины (включая операции подключения и отключения, а также дисковые операции ввода-вывода).  Сетевые правила обеспечивают безопасность во время доступа REST к страничным BLOB-объектам.

Классические учетные записи хранения **не** поддерживают брандмауэры и виртуальные сети.

Резервное копирование и восстановление виртуальных машин с неуправляемыми дисками в учетных записях хранения с действующими сетевыми правилами поддерживается путем создания исключения, как описано в разделе [Исключения](/storage/common/storage-network-security#exceptions) этой статьи.  Исключения брандмауэра не применяются к управляемым дискам, так как ими уже управляет Azure.

## <a name="change-the-default-network-access-rule"></a>Изменение сетевого правила доступа по умолчанию
По умолчанию учетные записи хранения принимают запросы на подключение от клиентов в сети.  Для ограничения доступа из выбранных сетей необходимо сначала изменить действие по умолчанию.

> [!WARNING]
> Изменение сетевых правил может повлиять на возможность подключения приложений к службе хранилища Azure.  Если настроить **запрет** доступа для сетевых правил по умолчанию, доступ к данным блокируется (если не применяются определенные сетевые правила, *разрешающие* доступ).  Предоставьте доступ для разрешенных сетей с помощью сетевых правил, прежде чем изменить правила по умолчанию и запретить доступ.
>

#### <a name="azure-portal"></a>Портал Azure
1. Перейдите к учетной записи хранения, которую нужно защитить.  

2. Щелкните меню параметров **Брандмауэры и виртуальные сети**.
3. Чтобы запретить доступ по умолчанию, разрешите доступ в разделе "Выбранные сети".  Чтобы разрешить передачу трафика из всех сетей, разрешите доступ в разделе "Все сети".
4. Щелкните *Сохранить*, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell
1. Установите последнюю версию [Azure PowerShell](/powershell/azure/install-azurerm-ps) и [выполните вход](/powershell/azure/authenticate-azureps).

2. Отобразите состояние правила по умолчанию для учетной записи хранения.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Настройте в правиле по умолчанию запрет сетевого доступа по умолчанию.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Настройте в правиле по умолчанию разрешение сетевого доступа по умолчанию.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLI 2.0
1. [Установите Azure CLI 2.0](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).
2. Отобразите состояние правила по умолчанию для учетной записи хранения.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Настройте в правиле по умолчанию запрет сетевого доступа по умолчанию.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Настройте в правиле по умолчанию разрешение сетевого доступа по умолчанию.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Предоставление доступа из виртуальной сети
Вы можете настроить учетные записи хранения таким образом, чтобы они разрешали доступ только из определенной виртуальной сети Azure. 

Включив в виртуальной сети [конечную точку службы](/azure/virtual-network/virtual-network-service-endpoints-overview) для службы хранилища Azure, вы обеспечите оптимальный маршрут трафика к службе хранилища Azure. В каждом запросе также передаются удостоверения виртуальной сети и подсети.  Администраторы могут впоследствии настроить сетевые правила учетной записи хранения, разрешающие получение запросов из определенных подсетей в виртуальной сети.  Клиентам, которым предоставлен доступ по этим сетевым правилам, для доступа к данным требуется следовать требованиям авторизации учетной записи хранения.

Каждая учетная запись хранения поддерживает 100 правил виртуальной сети, которые можно объединить с [правилами IP-сети](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Доступные регионы виртуальной сети
Как правило, конечные точки службы работают между виртуальными сетями и экземплярами служб в одном регионе Azure.  При использовании конечных точек службы со службой хранилища Azure область расширяется для включения [парных регионов](/azure/best-practices-availability-paired-regions).  Таким образом обеспечивается непрерывность работы во время региональной отработки отказа, а также простой доступ к экземплярам геоизбыточного хранилища (RA-GRS) только для чтения.  Правила сети, предоставляющие доступ к учетной записи хранения из виртуальной сети, также предоставляют доступ к любому экземпляру RA-GRS.

Планируя аварийное восстановление при региональном сбое, следует заранее подготовить виртуальные сети в парном регионе. Нужно включить конечные точки службы для службы хранилища Azure, а также применить правила сети, предоставляющие доступ из этих альтернативных виртуальных сетей, к учетным записям геоизбыточного хранилища.

> [!NOTE]
> Конечные точки службы не применяются к трафику вне региона виртуальной сети и заданного парного региона.  Правила сети, предоставляющие доступ к учетным записям хранения из виртуальных сетей, могут применяться только к виртуальным сетям в основном регионе учетной записи хранения или в указанном парном регионе.
>

### <a name="required-permissions"></a>Необходимые разрешения
Чтобы применить правило виртуальной сети к учетной записи хранения, у пользователя должно быть разрешение на *присоединение службы к добавляемой подсети*.  Это разрешение включено во встроенную роль *Участник учетной записи хранения*, и его можно добавить к определениям пользовательских ролей.

Учетная запись хранения и виртуальные сети, которым предоставлен доступ, **могут** находиться в разных подписках, которые должны быть частью одного клиента Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Управление правилами для виртуальной сети
Правилами виртуальных сетей для учетных записей хранения можно управлять на портале Azure с помощью PowerShell или CLI 2.0.

#### <a name="azure-portal"></a>Портал Azure
1. Перейдите к учетной записи хранения, которую нужно защитить.  
2. Щелкните меню параметров **Брандмауэры и виртуальные сети**.
3. Убедитесь, что вы разрешили доступ в разделе "Выбранные сети".
4. Чтобы предоставить доступ виртуальной сети в новом правиле сети, в разделе "Виртуальные сети" щелкните Add existing (Добавить имеющуюся) для выбора имеющейся виртуальной сети и подсетей, а затем выберите *Добавить*.  Чтобы создать виртуальную сеть и предоставить ей доступ, щелкните *Добавить новое*, укажите сведения, необходимые для создания виртуальной сети, и выберите *Создать*.

> [!NOTE]
> Если конечная точка службы для службы хранилища Azure для выбранной виртуальной сети и подсетей не настроена ранее, ее можно настроить в ходе этой операции.
>

5. Для удаления правила виртуальной сети или подсети щелкните "...", чтобы открыть контекстное меню для виртуальной сети или подсети, и выберите пункт "Удалить".
6. Щелкните *Сохранить*, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell
1. Установите последнюю версию [Azure PowerShell](/powershell/azure/install-azurerm-ps) и [выполните вход](/powershell/azure/authenticate-azureps).
2. Выведите список правил для виртуальной сети.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Включите конечную точку службы для службы хранилища Azure на имеющейся виртуальной сети и подсети.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Добавьте правило сети для виртуальной сети и подсети.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Удалите правило сети для виртуальной сети и подсети.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Настройте для [правила по умолчанию](#change-the-default-network-access-rule) запрет. В противном случае правила сети не будут действовать.
>

#### <a name="cliv2"></a>CLI 2.0
1. [Установите Azure CLI 2.0](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).
2. Выведите список правил для виртуальной сети.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Включите конечную точку службы для службы хранилища Azure на имеющейся виртуальной сети и подсети.
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Добавьте правило сети для виртуальной сети и подсети.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Удалите правило сети для виртуальной сети и подсети. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Настройте для [правила по умолчанию](#change-the-default-network-access-rule) запрет. В противном случае правила сети не будут действовать.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Предоставление доступа из диапазона IP-адресов в Интернете
Учетные записи хранения можно настроить для разрешения доступа из определенных диапазонов общедоступных IP-адресов в Интернете.  При этом определенным интернет-службам и локальным сетям предоставляется доступ, а общий интернет-трафик блокируется.

Разрешенные диапазоны адресов в Интернете можно предоставить с использованием [нотации CIDR](https://tools.ietf.org/html/rfc4632) в формате *16.17.18.0/24* или в качестве отдельных IP-адресов, таких как *16.17.18.19*.

> [!NOTE]
> Небольшие адреса, использующие размеры с префиксом /31 или /32, не поддерживаются.  Эти диапазоны следует настраивать с помощью отдельных правил для IP-адресов.
>

Правила IP-сети можно применять только для **общедоступных** IP-адресов в Интернете.  Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в документе RFC 1918), запрещено использовать в правилах IP.  Частные сети включают адреса, начинающиеся с *10.\**, *172.16.\** и *192.168.\**

Сейчас поддерживаются только IPV4-адреса.

Каждая учетная запись хранения поддерживает 100 правил IP-сети, которые можно объединить с [правилами виртуальной сети](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Настройка доступа из локальных сетей
Чтобы предоставить доступ из вашей локальной сети к учетной записи хранения в правиле IP-сети, необходимо определить IP-адреса для Интернета, используемые в сети.  За помощью обращайтесь к администратору сети.

Если сеть подключена к сети Azure с помощью [ExpressRoute](/azure/expressroute/expressroute-introduction), для каждого канала в Microsoft Edge настроено два общедоступных IP-адреса, используемые для подключения к службам Майкрософт, таким как служба хранилища Azure, с использованием [общедоступного пиринга Azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Чтобы разрешить взаимодействие канала со службой хранилища Azure, необходимо создать правила IP-сети для общих IP-адресов каналов.  Чтобы найти общедоступные IP-адреса канала ExpressRoute, [отправьте запрос ExpressRoute в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) через портал Azure.


### <a name="managing-ip-network-rules"></a>Управление правилами IP-сети
Правилами IP-сети для учетных записей хранения можно управлять на портале Azure с помощью PowerShell или CLI 2.0.

#### <a name="azure-portal"></a>Портал Azure
1. Перейдите к учетной записи хранения, которую нужно защитить.  
2. Щелкните меню параметров **Брандмауэры и виртуальные сети**.
3. Убедитесь, что вы разрешили доступ в разделе "Выбранные сети".
4. Чтобы предоставить доступ к диапазону IP-адресов в Интернете, введите IP-адрес или диапазон адресов (в формате CIDR) в разделе "Брандмауэр" в поле "Диапазоны адресов".
5. Для удаления правила IP-сети нажмите кнопку "...", чтобы открыть контекстное меню правила, и щелкните "Удалить".
6. Щелкните *Сохранить*, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell
1. Установите последнюю версию [Azure PowerShell](/powershell/azure/install-azurerm-ps) и [выполните вход](/powershell/azure/authenticate-azureps).
2. Выведите список правил IP-сети.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Добавьте правило сети для отдельного IP-адреса.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Добавьте правило сети для диапазона IP-адресов.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Удалите правило сети для отдельного IP-адреса. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Удалите правило сети для диапазона IP-адресов.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Настройте для [правила по умолчанию](#change-the-default-network-access-rule) запрет. В противном случае правила сети не будут действовать.
>

#### <a name="cliv2"></a>CLI 2.0
1. [Установите Azure CLI 2.0](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).
2. Выведите список правил IP-сети.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Добавьте правило сети для отдельного IP-адреса.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Добавьте правило сети для диапазона IP-адресов.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Удалите правило сети для отдельного IP-адреса.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Удалите правило сети для диапазона IP-адресов.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Настройте для [правила по умолчанию](#change-the-default-network-access-rule) запрет. В противном случае правила сети не будут действовать.
>

## <a name="exceptions"></a>Исключения
Правила сети могут обеспечить безопасную конфигурацию сети для большинства сценариев. Однако в некоторых случаях нужны исключения, чтобы воспользоваться всеми функциями.  В учетных записях хранения можно настроить исключения для доверенных служб Майкрософт, а также для доступа к данным аналитики хранилища.

### <a name="trusted-microsoft-services"></a>Доверенные службы Майкрософт
Некоторые службы Майкрософт, взаимодействующие с учетными записями хранения, работают из сетей, которым нельзя предоставить доступ через сетевые правила. 

Чтобы обеспечить таким службам работу надлежащим образом, можно разрешить набору надежных служб Майкрософт обходить правила сети. Эти службы будут использовать строгую аутентификацию для доступа к учетной записи хранения.

При включении исключения "Доверенные службы Майкрософт" доступ к учетной записи хранения предоставляется следующим службам (при регистрации в вашей подписке):

|Service|Имя поставщика ресурсов|Назначение|
|:------|:---------------------|:------|
|Служба архивации Azure|Microsoft.Backup|Выполняет резервное копирование и восстановление неуправляемых дисков в виртуальных машинах IAAS. (Не требуется для управляемых дисков.) [Узнайте больше](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Создание пользовательских образов и установка артефактов.  [Узнайте больше](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Сетка событий Azure|Microsoft.EventGrid|Включение публикации событий в хранилище BLOB-объектов.  [Узнайте больше](https://docs.microsoft.com/azure/event-grid/overview).|
|Концентраторы событий Azure|Microsoft.EventHub|Архивация данных с помощью записи концентраторов событий.  [Подробнее](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Сеть Azure|Microsoft.Networking|Хранение и анализ журналов сетевого трафика.  [Узнайте больше](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Доступ к данным аналитики хранилища
В некоторых случаях доступ для чтения журналов диагностики и метрик нужно получать за пределами сети.  Вы можете предоставить исключения из правил сети, чтобы разрешить доступ на чтение к файлам журналов учетной записи хранения, таблицам метрик или и к тем, и к другим. Дополнительные сведения о работе с аналитикой хранилища см. в [этой статье](/azure/storage/storage-analytics).

### <a name="managing-exceptions"></a>Управление исключениями
Исключениями из правил сети можно управлять с помощью портала Azure, PowerShell или Azure CLI v2.

#### <a name="azure-portal"></a>Портал Azure
1. Перейдите к учетной записи хранения, которую нужно защитить.  
2. Щелкните меню параметров **Брандмауэры и виртуальные сети**.
3. Убедитесь, что вы разрешили доступ в разделе "Выбранные сети".
4. В разделе "Исключения" выберите исключения, которые нужно предоставить.
5. Щелкните *Сохранить*, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell
1. Установите последнюю версию [Azure PowerShell](/powershell/azure/install-azurerm-ps) и [выполните вход](/powershell/azure/authenticate-azureps).
2. Отобразите исключения для сетевых правил учетной записи хранения.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Настройте исключения для сетевых правил учетной записи хранения.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Удалите исключения для сетевых правил учетной записи хранения.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Настройте для [правила по умолчанию](#change-the-default-network-access-rule) запрет. В противном случае удаление исключений не подействует.
>

#### <a name="cliv2"></a>CLI 2.0
1. [Установите Azure CLI 2.0](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).
2. Отобразите исключения для сетевых правил учетной записи хранения.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Настройте исключения для сетевых правил учетной записи хранения.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Удалите исключения для сетевых правил учетной записи хранения.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Настройте для [правила по умолчанию](#change-the-default-network-access-rule) запрет. В противном случае удаление исключений не подействует.
>

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о конечных точках службы сети Azure см. в статье [Virtual Network Service Endpoints (Preview)](/azure/virtual-network/virtual-network-service-endpoints-overview) (Конечные точки виртуальной сети (предварительная версия)).

Более подробную информацию о безопасности службы хранилища Azure см. в статье [Руководство по безопасности службы хранилища Azure](storage-security-guide.md).
