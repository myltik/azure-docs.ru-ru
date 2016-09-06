<properties
	pageTitle="Создание виртуальной машины в Azure с помощью PowerShell | Microsoft Azure"
	description="При помощи Azure PowerShell и Azure Resource Manager можно с легкостью создать виртуальную машину под управлением Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Создание виртуальной машины Windows с помощью Resource Manager и PowerShell

В этой статье показано, как быстро создать виртуальную машину Azure под управлением Windows Server и связанные с ней ресурсы с помощью [Resource Manager](../resource-group-overview.md) и PowerShell.

Все действия, описанные в этой статье, необходимы для создания виртуальной машины; их выполнение займет около 30 минут.

## Шаг 1. Установка Azure PowerShell

Сведения об установке последней версии Azure PowerShell, выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
        
## Шаг 2. Создание группы ресурсов

В первую очередь создается группа ресурсов.

1. Получите список доступных расположений, где можно создавать ресурсы.

	    Get-AzureRmLocation | sort Location | Select Location
        
    Вы увидите нечто вроде этого:
    
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus

2. Замените значение **$locName** расположением в списке. Создайте переменную.

        $locName = "centralus"
        
3. Замените значение **$rgName** именем новой группы ресурсов. Создайте переменную и группу ресурсов.

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Шаг 3. Создание учетной записи хранения

Для хранения файла виртуального жесткого диска, используемого создаваемой виртуальной машиной, необходима [учетная запись хранения](../storage/storage-introduction.md).

1. Замените значение **$stName** именем учетной записи хранения. Проверьте имя на уникальность.

        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName

    Если команда возвращает значение **True**, предложенное имя является уникальным в пределах Azure. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.
    
2. Теперь выполните команду создания учетной записи хранения.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName
        
## Шаг 4. Создание виртуальной сети

Все виртуальные машины входят в [виртуальную сеть](../virtual-network/virtual-networks-overview.md).

1. Замените значение **$subnetName** именем подсети. Создайте переменную и подсеть.
    	
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Замените значение **$vnetName** именем виртуальной сети. Создайте переменную и виртуальную сеть с подсетью.

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    Для приложения и среды следует использовать содержательные значения.
        
## Шаг 5. Создание общедоступного IP-адреса и сетевого интерфейса

Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Замените значение **$ipName** именем общедоступного IP-адреса. Создайте переменную и общедоступный IP-адрес.

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Замените значение **$nicName** именем сетевого интерфейса. Создайте переменную и сетевой интерфейс.

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Шаг 6. Создание виртуальной машины

Теперь, когда все элементы готовы, можно приступать к созданию виртуальной машины.

1. Выполните команду, задающую имя учетной записи администратора и пароль для виртуальной машины.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    Пароль должен содержать от 8 до 123 символов и включать по крайней мере три из перечисленных далее символов: одна строчная буква, одна заглавная буква, одно число и один специальный символ. См. дополнительную информацию о [требованиях к имени пользователя и паролю](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
        
2. Замените значение **$vmName** именем виртуальной машины. Создайте переменную и конфигурацию виртуальной машины.

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Список доступных размеров виртуальных машин см. в статье [Размеры виртуальных машин Windows в Azure](virtual-machines-windows-sizes.md).
    
3. Замените значение **$compName** именем компьютера для виртуальной машины. Создайте переменную и добавьте в конфигурацию сведения об операционной системе.

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Определите образ, который будет использоваться для подготовки виртуальной машины.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Дополнительную информацию о выборе образов, которые можно использовать, см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-windows-cli-ps-findimage.md).
        
5. Добавьте в конфигурацию созданный сетевой интерфейс.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Замените значение **$blobPath** путем и именем файла в хранилище, который будет использоваться виртуальным жестким диском. Файл виртуального жесткого диска обычно хранится в контейнере, например **vhds/WindowsVMosDisk.vhd**. Создайте переменные.

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Замените значение **$diskName** именем диска операционной системы. Создайте переменную и добавьте в конфигурацию сведения о диске.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Наконец создайте виртуальную машину.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    На портале Azure отобразится группа ресурсов со всеми ее ресурсами, а в окне PowerShell появятся сведения о состоянии успешного выполнения:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Дальнейшие действия

- При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении неполадок развертывания групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Узнайте, как управлять созданной виртуальной машиной, прочитав статью об [управлении виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).
- Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Создание виртуальной машины Windows с использованием шаблона Resource Manager](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_0831_2016-->