---
title: Создание виртуальной машины со статическим общедоступным IP-адресом с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать виртуальную машину со статическим общедоступным IP-адресом с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Создание виртуальной машины со статическим общедоступным IP-адресом с помощью PowerShell

> [!div class="op_single_selector"]
> * [портал Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [интерфейс командной строки Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (классическая модель)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>Запуск сценария
Полный сценарий PowerShell можно скачать [здесь](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Чтобы изменить сценарий для работы в вашей среде, сделайте следующее:

Измените значения следующих переменных на основе значений, которые нужно использовать для своего развертывания. Приведенные ниже значения соответствуют сценарию, используемому в этой статье.

```powershell
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
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Создание необходимых ресурсов для виртуальных машин
Перед созданием виртуальной машины необходимо создать группу ресурсов, виртуальную сеть, общедоступный IP-адрес и сетевой адаптер для использования с виртуальной машиной.

1. Создайте новую группу ресурсов.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. Создайте виртуальную сеть и подсеть.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. Создайте ресурс общедоступного IP-адреса. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Создайте сетевой интерфейс (NIC) для виртуальной машины в подсети, созданной ранее, с помощью общедоступного IP-адреса. Обратите внимание на первый командлет, который извлекает виртуальную сеть из Azure. Это необходимо, так как команда `Set-AzureRmVirtualNetwork` была выполнена для изменения существующей виртуальной сети.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Создайте учетную запись хранения для размещения диска виртуальной машины с ОС.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Создание виртуальной машины
Теперь, когда имеются все необходимые ресурсы, можно создать новую виртуальную машину.

1. Создайте объект конфигурации для виртуальной машины.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. Получите учетные данные для учетной записи локального администратора виртуальной машины.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Создайте объект конфигурации виртуальной машины.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Установите образ операционной системы для виртуальной машины.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Настройте диск операционной системы.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Добавьте сетевой адаптер в виртуальную машину.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. Создайте виртуальную машину.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Сохраните файл скрипта.

## <a name="run-the-script"></a>Запуск сценария

После внесения необходимых изменений запустите предыдущий скрипт. Через несколько минут виртуальная машина будет создана.

## <a name="set-ip-addresses-within-the-operating-system"></a>Настройка IP-адресов в операционной системе

Никогда не следует вручную назначать общедоступный IP-адрес для виртуальной машины Azure в ее операционной системе. Не рекомендуем статически назначать виртуальной машине Azure частный IP-адрес в ее операционной системе за исключением ситуаций, когда это необходимо, например при [назначении нескольких IP-адресов виртуальной машине Windows](virtual-network-multiple-ip-addresses-powershell.md). Если вы будете вручную устанавливать частный IP-адрес в операционной системе, убедитесь, что он соответствует частному IP-адресу, назначенному [сетевому интерфейсу](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure. Иначе соединение с виртуальной машиной может быть потеряно. Ознакомьтесь с дополнительными сведениями о параметрах [частных IP-адресов](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Дополнительная информация

Виртуальная машина, созданная в этой статье, может принимать и передавать любой сетевой трафик. Вы можете определить правила безопасности, ограничивающие входящий и исходящий трафик в группе безопасности сети для сетевого интерфейса и (или) подсети. Дополнительные сведения о группах безопасности сети см. [в этой статье](security-overview.md).