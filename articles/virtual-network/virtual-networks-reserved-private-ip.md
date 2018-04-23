---
title: Настройка статического внутреннего частного IP-адреса для классической виртуальной машины Azure
description: Основные сведения о статических внутренних IP-адресах (DIP) и управлении ими
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.openlocfilehash: 1cdf33632c282a872d0eb83dd1a1b1c639fc14bd
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Как задать статический внутренний частный IP-адрес с помощью PowerShell (классическая модель)
В большинстве случаев для виртуальной машины не нужно указывать статический внутренний IP-адрес. Виртуальные машины в виртуальной сети будут автоматически получать внутренний IP-адрес из указанного вами диапазона. Однако в некоторых случаях указание статического IP-адреса для конкретной виртуальной машины имеет смысл. Например, если на виртуальной машине планируется запускать DNS или она будет контроллером домена. Статический внутренний IP-адрес остается у виртуальной машины даже при переходе в состояние остановки или отзыва. 

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний корпорация Майкрософт рекомендует [использовать модель развертывания с помощью Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Проверка доступности определенного IP-адреса
Чтобы проверить, доступен ли IP-адрес *10.0.0.7* в виртуальной сети с именем *TestVnet*, выполните следующую команду PowerShell и проверьте значение для *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Если требуется протестировать указанную выше команду в защищенной среде, следуйте указаниям из статьи [Создание (классической) виртуальной сети с помощью портала предварительной версии Azure](virtual-networks-create-vnet-classic-pportal.md), чтобы создать виртуальную сеть *TestVnet* и убедиться в том, что она использует адресное пространство *10.0.0.0/8*.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Указание статического внутреннего IP-адреса при создании виртуальной машины
Приведенный ниже сценарий PowerShell создает облачную службу *TestService*, затем получает образ из Azure, после чего создает виртуальную машину *TestVM* в новой облачной службе с использованием полученного образа, задает для этой виртуальной машины нахождение в подсети *Subnet-1* и устанавливает *10.0.0.7* статическим внутренним IP-адресом виртуальной машины.

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Получение сведений о статическом внутреннем IP-адресе виртуальной машины
Чтобы просмотреть сведения о статическом внутреннем IP-адресе виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду PowerShell и обратите внимание на значения *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Удаление статического внутреннего IP-адреса с виртуальной машины
Чтобы удалить статический внутренний IP-адрес, добавленный на виртуальную машину в приведенном выше сценарии, выполните следующую команду PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Добавление статического внутреннего IP-адреса для существующей виртуальной машины
Чтобы добавить статический внутренний IP-адрес для виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Дополнительная информация
[Зарезервированный IP-адрес](virtual-networks-reserved-public-ip.md)

[Общедоступный IP-адрес уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx)

