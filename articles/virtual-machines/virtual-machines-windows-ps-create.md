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
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# Создание виртуальной машины Windows с помощью Resource Manager и PowerShell

В этой статье показано, как быстро создать виртуальную машину Azure под управлением Windows Server и связанные с ней ресурсы при помощи Resource Manager и PowerShell.

Процедура, описанная в этой статье, занимает около 30 минут.

## Шаг 1. Установка Azure PowerShell

Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и войти в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
        
## Шаг 2. Создание группы ресурсов

Все ресурсы должны развертываться в группе ресурсов. Дополнительные сведения см. в статье [Общие сведения об Azure Resource Manager](../resource-group-overview.md).

1. Получите список доступных расположений, где можно создавать ресурсы.

	    Get-AzureLocation | sort Name | Select Name

2. Замените значение **$locName** расположением из списка, например **Центральная часть США**. Создайте переменную.

        $locName = "location name"
        
3. Замените значение **$rgName** именем новой группы ресурсов. Создайте переменную и группу ресурсов.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Шаг 3. Создание учетной записи хранения

Для сохранения файла виртуального жесткого диска, связанного с создаваемой виртуальной машиной, необходима учетная запись хранения.

1. Замените значение **$stName** (только строчные буквы и цифры) именем учетной записи хранения. Проверьте имя на уникальность.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Если команда возвращает значение **False**, предложенное имя является уникальным.
    
2. Теперь выполните команду создания учетной записи хранения.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Шаг 4. Создание виртуальной сети

Все виртуальные машины необходимо связать с виртуальной сетью.

1. Замените значение **$subnetName** именем подсети. Создайте переменную и подсеть.
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Замените значение **$vnetName** именем виртуальной сети. Создайте переменную и виртуальную сеть с подсетью.

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## Шаг 5. Создание общедоступного IP-адреса и сетевого интерфейса

Для обеспечения связи с виртуальной машиной в виртуальной сети требуются общедоступный IP-адрес и сетевой интерфейс.

1. Замените значение **$ipName** именем общедоступного IP-адреса. Создайте переменную и общедоступный IP-адрес.

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Замените значение **$nicName** именем сетевого интерфейса. Создайте переменную и сетевой интерфейс.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Шаг 6. Создание виртуальной машины

Теперь, когда все элементы готовы, можно приступать к созданию виртуальной машины.

1. Выполните команду, задающую имя учетной записи администратора и пароль для виртуальной машины.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. Замените значение **$vmName** именем виртуальной машины. Создайте переменную и конфигурацию виртуальной машины.

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Список доступных размеров виртуальных машин см. в статье [Размеры виртуальных машин Windows в Azure](virtual-machines-windows-sizes.md).
    
3. Замените значение **$compName** именем компьютера виртуальной машины. Создайте переменную и добавьте в конфигурацию сведения об операционной системе.

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Определите образ, который будет использоваться для подготовки виртуальной машины.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Дополнительную информацию о выборе образов, которые можно использовать, см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-windows-cli-ps-findimage.md).
        
5. Добавьте в конфигурацию созданный сетевой интерфейс.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Замените значение **$blobPath** путем и именем файла в хранилище виртуального жесткого диска. Файл виртуального жесткого диска обычно хранится в контейнере, например vhds/WindowsVMosDisk.vhd. Создайте переменные.

        $blobPath = "vhd path and file name"
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

- При возникновении проблем с развертыванием ознакомьтесь со статьей [Устранение неполадок развертываний групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Узнайте, как управлять созданной виртуальной машиной, прочитав статью [Управление виртуальными машинами с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).
- Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Создание виртуальной машины Windows с использованием шаблона Resource Manager](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_0420_2016-->