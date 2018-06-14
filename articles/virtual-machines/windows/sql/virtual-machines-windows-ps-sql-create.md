---
title: Руководство по подготовке виртуальных машин SQL Server с помощью Azure PowerShell | Документация Майкрософт
description: Содержит описание действий и команд PowerShell для создания виртуальной машины Azure на основе образа из коллекции образов виртуальных машин SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 2f0d9c42e32f2dd1181eac8d74c324b5ff2b0c53
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944524"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Как подготовить виртуальные машины SQL Server с помощью Azure PowerShell

В этом руководстве рассматриваются варианты создания виртуальных машин SQL Server под управлением ОС Windows с помощью Azure PowerShell. Краткий пример Azure PowerShell с дополнительными значениями по умолчанию приведен в статье [Создание виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этой статьей требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Настройка подписки

1. Откройте PowerShell и установите доступ к учетной записи Azure, выполнив команду **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Должно отобразиться окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

## <a name="define-image-variables"></a>Определение переменных образа
Чтобы упростить операции повторного использования и создания скриптов, начните с определения количества переменных. Изменяйте значения параметров по своему усмотрению. Однако учитывайте при этом ограничения именования, связанные с длиной имени и специальными знаками.

### <a name="location-and-resource-group"></a>Расположение и группа ресурсов
Используйте две указанные ниже переменные, чтобы определить область данных и группу ресурсов, в которых вы создадите другие ресурсы для виртуальной машины.

Измените их соответствующим образом, а затем выполните следующие командлеты, чтобы инициализировать эти переменные:

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Свойства хранилища
Используйте следующие переменные, чтобы определить учетную запись хранения и тип хранилища в виртуальной машине.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные. Обратите внимание, что в этом примере используется [хранилище уровня "Премиум"](../premium-storage.md), которое рекомендуется для рабочих нагрузок в рабочей среде.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Свойства сети
Используйте указанные ниже переменные, чтобы определить сетевой интерфейс, метод распределения TCP/IP, имя виртуальной сети, имя виртуальной подсети, диапазон IP-адресов для виртуальной сети, диапазон IP-адресов для подсети и метку общедоступного доменного имени, которые будет использовать сеть на виртуальной машине.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Свойства виртуальной машины
Используйте указанные ниже переменные, чтобы определить имя виртуальной машины, имя компьютера, размер виртуальной машины и имя диска операционной системы для виртуальной машины.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Выбор образа SQL Server
Используйте следующие переменные, чтобы определить образ SQL Server, который будет применен для виртуальной машины.

1. Сначала получите список всех образов SQL Server, выполнив команду **Get-AzureRmVMImageOffer**.

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. В данном руководстве для указания SQL Server 2017 в Windows Server 2016 используйте следующие переменные:

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Затем получите список выпусков, доступных для вашего предложения.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. В данном руководстве используется SQL Server 2017 Developer Edition (**SQLDEV**). Лицензию Developer Edition для тестирования и разработки можно получить бесплатно. Вы оплачиваете только стоимость выполнения виртуальной машины.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Если используется модель развертывания с помощью Resource Manager, первый создаваемый объект — группа ресурсов. Используйте командлет [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup), чтобы создать группу ресурсов Azure и соответствующие ресурсы. При этом имя и расположение группы ресурсов определяются в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы создать группу ресурсов.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Виртуальной машине требуются ресурсы хранения для диска операционной системы, а также файлов данных и журналов SQL Server. Для упрощения создадим один диск для всех ресурсов. Позже можно будет подключить дополнительные диски, выполнив командлет [Add-Azure Disk](/powershell/module/azure/add-azuredisk), чтобы поместить файлы данных и журналов SQL Server на выделенные диски. Выполните командлет [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount), чтобы создать стандартную учетную запись хранения в новой группе ресурсов. При этом имя учетной записи хранения, номер SKU хранилища и расположение определяются в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы создать учетную запись хранения.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Создание учетной записи хранения может занять несколько минут.

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Виртуальной машине нужны сетевые ресурсы для подключения к сети.

* Каждой виртуальной машине требуется виртуальная сеть.
* Для виртуальной сети следует определить по крайней мере одну подсеть.
* Для сетевого интерфейса следует определить общедоступный или частный IP-адрес.

### <a name="create-a-virtual-network-subnet-configuration"></a>Создание конфигурации подсети в виртуальной сети
Сначала создайте конфигурацию подсети для виртуальной сети. В рамках этого руководства мы создадим подсеть по умолчанию, используя командлет [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Имя подсети и префикс адреса для нашей конфигурации подсети в виртуальной сети определяются в переменных, инициализированных ранее.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства конфигурации подсети виртуальной сети. Однако это выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать конфигурацию виртуальной подсети.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Далее с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) создайте виртуальную сеть в новой группе ресурсов. При этом имя, расположение и префикс адреса определяются в переменных, инициализированных ранее. Кроме того, используйте конфигурацию подсети, определенную на предыдущем шаге.

Выполните следующий командлет, чтобы создать виртуальную сеть.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса
Теперь, определив виртуальную сеть, необходимо настроить IP-адрес для подключения к виртуальной машине. В рамках этого руководства мы создадим общедоступный IP-адрес с применением динамического предоставления IP-адресов, чтобы обеспечить подключение к Интернету. Используйте командлет [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress), чтобы создать общедоступный IP-адрес в ранее созданной группе ресурсов. При этом имя, расположение, метод распределения и метка общедоступного имени DNS-домена определяются в переменных, инициализированных ранее.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства общедоступного IP-адреса. Однако это выходит за рамки данного руководства. Кроме того, можно создать частный или статический адрес, но это также выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать общедоступный IP-адрес.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Создание группы безопасности сети
Чтобы защитить трафик виртуальной машины и SQL Server, создайте группу безопасности сети.

1. Сначала создайте правило группы безопасности сети для RDP, которое разрешает подключения с удаленного рабочего стола.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Настройте правило группы безопасности сети, которое разрешает трафик через TCP-порт 1433. Так вы сможете подключаться к SQL Server через Интернет.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Затем создайте группу безопасности сети.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Создание сетевого интерфейса
Теперь все готово для создания сетевого интерфейса, который будет использовать виртуальная машина. Чтобы создать сетевой интерфейс в ранее созданной группе ресурсов с ранее определенными именем, расположением, подсетью и общедоступным IP-адресом, вызовите командлет [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface).

Выполните следующий командлет, чтобы создать сетевой интерфейс.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Настройка объекта виртуальной машины
После определения ресурсов хранения и сетевых ресурсов можно определить вычислительные ресурсы для виртуальной машины. В рамках этого руководства мы укажем размер виртуальной машины, различные свойства операционной системы, ранее созданный сетевой интерфейс, определим хранилище BLOB-объектов, а затем укажем диск операционной системы.

### <a name="create-the-vm-object"></a>Создание виртуальной машины
Сначала укажем размер виртуальной машины. В этом руководстве мы указываем DS13. Чтобы создать настраиваемый объект виртуальной машины, вызовите командлет [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). При этом имя и размер определяются в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы создать объект виртуальной машины.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Создание объекта учетных данных для хранения имени и пароля локального администратора
Чтобы задать свойства операционной системы для виртуальной машины, необходимо сначала предоставить учетные данные для учетной записи локального администратора в виде защищенной строки. Для этого используйте командлет [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Выполните следующий командлет и в окне запроса учетных данных PowerShell введите имя и пароль, которые будут использоваться для учетной записи локального администратора на виртуальной машине.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Определение свойств операционной системы для виртуальной машины
Теперь необходимо задать параметры операционной системы виртуальной машины. Используйте командлет [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), чтобы установить тип операционной системы (например, Windows). Для этого установите [агент виртуальной машины](../../extensions/agent-windows.md). Укажите, что командлет включает автоматическое обновление, и задайте имя виртуальной машины, имя компьютера и учетные данные в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы задать свойства операционной системы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Добавление сетевого интерфейса к виртуальной машине
Далее добавьте созданный ранее сетевой интерфейс к виртуальной машине. Для этого вызовите командлет [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) и переменную сетевого интерфейса, определенную ранее.

Выполните следующий командлет, чтобы указать сетевой интерфейс для виртуальной машины.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Определение расположения хранилища BLOB-объектов для диска, используемого виртуальной машиной
Далее укажите расположение хранилища BLOB-объектов для диска, который будет использоваться в виртуальной машине, с определенными ранее переменными.

Выполните следующий командлет, чтобы задать расположение хранилища BLOB-объектов.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Определение свойств диска операционной системы для виртуальной машины
Затем определите свойства диска операционной системы для виртуальной машины. Используйте командлет [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), чтобы указать, что для операционной системы виртуальной машины используется образ, настроить кэширование для чтения (так как SQL Server устанавливается на том же диске), а также определить имя виртуальной машины и диск операционной системы с использованием переменных, определенных ранее.

Выполните следующий командлет, чтобы задать свойства диска операционной системы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Определение образа платформы для виртуальной машины
Последний этап конфигурации — определение образа платформы для виртуальной машины. В рамках этого руководства мы используем последний образ SQL Server 2016 CTP-версии. Чтобы задать образ согласно значениям переменных, определенных ранее, используйте командлет [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage).

Выполните следующий командлет, чтобы указать образ платформы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL
Теперь по завершении конфигурации все готово для создания виртуальной машины. Для этого используйте командлет [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) и определенные переменные.

Выполните следующий командлет, чтобы создать виртуальную машину.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Теперь виртуальная машина создана.

> [!NOTE]
> Ошибку о диагностике загрузки можно игнорировать. Стандартная учетная запись хранения создается для диагностики загрузки, так как для диска виртуальной машины указана учетная запись хранилища класса Premium.

## <a name="install-the-sql-iaas-agent"></a>Установка агента SQL IaaS
Виртуальные машины SQL Server поддерживают функции автоматизированного управления при наличии [расширения агента IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Чтобы установить агент на новой виртуальной машине, после его создания выполните следующую команду:

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Удаление тестовой виртуальной машины

Если не требуется, чтобы виртуальная машина работала постоянно, можно избежать ненужных затрат, останавливая ее, когда она не используется. С помощью следующей команды можно остановить виртуальную машину, оставив ее доступной для использования в будущем.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

А с помощью команды **Remove-AzureRmResourceGroup** можно удалить все ресурсы, связанные с виртуальной машиной, без возможности восстановления. Это также приведет к окончательному удалению самой виртуальной машины, поэтому указанную команду следует использовать с осторожностью.

## <a name="example-script"></a>Пример сценария
Файл ниже содержит полный сценарий PowerShell для этого руководства. Предполагается, что вы уже настроили подписку Azure для использования с командами **Connect-AzureRmAccount** и **Select-AzureRmSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Дополнительная информация
После создания виртуальной машины можно:

- Подключаться к виртуальной машине через удаленный рабочий стол (RDP).
- Настраивать параметры SQL Server на портале для виртуальной машины, включая:
   - [параметры хранилища](virtual-machines-windows-sql-server-storage-configuration.md); 
   - [автоматизированные задачи управления](virtual-machines-windows-sql-server-agent-extension.md).
- [Настраивать подключение](virtual-machines-windows-sql-connect.md).
- Подключать клиентов и приложения к новому экземпляру SQL Server.

