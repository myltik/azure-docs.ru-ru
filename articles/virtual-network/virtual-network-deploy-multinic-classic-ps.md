<properties 
   pageTitle="Развертывание виртуальных машин по классической модели развертывания с несколькими сетевыми картами с использованием PowerShell | Microsoft Azure"
   description="Сведения о развертывании виртуальных машин по классической модели развертывания с несколькими сетевыми картами с использованием PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="telmos" />

#Развертывание виртуальных машин с несколькими сетевыми картами с использованием PowerShell (классическая модель)

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Так как на данный момент невозможно иметь несколько виртуальных машин с одной сетевой картой и несколько виртуальных машин с несколькими сетевыми картами в одной облачной службе, внутренние серверы необходимо реализовать в другой облачной службе по сравнению с остальными компонентами сценария. В приведенных ниже действиях используются следующие облачные службы: *IaaSStory* в качестве основной группы ресурсов и *IaaSStory-BackEnd* для внутренних серверов.

## Предварительные требования

Перед развертыванием внутренних серверов необходимо выполнить развертывание основной облачной службы со всеми ресурсами, необходимыми для этого сценария. Нужно создать по крайней мере виртуальную сеть с подсетью для внутреннего сервера. Чтобы узнать, как развертывать виртуальную сеть, см. статью [Создание виртуальной сети с использованием PowerShell](virtual-networks-create-vnet-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Развертывание внутренних виртуальных машин

Внутренние виртуальные машины зависят от создания ресурсов, перечисленных ниже.

- **Внутренняя подсеть**. Чтобы разделить трафик, серверы базы данных будут входить в отдельную подсеть. Приведенный ниже сценарий предполагает наличие этой подсети в виртуальной сети с именем *WTestVnet*.
- **Учетная запись хранения для дисков данных**. Для повышения производительности для дисков данных на серверах баз данных будет использоваться технология твердотельного накопителя (SSD), которая требует наличия учетной записи хранения класса Premium. Расположение Azure, в которое выполняется развертывание, должно поддерживать хранилище класса Premium.
- **Группа доступности**. Все серверы баз данных будут добавлены в одну группу доступности, чтобы гарантировать, что как минимум одна из виртуальных машин будет запущена и доступна во время обслуживания. 

### Шаг 1. Запуск сценария

Полный сценарий PowerShell можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.ps1). Чтобы изменить сценарий для работы в вашей среде, сделайте следующее:

1. Измените значения следующих переменных в зависимости от существующей группы ресурсов, развернутой в соответствии с инструкциями в разделе [Предварительные требования](#Prerequisites) выше.

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. Измените значения следующих переменных на основе значений, которые нужно использовать для внутреннего развертывания.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### Шаг 2. Создание необходимых ресурсов для виртуальных машин

Необходимо создать облачную службу и учетную запись хранения для дисков данных для всех виртуальных машин. Кроме того, для виртуальных машин необходимо указать образ и учетную запись локального администратора. Чтобы создать эти ресурсы, сделайте следующее:

1. Создайте облачную службу.

		New-AzureService -ServiceName $backendCSName -Location $location

2. Создайте учетную запись хранения класса Premium.

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. Задайте созданную ранее учетную запись хранения в качестве текущей учетной записи хранения для подписки.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. Выберите образ виртуальной машины.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. Задайте учетные данные пароля учетной записи локального администратора.

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### Шаг 3. Создание виртуальных машин

Необходимо использовать цикл, чтобы создать необходимое количество виртуальных машин, и создать в нем необходимые сетевые карты и виртуальные машины. Чтобы создать сетевые карты и виртуальные машины, сделайте следующее:

1. Запустите цикл `for` для повтора команды, которая позволяет создать виртуальную машину и две сетевые карты необходимое количество раз на основе значения переменной `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Создайте объект `VMConfig`, указывающий образ, размер и группу доступности для виртуальной машины.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. Подготовьте виртуальную машину в качестве виртуальной машины Windows.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. Задайте сетевую карту по умолчанию и назначьте ей статический IP-адрес.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Добавьте вторую сетевую карту для каждой виртуальной машины.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig 

6. Создайте по два диска данных для каждой виртуальной машины.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. Создайте каждую виртуальную машину и завершите цикл.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### Шаг 4. Запуск сценария

Теперь, когда вы скачали и изменили сценарий в соответствии со своими потребностями, запустите сценарий для создания виртуальных машин внутренней базы данных с несколькими сетевыми картами.

1. Сохраните сценарий и запустите его из командной строки **PowerShell** или **интегрированной среды сценариев PowerShell**. Вы увидите начальный вывод сценария, как показано ниже.

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		                                                                            
		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Укажите учетные данные и нажмите кнопку **ОК**. Отобразится вывод, как показано ниже.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 

<!---HONumber=Nov15_HO4-->