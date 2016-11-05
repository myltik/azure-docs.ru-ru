---
title: Развертывание виртуальной Машины со статическим общедоступным IP-адресом в диспетчере ресурсов | Microsoft Docs
description: Дополнительные сведения о развертывании виртуальных машин со статическим общедоступным IP-адресом с помощью PowerShell в диспетчере ресурсов
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Развертывание виртуальной машины со статическим общедоступным IP-адресом с помощью PowerShell
[!INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Классическая модель развертывания.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Шаг 1. Запуск сценария
Полный сценарий PowerShell можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Чтобы изменить сценарий для работы в вашей среде, сделайте следующее:

1. Измените значения следующих переменных на основе значений, которые нужно использовать для своего развертывания. Значения ниже соответствуют сценарию, используемому в этом документе.
   
        # Set variables resource group
        $rgName                = "IaaSStory"
        $location              = "West US"
   
        # Set variables for VNet
        $vnetName              = "WTestVNet"
        $vnetPrefix            = "192.168.0.0/16"
        $subnetName            = "FrontEnd"
        $subnetPrefix          = "192.168.1.0/24"
   
        # Set variables for storage
        $stdStorageAccountName = "iaasstorystorage"
   
        # Set variables for VM
        $vmSize                = "Standard_A1"
        $diskSize              = 127
        $publisher             = "MicrosoftWindowsServer"
        $offer                 = "WindowsServer"
        $sku                   = "2012-R2-Datacenter"
        $version               = "latest"
        $vmName                = "WEB1"
        $osDiskName            = "osdisk"
        $nicName               = "NICWEB1"
        $privateIPAddress      = "192.168.1.101"
        $pipName               = "PIPWEB1"
        $dnsName               = "iaasstoryws1"

## Шаг 2. Создание необходимых ресурсов для виртуальных машин
Перед созданием виртуальной машины необходимо создать группу ресурсов, виртуальную сеть, общедоступный IP-адрес и сетевой адаптер для использования с виртуальной машиной.

1. Создайте новую группу ресурсов.
   
        New-AzureRmResourceGroup -Name $rgName -Location $location
2. Создайте виртуальную сеть и подсеть.
   
        $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
            -AddressPrefix $vnetPrefix -Location $location   
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
            -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 
3. Создайте ресурс общедоступного IP-адреса.
   
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
            -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
4. Создайте сетевой интерфейс (NIC) для виртуальной машины в подсети, созданной ранее, с помощью общедоступного IP-адреса. Обратите внимание на первый командлет, который извлекает виртуальную сеть из Azure. Это необходимо, поскольку команда **Set-AzureRmVirtualNetwork** была выполнена для изменения существующей виртуальной сети.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
            -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
            -PublicIpAddress $pip
5. Создайте учетную запись хранения для размещения диска виртуальной машины с ОС.
   
        $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
            -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## Шаг 3. Создание виртуальной машины
Теперь, когда имеются все необходимые ресурсы, можно создать новую виртуальную машину.

1. Создайте объект конфигурации для виртуальной машины.
   
        $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 
2. Получите учетные данные для учетной записи локального администратора виртуальной машины.
   
        $cred = Get-Credential -Message "Type the name and password for the local administrator account."
3. Создайте объект конфигурации виртуальной машины.
   
        $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
            -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
4. Установите образ операционной системы для виртуальной машины.
   
        $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
            -Offer $offer -Skus $sku -Version $version
5. Настройте диск операционной системы.
   
        $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
        $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
6. Добавьте сетевой адаптер в виртуальную машину.
   
        $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
7. Создайте виртуальную машину.
   
        New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
8. Сохраните файл скрипта.

## Шаг 4. Запуск сценария
После внесения необходимых изменений и изучения указанного выше скрипта запустите сценарий.

1. В консоли PowerShell или PowerShell ISE выполните приведенный выше сценарий.
2. Представленные ниже выходные данные должны отобразиться через несколько минут.
   
        ResourceGroupName : IaaSStory
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
   
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {}
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "AddressPrefix": "192.168.1.0/24"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
   
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {
                              "DnsServers": []
                            }
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "ProvisioningState": "Succeeded"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
   
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Status              : Succeeded
        StatusCode          : OK
        Output              : 
        StartTime           : 1/12/2016 12:57:56 PM -08:00
        EndTime             : 1/12/2016 12:59:13 PM -08:00
        Error               : 
        ErrorText           : 

<!---HONumber=AcomDC_0810_2016-->