---
title: "Создание виртуальной машины SQL Server в Azure PowerShell (Resource Manager) | Документация Майкрософт"
description: "Содержит описание действий и сценарии PowerShell для создания виртуальной машины Azure на основе образа из коллекции образов виртуальных машин SQL Server."
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
ms.date: 01/17/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 7706fd453ef8be4ebe65b65da643ec38b7c18433


---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Подготовка виртуальной машины SQL Server к работе с помощью Azure PowerShell (в Resource Manager)
> [!div class="op_single_selector"]
> * [Портал](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Обзор
В этом руководстве показано, как создать одну виртуальную машину Azure, используя модель развертывания с помощью **Azure Resource Manager** и командлеты Azure PowerShell. Мы создадим одну виртуальную машину, используя один диск из образа в коллекции SQL. Мы также создадим новых поставщиков ресурсов хранения, сетевых и вычислительных ресурсов, которые будут использоваться виртуальной машиной. Вы можете использовать существующие поставщики для любых из этих ресурсов.

Версию этого раздела для классической модели см. в статье [Подготовка виртуальной машины SQL Server к работе с помощью Azure PowerShell (классическая модель)](../sqlclassic/virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется:

* Учетная запись Azure и подписка (еще до начала). Если у вас ее нет, зарегистрируйтесь, чтобы [воспользоваться бесплатной пробной версией](https://azure.microsoft.com/pricing/free-trial/).
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)1.4.0 или более поздней версии (в этом учебнике использовалась версия 1.5.0).
  * Чтобы получить сведения о версии, выполните команду **Get-Module Azure -ListAvailable**.

## <a name="configure-your-subscription"></a>Настройка подписки
Откройте Windows PowerShell и установите доступ к учетной записи Azure, выполнив указанный ниже командлет. Откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

    Add-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор подписки, под которым вы вошли в систему. Это подписка, в которой будут созданы ресурсы для этого руководства (если вы не перейдете к другой подписке). При наличии нескольких идентификаторов подписки выполните следующий командлет, чтобы получить список всех идентификаторов подписки:

    Get-AzureRmSubscription

Чтобы использовать другой идентификатор подписки, выполните следующий командлет с требуемым идентификатором подписки:

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Определение переменных образа
Чтобы упростить использование и понимание завершенного сценария из этого руководства, сначала мы определим количество переменных. Изменяйте значения параметров по своему усмотрению. Однако учитывайте при этом ограничения именования, связанные с длиной имени и специальными знаками.

### <a name="location-and-resource-group"></a>Расположение и группа ресурсов
Используйте две указанные ниже переменные, чтобы определить область данных и группу ресурсов, в которых будут создаваться другие ресурсы для виртуальной машины.

Измените их соответствующим образом, а затем выполните следующие командлеты, чтобы инициализировать эти переменные:

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Свойства хранилища
Используйте следующие переменные, чтобы определить учетную запись хранения и тип хранилища в виртуальной машине.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные. Обратите внимание, что в этом примере используется [хранилище уровня "Премиум"](../../../storage/storage-premium-storage.md), которое рекомендуется для рабочих нагрузок в рабочей среде. Дополнительные сведения об этом руководстве, а также другие рекомендации см. в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Свойства сети
Используйте указанные ниже переменные, чтобы определить сетевой интерфейс, метод распределения TCP/IP, имя виртуальной сети, имя виртуальной подсети, диапазон IP-адресов для виртуальной сети, диапазон IP-адресов для подсети и метку общедоступного доменного имени, которые будет использовать сеть на виртуальной машине.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"

### <a name="virtual-machine-properties"></a>Свойства виртуальной машины
Используйте указанные ниже переменные, чтобы определить имя виртуальной машины, имя компьютера, размер виртуальной машины и имя диска операционной системы для виртуальной машины.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Свойства образа
Используйте следующие переменные, чтобы определить образ, который будет применен для виртуальной машины. В этом примере используется образ SQL Server 2016 Enterprise.

Измените их соответствующим образом, а затем выполните командлет ниже, чтобы инициализировать эти переменные.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Вы можете получить полный список образов SQL Server, выполнив команду Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Кроме того, выполнив команду Get-AzureRmVMImageSku, можно получить сведения о единицах хранения, доступных для предложения. При выполнении следующей команды отображаются все SKU для предложения **SQL2014SP1-WS2012R2** .

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Если используется модель развертывания с помощью Resource Manager, первый создаваемый объект — группа ресурсов. Мы используем командлет [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx), чтобы создать группу ресурсов Azure и соответствующие ресурсы. При этом имя и расположение группы ресурсов определяются в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы создать группу ресурсов.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Виртуальной машине требуются ресурсы хранения для диска операционной системы, а также файлов данных и журналов SQL Server. Для упрощения мы создадим один диск для всех ресурсов. Позже можно будет подключить дополнительные диски, выполнив командлет [Add-Azure Disk](https://msdn.microsoft.com/library/azure/dn495252.aspx), чтобы поместить файлы данных и журналов SQL Server на выделенные диски. Мы выполним командлет [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx), чтобы создать стандартную учетную запись хранения в новой группе ресурсов. При этом имя учетной записи хранения, номер SKU хранилища и расположение определяются в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы создать учетную запись хранения.

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Виртуальной машине нужны сетевые ресурсы для подключения к сети.

* Каждой виртуальной машине требуется виртуальная сеть.
* Для виртуальной сети следует определить по крайней мере одну подсеть.
* Для сетевого интерфейса следует определить общедоступный или частный IP-адрес.

### <a name="create-a-virtual-network-subnet-configuration"></a>Создание конфигурации подсети в виртуальной сети
Сначала мы создадим конфигурацию подсети для нашей виртуальной сети. Для целей учебника мы создадим подсеть по умолчанию, используя командлет [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) . Имя подсети и префикс адреса для нашей конфигурации подсети в виртуальной сети определяются в переменных, инициализированных ранее.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства конфигурации подсети виртуальной сети. Однако это выходит за рамки данного руководства.
>
>

Выполните следующий командлет, чтобы создать конфигурацию виртуальной подсети.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Далее с помощью командлета [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) мы создадим виртуальную сеть. в новой группе ресурсов. При этом имя, расположение и префикс адреса определяются в переменных, инициализированных ранее. Кроме того, мы используем конфигурацию подсети, определенную на предыдущем шаге.

Выполните следующий командлет, чтобы создать виртуальную сеть.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Создание общедоступного IP-адреса
Теперь, определив виртуальную сеть, необходимо настроить IP-адрес для подключения к виртуальной машине. В этом руководстве мы создадим общедоступный IP-адрес с применением динамического предоставления IP-адресов, чтобы обеспечить подключение к Интернету. Мы используем командлет [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx), чтобы создать общедоступный IP-адрес в ранее созданной группе ресурсов. При этом имя, расположение, метод распределения и метка общедоступного имени DNS-домена определяются в переменных, инициализированных ранее.

> [!NOTE]
> Выполнив этот командлет, можно определить дополнительные свойства общедоступного IP-адреса. Однако это выходит за рамки данного руководства. Кроме того, можно создать частный или статический адрес, но это также выходит за рамки данного руководства.
>
>

Выполните следующий командлет, чтобы создать общедоступный IP-адрес.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Создание сетевого интерфейса
Теперь все готово для создания сетевого интерфейса, который будет использовать виртуальная машина. Мы используем командлет [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) , чтобы создать сетевой интерфейс в ранее созданной группе ресурсов с ранее определенными именем, расположением, подсетью и общедоступным IP-адресом.

Выполните следующий командлет, чтобы создать сетевой интерфейс.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Настройка объекта виртуальной машины
После определения ресурсов хранения и сетевых ресурсов можно определить вычислительные ресурсы для виртуальной машины. В рамках целей руководства мы укажем размер виртуальной машины, различные свойства операционной системы, ранее созданный сетевой интерфейс, определим хранилище BLOB-объектов, а затем укажем диск операционной системы.

### <a name="create-the-vm-object"></a>Создание виртуальной машины
Сначала мы укажем размер виртуальной машины. В этом руководстве мы указываем DS13. Мы используем командлет [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx), чтобы создать настраиваемый объект виртуальной машины. При этом имя и размер определяются в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы создать объект виртуальной машины.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Создание объекта учетных данных для хранения имени и пароля локального администратора
Чтобы задать свойства операционной системы для виртуальной машины, необходимо сначала предоставить учетные данные для учетной записи локального администратора в виде защищенной строки. Для этого мы используем командлет [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Выполните следующий командлет и в окне запроса учетных данных Windows PowerShell введите имя и пароль, которые будут использоваться для учетной записи локального администратора на виртуальной машине Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Определение свойств операционной системы для виртуальной машины
Теперь мы можем задать свойства операционной системы виртуальной машины. Мы используем командлет [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx), чтобы задать тип операционной системы Windows. Для этого нужно установить [агент виртуальной машины](../../virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Укажите, что командлет включает автоматическое обновление, и задайте имя виртуальной машины, имя компьютера и учетные данные в переменных, инициализированных ранее.

Выполните следующий командлет, чтобы задать свойства операционной системы для виртуальной машины.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Добавление сетевого интерфейса к виртуальной машине
Далее мы добавим созданный ранее сетевой интерфейс к виртуальной машине. Для этого мы используем командлет [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) и переменную сетевого интерфейса, определенную ранее.

Выполните следующий командлет, чтобы указать сетевой интерфейс для виртуальной машины.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Определение расположения хранилища BLOB-объектов для диска, используемого виртуальной машиной
Далее мы укажем расположение хранилища BLOB-объектов для диска, который будет использоваться в виртуальной машине, с определенными ранее переменными.

Выполните следующий командлет, чтобы задать расположение хранилища BLOB-объектов.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Определение свойств диска операционной системы для виртуальной машины
Далее мы зададим свойства диска операционной системы для виртуальной машины. Мы используем командлет [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) , чтобы указать, что для операционной системы виртуальной машины используется образ, настроить кэширование для чтения (так как SQL Server устанавливается на том же диске), а также определить имя виртуальной машины и диск операционной системы с использованием переменных, определенных ранее.

Выполните следующий командлет, чтобы задать свойства диска операционной системы для виртуальной машины.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Определение образа платформы для виртуальной машины
Последний этап конфигурации — определение образа платформы для виртуальной машины. В рамках этого руководства мы используем последний образ SQL Server 2016 CTP-версии. Мы используем командлет [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) , чтобы задать образ согласно значениям переменных, определенных ранее.

Выполните следующий командлет, чтобы указать образ платформы для виртуальной машины.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL
Теперь по завершении конфигурации все готово для создания виртуальной машины. Для этого мы используем командлет [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) и определенные переменные.

Выполните следующий командлет, чтобы создать виртуальную машину.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

Теперь виртуальная машина создана. Обратите внимание, что стандартная учетная запись хранения создается для диагностики загрузки, так как для диска виртуальной машины указана учетная запись хранилища класса Premium.

Теперь этот компьютер можно просмотреть на портале Azure, чтобы увидеть [его общедоступный IP-адрес и полное доменное имя](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>Пример сценария
Файл ниже содержит полный сценарий PowerShell для этого руководства. Предполагается, что вы уже настроили подписку Azure на использование с командами **Add-AzureRmAccount** и **Select-AzureRmSubscription**.

    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Дальнейшие действия
После создания виртуальной машины вы можете подключиться к ней, используя протокол удаленного рабочего и настроив подключение. Дополнительные сведения см. в статье [Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)](virtual-machines-windows-sql-connect.md).




<!--HONumber=Jan17_HO2-->


