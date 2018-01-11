---
title: "Создание виртуальных машин SQL Server с помощью Azure PowerShell | Документы Microsoft"
description: "Предоставляет действия и команды PowerShell для создания ВМ Azure с образах коллекции виртуальных машин SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/29/2017
ms.author: jroth
ms.openlocfilehash: 5babea628180501e959387f80dac55618051f552
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>Создание виртуальных машин SQL Server с помощью Azure PowerShell

В этом руководстве объясняется варианты для создания виртуальных машин Windows SQL Server с помощью Azure PowerShell. Пример упрощенное Azure PowerShell дополнительные значениями по умолчанию, в разделе [быстрый запуск VM в SQL Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Настройка подписки

1. Откройте PowerShell и обеспечить доступ к вашей учетной записью Azure, выполнив **AzureRmAccount добавить** команды.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Появится экран входа для ввода учетных данных. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

## <a name="define-image-variables"></a>Определение переменных образа
Чтобы упростить операции создания, повторного использования и скрипт, начинают с определения количество переменных. Изменяйте значения параметров по своему усмотрению. Однако учитывайте при этом ограничения именования, связанные с длиной имени и специальными знаками.

### <a name="location-and-resource-group"></a>Расположение и группа ресурсов
Используйте две переменные для определения области данных и группы ресурсов, в который создания других ресурсов для виртуальной машины.

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

### <a name="choose-a-sql-server-image"></a>Выберите образ SQL Server
Используйте следующие переменные для определения образа SQL Server для использования виртуальной машины.

1. Во-первых, список всех предложений образа SQL Server с **Get AzureRmVMImageOffer** команды:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. В этом учебнике используйте следующие переменные для указания 2017 г. SQL Server в Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Далее, список ожидания Доступные выпуски для вашего предложения.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. В этом учебнике используется выпуск SQL Server Developer 2017 г. (**SQLDEV**). Лицензию Developer Edition для тестирования и разработки можно получить бесплатно. Вы оплачиваете только стоимость выполнения виртуальной машины.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Если используется модель развертывания с помощью Resource Manager, первый создаваемый объект — группа ресурсов. Используйте [New AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) для создания группы ресурсов Azure и его ресурсы с места, определенного в переменных, которые ранее инициализированы и имя группы ресурсов.

Выполните следующий командлет, чтобы создать группу ресурсов.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Виртуальной машине требуются ресурсы хранения для диска операционной системы, а также файлов данных и журналов SQL Server. Для простоты мы создать отдельный диск для обоих. Позже можно будет подключить дополнительные диски, выполнив командлет [Add-Azure Disk](/powershell/module/azure/add-azuredisk), чтобы поместить файлы данных и журналов SQL Server на выделенные диски. Используйте [New AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) командлет, чтобы создать учетную запись хранения standard в новой группы ресурсов и имя учетной записи хранения, имя Sku хранения и места, определенного с помощью переменных, которые ранее инициализированы .

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
Начните с создания конфигурации с подсетями для нашей виртуальной сети. В этом руководстве мы создаем подсети по умолчанию, используя [New AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) командлета. Создадим нашей конфигурации подсети виртуальной сети с определены переменные, которые ранее инициализирован с помощью префикса имя и адрес подсети.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства конфигурации подсети виртуальной сети. Однако это выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать конфигурацию виртуальной подсети.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть с помощью [New AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) командлета. Создание виртуальной сети в новой группы ресурсов, с именем, расположение и адрес префикс определен с помощью переменных, которые ранее инициализированы и с помощью конфигурации подсети, определенные в предыдущем шаге.

Выполните следующий командлет, чтобы создать виртуальную сеть.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса
Теперь, определив виртуальную сеть, необходимо настроить IP-адрес для подключения к виртуальной машине. В этом учебнике мы создать общедоступный IP-адрес, с помощью динамического IP-адресации для поддержки подключения к Интернету. Используйте [New AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) командлет, чтобы создать общедоступный IP-адрес в группе ресурсов, созданные ранее и адресов с именем, расположение, способ распределения и метка доменного имени DNS, определенные с помощью переменных, которые ранее инициализирован.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства общедоступного IP-адреса. Однако это выходит за рамки данного руководства. Кроме того, можно создать частный или статический адрес, но это также выходит за рамки данного руководства.

Выполните следующий командлет, чтобы создать общедоступный IP-адрес.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Создание группы безопасности сети
Чтобы защитить трафик виртуальной Машины и SQL Server, создайте группу безопасности сети.

1. Во-первых создайте правило группы безопасности сети для протокола удаленного рабочего СТОЛА разрешить подключения удаленного рабочего стола.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Настройте правило группы безопасности сети, которое разрешает трафик на TCP-порт 1433. Это позволяет подключения к SQL Server через Интернет.

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
Теперь все готово для создания сетевого интерфейса, который будет использовать виртуальная машина. Мы называем [New AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) ранее определенные для создания нашей сетевого интерфейса в группе ресурсов, созданного ранее и имя, расположение, подсеть и общедоступный IP-адрес.

Выполните следующий командлет, чтобы создать сетевой интерфейс.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Настройка объекта виртуальной машины
После определения ресурсов хранения и сетевых ресурсов можно определить вычислительные ресурсы для виртуальной машины. В этом руководстве мы размер виртуальной машины, а также различные свойства операционной системы, укажите сетевой интерфейс, который мы создали ранее, определить хранилище больших двоичных объектов, а затем укажите диск операционной системы.

### <a name="create-the-vm-object"></a>Создание виртуальной машины
Запускать, указав размер виртуальной машины. В этом руководстве мы указываем DS13. Мы называем [New AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) для создания объекта можно настроить виртуальную машину с именем и размер, определенный с помощью переменных, которые ранее инициализированы.

Выполните следующий командлет, чтобы создать объект виртуальной машины.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Создание объекта учетных данных для хранения имени и пароля локального администратора
Чтобы задать свойства операционной системы для виртуальной машины, необходимо сначала предоставить учетные данные для учетной записи локального администратора в виде защищенной строки. Чтобы сделать это, используйте [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) командлета.

Выполните следующий командлет и в окне запроса учетных данных PowerShell, введите имя и пароль для учетной записи локального администратора на виртуальной машине.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Определение свойств операционной системы для виртуальной машины
Теперь все готово для установки свойств операционной системы виртуальной машины с [набор AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) требуют, чтобы назначить тип операционной системы Windows, [агент виртуальной машины](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) должны быть установлены, укажите, что командлет включает автоматическое обновление и имя виртуальной машины, имя компьютера и учетные данные, используя переменные, которые ранее инициализированы.

Выполните следующий командлет, чтобы задать свойства операционной системы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Добавление сетевого интерфейса к виртуальной машине
Мы добавьте сетевого интерфейса, который мы создали ранее к виртуальной машине. Вызовите [AzureRmVMNetworkInterface добавить](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) командлет для добавления сетевого интерфейса, с помощью переменной сетевого интерфейса, определенного ранее.

Выполните следующий командлет, чтобы указать сетевой интерфейс для виртуальной машины.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Определение расположения хранилища BLOB-объектов для диска, используемого виртуальной машиной
Затем задайте место хранения больших двоичных объектов для диска, используемый виртуальной машиной, с помощью переменных, которые были определены ранее.

Выполните следующий командлет, чтобы задать расположение хранилища BLOB-объектов.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Определение свойств диска операционной системы для виртуальной машины
Затем задайте свойства диска для виртуальной машины операционной системы. Используйте [AzureRmVMOSDisk набор](/powershell/module/azurerm.compute/set-azurermvmosdisk) командлет, чтобы указать происхождения операционной системы для виртуальной машины из образа, Настройка кэширования для чтения только (потому, что сервер SQL Server установлен на том же диске) и определение виртуальной машины имя и диск операционной системы, определяются с помощью переменных, которые было определено ранее.

Выполните следующий командлет, чтобы задать свойства диска операционной системы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Определение образа платформы для виртуальной машины
Последний этап конфигурации — определение образа платформы для виртуальной машины. В рамках этого руководства мы используем последний образ SQL Server 2016 CTP-версии. Используйте [AzureRmVMSourceImage набор](/powershell/module/azurerm.compute/set-azurermvmsourceimage) командлет, чтобы использовать это изображение в соответствии с определением переменных, которые были определены ранее.

Выполните следующий командлет, чтобы указать образ платформы для виртуальной машины.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL
Теперь по завершении конфигурации все готово для создания виртуальной машины. Используйте [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) для создания виртуальной машины, используя переменные, которые мы определили.

Выполните следующий командлет, чтобы создать виртуальную машину.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Теперь виртуальная машина создана.

> [!NOTE]
> Можно пропустить ошибку о диагностике программа-робот. Стандартная учетная запись хранения создается для диагностики загрузки, так как указанной учетной записи хранения для диска виртуальной машины — учетная запись хранения premium.

## <a name="install-the-sql-iaas-agent"></a>Установка агента Iaas SQL
Виртуальные машины SQL Server поддерживает функции автоматического управления с [расширения агента SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Чтобы установить агент на новой виртуальной Машины, выполните следующую команду, после его создания.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Удалить тест виртуальной Машины

Если нет необходимости виртуальной Машины постоянно запущен, можно избежать ненужных затрат, остановки, она не во время работы. Следующая команда останавливает виртуальную Машину, но оставляет доступной для использования в будущем.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Можно также полностью удалить все ресурсы, связанные с виртуальной машиной с **AzureRmResourceGroup удаление** команды. При этом также будет удалена виртуальная машина, поэтому данную команду следует использовать с осторожностью.

## <a name="example-script"></a>Пример сценария
Файл ниже содержит полный сценарий PowerShell для этого руководства. Предполагается, что вы уже настроили подписку Azure на использование с командами **Add-AzureRmAccount** и **Select-AzureRmSubscription**.

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

## <a name="next-steps"></a>Дальнейшие действия
После создания виртуальной машины, можно сделать следующее.

- Подключитесь к виртуальной машине с помощью удаленного рабочего стола (RDP).
- Настройка параметров SQL Server на портале для виртуальной Машины, включая:
   - [Параметры хранилища](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Автоматическое управление задачами](virtual-machines-windows-sql-server-agent-extension.md)
- [Настройка подключения к](virtual-machines-windows-sql-connect.md).
- Подключения клиентов и приложений в новый экземпляр SQL Server.

