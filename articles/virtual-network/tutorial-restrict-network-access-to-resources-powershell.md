---
title: Ограничение сетевого доступа к ресурсам PaaS с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как ограничить сетевой доступ к ресурсам Azure, таким как служба хранилища Azure и база данных SQL Azure, посредством конечных точек службы виртуальной сети с помощью PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 7e402af74babda2ce32d4a1597c61d71aba89b9e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Ограничение сетевого доступа к ресурсам PaaS посредством конечных точек службы виртуальной сети с помощью PowerShell

Конечные точки службы виртуальной сети позволяют ограничить сетевой доступ к некоторым ресурсам службы Azure определенной подсетью виртуальной сети. Можно также запретить доступ к ресурсам через Интернет. Конечные точки службы предоставляют прямое подключение из виртуальной сети к поддерживаемым службам Azure. Это позволяет использовать закрытый диапазон адресов виртуальной сети для доступа к службам Azure. Трафик, поступающий к ресурсам Azure через конечные точки службы, всегда остается в магистральной сети Microsoft Azure. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Создание виртуальной сети с одной подсетью.
> * Добавление подсети и включение конечной точки службы.
> * Создание ресурса Azure и разрешение сетевого доступа к нему только из подсети.
> * Развертывание виртуальной машины в каждой подсети.
> * Подтверждение прав доступа к ресурсу из подсети.
> * Подтверждение запрета доступа к ресурсу из подсети и Интернета.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Перед созданием виртуальной сети необходимо создать для нее группу ресурсов и другие компоненты, указанные в этой статье. Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов *myResourceGroup*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork* и префиксом адреса *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Создайте конфигурацию подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). В следующем примере создается конфигурация подсети *Public*.

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Создайте подсеть в виртуальной сети, записав конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork).

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Включение конечной точки службы 

Вы можете включить конечные точки службы только для служб, поддерживающих эту функцию. Просмотрите, какие службы с поддержкой конечных точек службы доступны в расположении Azure, выполнив командлет [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). В следующем примере возвращается список служб с поддержкой конечных точек службы, доступных в регионе *eastus*. Список возвращаемых служб со временем будет увеличиваться, так как поддержка конечных точек службы будет реализовываться во все большем числе служб Azure.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Создайте дополнительную подсеть в виртуальной сети. В этом примере создается подсеть *Private* с конечной точкой службы для службы *Microsoft.Storage*. 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Ограничение входящего и исходящего сетевого доступа для подсети

Создайте правила группы безопасности сети с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Приведенное ниже правило разрешает исходящий доступ к общедоступным IP-адресам, назначенным службе хранилища Azure. 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Следующее правило запрещает доступ ко всем общедоступным IP-адресам. Предыдущее правило переопределяет это правило ввиду более высокого приоритета. Оно предоставляет доступ к общедоступным IP-адресам службы хранилища Azure.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Следующее правило разрешает для подсети входящий трафик протокола удаленного рабочего стола (RDP), поступающий из любого расположения. В подсети разрешены подключения к удаленному рабочему столу, чтобы позже можно было подтвердить сетевой доступ к ресурсу.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Создайте группу безопасности сети с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). В следующем примере создается группа безопасности сети *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Свяжите группу безопасности сети с подсетью *Private* с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig), а затем запишите конфигурацию подсети в виртуальную сеть. В следующем примере подсеть *Private* привязывается к группе безопасности сети *myNsgPrivate*.

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Ограничение сетевого доступа к ресурсу

Действия, необходимые для ограничения сетевого доступа к ресурсам, созданным с помощью служб Azure, использующих конечные точки службы, отличаются в зависимости службы. Ознакомьтесь с документацией по отдельным службам, чтобы получить точные инструкции для них. В оставшейся части этой статьи в качестве примера приведены инструкции по ограничению сетевого доступа для учетной записи хранения Azure.

### <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Чтобы создать учетную запись хранения Azure, используйте командлет [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Замените `<replace-with-your-unique-storage-account-name>` именем, которое является уникальным для всех расположений Azure, содержащим только цифры и строчные буквы (длиной от 3 до 24 знаков).

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

После создания учетной записи хранения передайте ее ключ в переменную, выполнив командлет [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey).

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Этот ключ потребуется для создания файлового ресурса на более позднем этапе. Введите `$storageAcctKey` и запишите значение, так как позже его потребуется ввести вручную при подключении файлового ресурса как диска на виртуальной машине.

### <a name="create-a-file-share-in-the-storage-account"></a>Создание файлового ресурса в учетной записи хранения

Создайте объект контекста для своей учетной записи хранения и ключа, выполнив командлет [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). Этот контекст инкапсулирует имя учетной записи хранения и ее ключ.

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Создайте файловый ресурс командлетом [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare).

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Запрет любого сетевого доступа к учетной записи хранения

По умолчанию учетные записи хранения принимают сетевые подключения клиентов в любой сети. Чтобы ограничить доступ выбранными сетями, измените действие по умолчанию на *Запретить*, выполнив командлет [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). После запрещения сетевого доступа учетная запись хранения не будет доступна из любой сети.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Включение сетевого доступа из подсети

