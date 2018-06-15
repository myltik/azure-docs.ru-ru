---
title: Общедоступные IP-адреса уровня экземпляра Azure (классическая модель) | Документация Майкрософт
description: Описание общедоступных IP-адресов уровня экземпляра (ILPIP-адресов) и инструкции по управлению ими с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 4b4350e6b1616450ce45f9e947cc3b639a341ae7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796026"
---
# <a name="instance-level-public-ip-classic-overview"></a>Общие сведения об общедоступных IP-адресах уровня экземпляра (классическая модель развертывания)
Общедоступный IP-адрес уровня экземпляра (ILPIP) — это общедоступный IP-адрес, который можно назначить непосредственно виртуальной машине или экземпляру роли облачных служб, а не облачной службе, в которой они находятся. Он не заменяет виртуальный IP-адрес (VIP), назначенный облачной службе. а представляет собой дополнительный IP-адрес, с помощью которого можно подключаться непосредственно к виртуальной машине или экземпляру роли.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). В этой статье рассматривается использование классической модели развертывания. Корпорация Майкрософт рекомендует создавать виртуальные машины с помощью Resource Manager. Убедитесь, что вы понимаете как работают в Azure [IP-адреса](virtual-network-ip-addresses-overview-classic.md) .

![Разница между ILPIP-адресом и виртуальным IP-адресом](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Как показано на рис. 1, для доступа к облачной службе используется виртуальный IP-адрес, а для обращения к отдельным виртуальным машинам, как правило, применяется формат "виртуальный IP-адрес:&lt;номер_порта&gt;". Назначив определенной виртуальной машине ILPIP-адрес, вы сможете обращаться к ней напрямую.

При создании облачной службы в среде Azure автоматически создаются соответствующие записи A DNS, благодаря которым к службе можно обращаться по полному доменному имени вместо ее фактического виртуального IP-адреса. То же самое выполняется и для ILPIP-адресов, благодаря чему к виртуальным машинам и экземплярам ролей можно обращаться по полному доменному имени, не указывая ILPIP-адрес. Например, если при создании облачной службы *contosoadservice* вы настраиваете веб-роль *contosoweb* с двумя экземплярами, то платформа Azure регистрирует следующие записи A для этих экземпляров:

* contosoweb\_IN_0.contosoadservice.cloudapp.net;
* contosoweb\_IN_1.contosoadservice.cloudapp.net. 

> [!NOTE]
> Каждой виртуальной машине или экземпляру роли можно назначить только один ILPIP-адрес. В одной подписке разрешается использовать до 5 ILPIP-адресов. ILPIP-адреса не поддерживаются для виртуальных машин с несколькими сетевыми картами.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Зачем запрашивать ILPIP-адрес?
Чтобы подключиться к виртуальной машине или экземпляру роли непосредственно по определенному IP-адресу, не используйте формат "виртуальный IP-адрес облачной службы:&lt;номер_порта&gt;", а вместо этого отправьте запрос на создание ILPIP-адреса для виртуальной машины или экземпляра роли.

* **Активный FTP-сервер**. Если назначить ILPIP-адрес виртуальной машине, она сможет получать трафик через любой порт. Ей не понадобятся конечные точки, чтобы получать трафик.  Дополнительные сведения о протоколе FTP см. в (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[обзоре протокола FTP].
* **Исходящий IP-трафик.** Исходящий трафик с виртуальной машины сопоставляется с ILPIP-адресом в качестве источника, который уникальным образом идентифицирует эту виртуальную машину для внешних сущностей.

> [!NOTE]
> Ранее ILPIP-адрес назывался общедоступным IP-адресом.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Управление ILPIP-адресом виртуальной машины
Ниже описано, как создавать, назначать и удалять ILPIP-адреса виртуальных машин.

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Как отправлять запрос на получение ILPIP-адреса на этапе создания виртуальной машины с помощью PowerShell
Приведенный ниже сценарий PowerShell создает облачную службу *FTPService*, получает образ из Azure, создает на его основе виртуальную машину *FTPInstance*, задает для нее ILPIP-адрес и добавляет эту виртуальную машину в новую службу.

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Получение сведений об ILPIP-адресе виртуальной машины
Чтобы узнать ILPIP-адрес виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду PowerShell и обратите внимание на значения *PublicIPAddress* и *PublicIPName*.

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Ожидаемые выходные данные:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Удаление ILPIP-адреса виртуальной машины
Чтобы удалить ILPIP-адрес, назначенный виртуальной машине в приведенном выше сценарии, выполните следующую команду PowerShell.

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Назначение ILPIP-адреса существующей виртуальной машине
Чтобы добавить ILPIP-адрес для виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду.

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Управление ILPIP-адресом экземпляра роли облачных служб

Чтобы добавить ILPIP-адрес для экземпляра роли облачных служб, выполните следующие действия.

1. Скачайте CSCFG-файл облачной службы, выполнив действия, описанные в статье [Настройка облачных служб](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).
2. Измените этот CSCFG-файл, добавив в него элемент `InstanceAddress`. Приведенный ниже пример добавляет ILPIP-адрес *MyPublicIP* для экземпляра роли *WebRole1*. 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Передайте CSCFG-файл облачной службы, выполнив действия, описанные в статье [Настройка облачных служб](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения об IP-адресах в классической модели развертывания см. в статье [IP-адреса в Azure (классическая модель развертывания)](virtual-network-ip-addresses-overview-classic.md).
* Общие сведения о зарезервированных IP-адресах см. в [этой статье](virtual-networks-reserved-public-ip.md).
