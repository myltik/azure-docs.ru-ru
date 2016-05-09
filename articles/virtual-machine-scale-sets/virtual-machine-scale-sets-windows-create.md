<properties
	pageTitle="Создание набора масштабирования виртуальных машин | Microsoft Azure"
	description="Создание набора масштабирования виртуальных машин с помощью Powershell"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Создание набора масштабирования виртуальных машин Windows с помощью Azure PowerShell

Материал в этих шагах для создания набора масштабирования виртуальных машин Azure представлен таким образом, что достаточно лишь заполнить пробелы. В статье встречаются переменные, значения которых вам необходимо указать. Замените текст в кавычках на значения, соответствующие вашей подписке и приложению.

## Шаг 1. Установка Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Шаг 2. Настройка подписки

1. Запустите командную строку PowerShell.

2. Войдите в свою учетную запись.

        Login-AzureRmAccount

3. Получите свою подписку.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. Задайте подписку, которую нужно использовать в качестве текущей.

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## Шаг 2. Создание ресурсов

Создайте ресурсы, которые необходимы для вашего нового набора масштабирования виртуальных машин.

### Группа ресурсов

Набор масштабирования виртуальных машин должен содержаться в группе ресурсов.

1.  Чтобы получить список доступных расположений и поддерживаемых служб, выполните следующую команду:

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Вы увидите нечто вроде этого:

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    Выберите наиболее подходящее расположение, а затем замените текст в кавычках на название этого расположения.

        $locName = "location name from the list, such as Central US"

4. Замените текст в кавычках на имя, которое вы хотите использовать для новой группы ресурсов, а затем создайте ее в расположении, заданном ранее.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Вы увидите нечто вроде этого:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Учетная запись хранения

Для хранения связанных дисков виртуальным машинам, создаваемым в наборе масштабирования, требуется учетная запись хранения.

1. Замените текст в кавычках на имя, которое нужно использовать для учетной записи хранения, а затем проверьте, является ли оно уникальным.

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    Если команда возвращает значение **False**, предложенное имя является уникальным.

2. Замените текст в кавычках на тип учетной записи хранения, а затем создайте учетную запись с ранее заданными именем и расположением. Возможные значения: Standard\_LRS, Standard\_GRS, Standard\_RAGRS и Premium\_LRS.

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Вы увидите нечто вроде этого:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### Виртуальная сеть

Виртуальная сеть обязательна для виртуальных машин в наборе масштабирования.

1. Замените текст в кавычках на имя, которое вы хотите использовать для подсети в виртуальной сети, а затем создайте конфигурацию.

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. Замените текст в кавычках на имя, которое вы хотите использовать для виртуальной сети, а затем создайте ее, используя ранее определенные сведения и ресурсы.

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Общедоступный IP-адрес

Перед тем как создавать сетевой интерфейс, необходимо создать общедоступный IP-адрес.

1. Замените текст в кавычках на доменное имя, которое хотите использовать с общедоступным IP-адресом, а затем проверьте, является ли оно уникальным. Доменное имя может содержать только буквы, цифры и дефисы. Последним символом должна быть буква или цифра.

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Если команда возвращает значение **False**, предложенное имя является уникальным.

2. Замените текст в кавычках на имя, которое хотите использовать для общедоступного IP-адреса, а затем создайте его.

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Сетевой интерфейс

Теперь, когда есть общедоступный IP-адрес, вы можете создать сетевой интерфейс.

1. Замените текст в кавычках на имя, которое вы хотите использовать для сетевого интерфейса, а затем создайте его, используя ранее созданные ресурсы.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### Создание набора масштабирования виртуальных машин

Теперь, когда у вас есть все необходимые ресурсы, можно создать набор масштабирования.

1. Замените текст в кавычках на имя, которое хотите использовать для конфигурации IP, а затем создайте ее.

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Замените текст в кавычках на имя, которое хотите использовать для конфигурации набора масштабирования, а затем создайте ее. Этот шаг включает задание размера (SkuName) виртуальных машин в наборе. Чтобы подобрать размер в соответствии со своими потребностями, обратитесь к статье [Размеры виртуальных машин](..\virtual-machines\virtual-machines-windows-sizes.md). В этом примере рекомендуется использовать размер Standard\_A0.

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    Вы увидите нечто вроде этого:

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. Замените текст в кавычках на нужный префикс имени компьютера, имя учетной записи администратора виртуальных машин и пароль к учетной записи, а затем создайте профиль операционной системы.

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    В разделе osProfile вы должны увидеть нечто наподобие этого:

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. Замените текст в кавычках на имя, которое хотите использовать для профиля хранилища, сведения об образе и путь к месту в хранилище, где хранятся диски виртуальных машин, а затем создайте профиль. Необходимую информацию см. в статье [Просмотр и выбор образов виртуальных машин в Azure с помощью интерфейса командной строки или оболочки PowerShell](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md).

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    В разделе storageProfile вы увидите нечто вроде этого:

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. Замените текст в кавычках на имя набора масштабирования виртуальных машин и создайте его.

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Если развертывание прошло успешно, вы увидите нечто вроде этого:

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## Шаг 3. Изучение ресурсов

Для изучения созданного набора масштабирования виртуальных машин используйте указанные ниже ресурсы.

- Портал Azure — с помощью портала можно получить ограниченный объем сведений.
- [Обозреватель ресурсов Azure](https://resources.azure.com/) — это лучшее средство для просмотра текущего состояния набора масштабирования.
- Azure PowerShell — используйте следующую команду, чтобы получить сведения:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## Дальнейшие действия

1. Дополнительные сведения см. в статье [Обзор наборов для масштабирования виртуальных машин](virtual-machine-scale-sets-overview.md).

2. Вы можете настроить автоматическое масштабирование набора масштабирования с помощью сведений в статье [Автоматическое масштабирование и наборы масштабирования виртуальных машин](virtual-machine-scale-sets-autoscale-overview.md).

<!---HONumber=AcomDC_0427_2016-->