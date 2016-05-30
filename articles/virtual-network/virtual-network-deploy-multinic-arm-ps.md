<properties
   pageTitle="Развертывание виртуальных машин с несколькими сетевыми картами с использованием PowerShell в диспетчере ресурсов | Microsoft Azure"
   description="Сведения о развертывании виртуальных машин с несколькими сетевыми картами с использованием PowerShell в диспетчере ресурсов"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Развертывание виртуальных машин с несколькими сетевыми картами с использованием PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

В настоящее время в одной группе ресурсов нельзя одновременно использовать виртуальные машины с одной сетевой картой и виртуальные машины с несколькими сетевыми картами. Поэтому необходимо реализовать внутренние серверы в группе ресурсов, отдельной от всех прочих компонентов в сценарии. В приведенных ниже действиях используются следующие группы ресурсов: *IaaSStory* в качестве основной группы ресурсов и *IaaSStory серверная* для внутренних серверов.

## Предварительные требования

Перед развертыванием внутренних серверов необходимо выполнить развертывание основной группы ресурсов со всеми ресурсами, необходимыми для этого сценария. Чтобы развернуть эти ресурсы, сделайте следующее:

1. Перейдите к [странице шаблона](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. На странице шаблона справа от **Родительская группа ресурсов** щелкните **Развернуть в Azure**.
3. При необходимости измените значения параметров, а затем следуйте инструкциям на портале предварительной версии Azure для развертывания группы ресурсов.

> [AZURE.IMPORTANT] Имена учетных записей хранения должны быть уникальными. Они не могут повторяться в Azure.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Развертывание внутренних виртуальных машин

Внутренние виртуальные машины зависят от создания ресурсов, перечисленных ниже.

- **Учетная запись хранения для дисков данных**. Для повышения производительности для дисков данных на серверах баз данных будет использоваться технология твердотельного накопителя (SSD), которая требует наличия учетной записи хранения класса Premium. Расположение Azure, в которое выполняется развертывание, должно поддерживать хранилище класса Premium.
- **Сетевые карты**. У каждой виртуальной машины будет две сетевые карты: одна для доступа к базе данных, другая — для управления.
- **Группа доступности**. Все серверы баз данных будут добавлены в одну группу доступности, чтобы гарантировать, что как минимум одна из виртуальных машин будет запущена и доступна во время обслуживания.  

### Шаг 1. Запуск сценария

Полный сценарий PowerShell можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Чтобы изменить сценарий для работы в вашей среде, сделайте следующее:

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Измените значения следующих переменных в зависимости от существующей группы ресурсов, развернутой в соответствии с инструкциями в разделе [Предварительные требования](#Prerequisites) выше.

		$existingRGName        = "IaaSStory"
		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"
		$remoteAccessNSGName   = "NSG-RemoteAccess"
		$stdStorageAccountName = "wtestvnetstoragestd"

2. Измените значения следующих переменных на основе значений, которые нужно использовать для внутреннего развертывания.

		$backendRGName         = "IaaSStory-Backend"
		$prmStorageAccountName = "wtestvnetstorageprm"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$publisher             = "MicrosoftSQLServer"
		$offer                 = "SQL2014SP1-WS2012R2"
		$sku                   = "Standard"
		$version               = "latest"
		$vmNamePrefix          = "DB"
		$osDiskPrefix          = "osdiskdb"
		$dataDiskPrefix        = "datadisk"
		$diskSize	           = "120"	
		$nicNamePrefix         = "NICDB"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

3. Получите существующие ресурсы, необходимые для развертывания.

		$vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
		$backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
		$remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
		$stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### Шаг 2. Создание необходимых ресурсов для виртуальных машин

Необходимо создать группу ресурсов, учетную запись хранения для дисков данных и группу доступности для всех виртуальных машин. Кроме того, для каждой виртуальной машины понадобятся учетные данные локального администратора. Чтобы создать эти ресурсы, сделайте следующее:

1. Создайте новую группу ресурсов.

		New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Создайте учетную запись хранения класса Premium в созданной ранее группе ресурсов.

		$prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
			-ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Создайте группу доступности.

		$avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Получите учетные данные локального администратора, которые будут использоваться для каждой виртуальной машины.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

### Шаг 3. Создание сетевых карт и внутренних виртуальных машин

Необходимо использовать цикл, чтобы создать необходимое количество виртуальных машин, и создать в нем необходимые сетевые карты и виртуальные машины. Чтобы создать сетевые карты и виртуальные машины, сделайте следующее:

1. Запустите цикл `for` для повтора команды, которая позволяет создать виртуальную машину и две сетевые карты необходимое количество раз на основе значения переменной `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Создайте сетевую карту, используемую для доступа к базе данных.

		    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
		    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
		    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Создайте сетевую карту, используемую для удаленного доступа. Обратите внимание, что с этой сетевой картой связана группа безопасности сети.

		    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
		    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
		    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
				-NetworkSecurityGroupId $remoteAccessNSG.Id

4. Создайте объект `vmConfig`.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Создайте по два диска данных на виртуальную машину. Обратите внимание, что диски данных находятся в созданной ранее учетной записи хранения класса Premium.

		    $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
		    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
				-VhdUri $data1VhdUri -CreateOption empty -Lun 0

		    $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
		    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
				-VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Настройте операционную систему и образ для виртуальной машины.

		    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Добавьте две созданные ранее сетевые карты в объект `vmConfig`.

		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Создайте диск операционной системы и виртуальную машину. Обратите внимание, что в конце цикла `for` стоит `}`.

		    $osDiskName = $vmName + "-" + $osDiskSuffix
		    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
		    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
		}

### Шаг 4. Запуск сценария

Теперь, когда вы скачали и изменили сценарий в соответствии со своими потребностями, запустите сценарий для создания виртуальных машин внутренней базы данных с несколькими сетевыми картами.

1. Сохраните сценарий и запустите его из командной строки **PowerShell** или **интегрированной среды сценариев PowerShell**. Вы увидите начальный вывод сценария, как показано ниже.

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *                  

		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Через несколько минут укажите учетные данные и нажмите кнопку **ОК**. Приведенный ниже вывод представляет одну виртуальную машину. Обратите внимание, что выполнение всего процесса заняло 8 минут.

		ResourceGroupName            :
		Id                           :
		Name                         : DB2
		Type                         :
		Location                     :
		Tags                         :
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   :
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 :
		InstanceViewText             : null
		NetworkProfile               :
		NetworkProfileText           : null
		OSProfile                    :
		OSProfileText                : null
		Plan                         :
		PlanText                     : null
		ProvisioningState            :
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1}
		NetworkInterfaceIDs          :
		RequestId                    :
		StatusCode                   : 0


		ResourceGroupName            :
		Id                           :
		Name                         : DB2
		Type                         :
		Location                     :
		Tags                         :
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   :
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 :
		InstanceViewText             : null
		NetworkProfile               :
		NetworkProfileText           : null
		OSProfile                    :
		OSProfileText                : null
		Plan                         :
		PlanText                     : null
		ProvisioningState            :
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   },
		                                   {
		                                     "Lun": 1,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-2",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
		NetworkInterfaceIDs          :
		RequestId                    :
		StatusCode                   : 0


		EndTime             : 10/30/2015 9:30:03 AM -08:00
		Error               :
		Output              :
		StartTime           : 10/30/2015 9:22:54 AM -08:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK

<!---HONumber=AcomDC_0518_2016-->