Получите созданную виртуальную сеть, выполнив командлет [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), а затем извлеките объект подсети Private в переменную командлетом [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig).

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Разрешите сетевой доступ к учетной записи хранения из подсети *Private*, выполнив командлет [Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Чтобы проверить сетевой доступ к учетной записи хранения, разверните виртуальную машину в каждой подсети.

### <a name="create-the-first-virtual-machine"></a>Создание первой виртуальной машины

Создайте виртуальную машину в подсети *Public* с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). При выполнении следующей команды будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины. Параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Будут возвращены выходные данные, как показано ниже.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>Создание второй виртуальной машины

Создайте виртуальную машину в подсети *Private*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Создание виртуальной машины в Azure занимает несколько минут. Не переходите к следующему шагу только, пока Azure не завершит создание виртуальной машины и не вернет выходные данные в окне PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Подтверждение прав доступа к учетной записи хранения

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVmPrivate*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Замените `<publicIpAddress>` в следующей команде общедоступным IP-адресом, возвращенным предыдущей командой, а затем введите следующую команду: 

```powershell
mstsc /v:<publicIpAddress>
```

Будет создан и скачан на ваш компьютер файл протокола удаленного рабочего стола (RDP-файл). Откройте этот RDP-файл. При появлении запроса выберите **Подключиться**. Введите имя пользователя и пароль, указанные при создании виртуальной машины. Возможно, потребуется выбрать **More choices** (Дополнительные варианты), а затем **Use a different account** (Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины. Нажмите кнопку **ОК**. При входе в систему может появиться предупреждение о сертификате. Если вы получили предупреждение, выберите **Да** или **Продолжить**.

На виртуальной машине *myVmPrivate* с помощью PowerShell подключите файловый ресурс Azure как диск Z. Перед выполнением следующих команд замените `<storage-account-key>` и `<storage-account-name>` собственными значениями или значениями, полученными при [создании учетной записи хранения](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell вернет выходные данные, как в следующем примере.

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Файловый ресурс Azure успешно подключен как диск Z.

Убедитесь, что виртуальная машина запрещает любые исходящие подключения с любых других общедоступных IP-адресов.

```powershell
ping bing.com
```

Ответы не отобразятся, так как группа безопасности сети, связанная с подсетью *Private*, не разрешает исходящий доступ к общедоступным IP-адресам, отличным от адреса, назначенного службе хранилища Azure.

Закройте сеанс удаленного рабочего стола с виртуальной машиной *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Подтверждение запрета доступа к учетной записи хранения

Получите общедоступный IP-адрес виртуальной машины *myVmPublic*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Замените `<publicIpAddress>` в следующей команде общедоступным IP-адресом, возвращенным предыдущей командой, а затем введите следующую команду: 

```powershell
mstsc /v:<publicIpAddress>
```

На виртуальной машине *myVmPublic* попытайтесь повторно подключить файловый ресурс Azure как диск Z. Перед выполнением приведенных ниже команд замените `<storage-account-key>` и `<storage-account-name>` собственными значениями или значениями, полученными при [создании учетной записи хранения](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Доступ к файловому ресурсу будет запрещен, и отобразится сообщение об ошибке `New-PSDrive : Access is denied`. В доступе будет отказано, так как виртуальная машина *myVmPublic* развернута в подсети *Public*. Для подсети *Public* не включена конечная точка службы для службы хранилища Azure, и учетная запись хранения предназначена только для сетевого доступа из подсети *Private*, а не *Public*.

Закройте сеанс удаленного рабочего стола с виртуальной машиной *myVmPublic*.

Используя свой компьютер, попытайтесь просмотреть файловые ресурсы в учетной записи хранения, выполнив следующую команду.

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

В доступе будет отказано, и отобразится сообщение об ошибке *Get-AzureStorageFile : The remote server returned an error: (403) Forbidden. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation* (Get-AzureStorageFile: удаленный сервер вернул ошибку: (403) запрещено. Код состояния HTTP: 403. Сообщение об ошибке HTTP: данный запрос не авторизован для выполнения этой операции), так как ваш компьютер не находится в подсети *Private* виртуальной сети *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы включили конечную точку службы для подсети виртуальной сети. Вы узнали, что конечные точки службы можно включать для ресурсов, развернутых несколькими службами Azure. Вы создали учетную запись хранения Azure и ограничили сетевой доступ к учетной записи хранения ресурсами одной подсети в виртуальной сети. Прежде чем создавать конечные точки службы в рабочих виртуальных сетях, рекомендуется внимательно ознакомиться с [конечными точками службы](virtual-network-service-endpoints-overview.md).

Если в вашей учетной записи имеется несколько виртуальных сетей, можно соединить две виртуальные сети, чтобы ресурсы в каждой из них могли взаимодействовать друг с другом. Перейдите к следующему руководству, чтобы научиться соединять виртуальные сети.

> [!div class="nextstepaction"]
> [Подключение виртуальных сетей](./tutorial-connect-virtual-networks-powershell.md)
