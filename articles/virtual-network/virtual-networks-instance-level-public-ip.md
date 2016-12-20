---
title: "Управление общедоступными IP-адресами уровня экземпляра (классическая модель развертывания) с помощью PowerShell | Документация Майкрософт"
description: "Общие сведения об общедоступных IP-адресах уровня экземпляра и о том, как управлять ими с помощью PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: f1a4dc135721a1c2a134806fbae031ee60e23074
ms.openlocfilehash: 1aa0990f53059fa832100f9fcf88f93633ca93ce


---
# <a name="instance-level-public-ip-classic-overview"></a>Общие сведения об общедоступных IP-адресах уровня экземпляра (классическая модель развертывания)
Общедоступный IP-адрес уровня экземпляра (ILPIP) — это общедоступный IP-адрес, который можно назначить непосредственно виртуальной машине или экземпляру роли, а не облачной службе, в которой они находятся. Он не заменяет виртуальный IP-адрес, назначенный облачной службе. а представляет собой дополнительный IP-адрес, с помощью которого можно подключаться непосредственно к виртуальной машине или экземпляру роли.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний мы рекомендуем использовать модель развертывания с помощью Azure Resource Manager. Убедитесь, что вы понимаете как работают в Azure [IP-адреса](virtual-network-ip-addresses-overview-classic.md) .

![Разница между ILPIP-адресом и виртуальным IP-адресом](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Как показано на рис. 1, для доступа к облачной службе используется виртуальный IP-адрес, а для обращения к отдельным виртуальным машинам, как правило, применяется формат "виртуальный IP-адрес:&lt;номер_порта&gt;". Назначив определенной виртуальной машине ILPIP-адрес, вы сможете обращаться к ней напрямую.

При создании облачной службы в среде Azure автоматически создаются соответствующие A-записи DNS, благодаря которым к службе можно обращаться по полному доменному имени вместо ее фактического виртуального IP-адреса. То же самое выполняется и для ILPIP-адресов, благодаря чему к виртуальным машинам и экземплярам ролей можно обращаться по полному доменному имени, не указывая ILPIP-адрес. Например, при создании облачной службы с именем *contosoadservice* вы настраиваете веб-роль *contosoweb* с двумя экземплярами, а служба Azure регистрирует следующие записи A для экземпляров:

* contosoweb\_IN_0.contosoadservice.cloudapp.net;
* contosoweb\_IN_1.contosoadservice.cloudapp.net. 

> [!NOTE]
> Каждой виртуальной машине или экземпляру роли можно назначить только один ILPIP-адрес. В одной подписке разрешается использовать до пяти ILPIP-адресов. В настоящее время ILPIP-адреса не поддерживаются для виртуальных машин с несколькими сетевыми адаптерами.
> 
> 

## <a name="why-should-i-request-an-ilpip"></a>Зачем запрашивать ILPIP-адрес?
Чтобы подключиться к виртуальной машине или экземпляру роли непосредственно по определенному IP-адресу, не используйте формат "виртуальный IP-адрес облачной службы:&lt;номер_порта&gt;", а вместо этого отправьте запрос на создание ILPIP-адреса для виртуальной машины или экземпляра роли.

* **Пассивный FTP.** Создав ILPIP-адрес для виртуальной машины, вы сможете принимать трафик практически на любой порт, не открывая для этого конечную точку. Благодаря этому можно, например, создать пассивный FTP-сервер, порты на котором выбираются динамически.
* **Внешний IP-адрес.** Исходящий трафик с виртуальной машины отправляется с ILPIP-адресом в качестве источника, который уникальным образом идентифицирует эту машину для внешних объектов.

> [!NOTE]
> Ранее ILPIP-адрес назывался общедоступным IP-адресом.
> 

## <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Как отправлять запрос на получение ILPIP-адреса на этапе создания виртуальной машины с помощью PowerShell
Приведенный ниже сценарий PowerShell создает облачную службу *FTPService*, загружает образ из среды Azure, создает на его основе виртуальную машину *FTPInstance*, задает для нее ILPIP-адрес и добавляет эту машину в новую службу.

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Получение сведений об ILPIP-адресе виртуальной машины
Чтобы узнать ILPIP-адрес виртуальной машины, созданной с помощью приведенного выше сценария, выполните следующую команду PowerShell и обратите внимание на значения *PublicIPAddress* и *PublicIPName*:

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

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Удаление ILPIP-адреса виртуальной машины
Чтобы удалить ILPIP-адрес, назначенный виртуальной машине в приведенном выше скрипте, выполните следующую команду PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Назначение ILPIP-адреса существующей виртуальной машине
Чтобы назначить ILPIP-адрес виртуальной машине, созданной с помощью приведенного выше сценария, выполните следующую команду:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Связывание ILPIP-адреса с виртуальной машиной с помощью файла конфигурации службы
Чтобы связать ILPIP-адрес с виртуальной машиной, также можно воспользоваться файлом конфигурации службы (CSCFG-файлом). В приведенном ниже XML-коде показано, как настроить облачную службу для использования ILPIP-адреса с именем *MyPublicIP* для экземпляра роли. 

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения об IP-адресах в классической модели развертывания см. в статье [IP-адреса в Azure (классическая модель развертывания)](virtual-network-ip-addresses-overview-classic.md).
* Общие сведения о зарезервированных IP-адресах см. в [этой статье](virtual-networks-reserved-public-ip.md).




<!--HONumber=Nov16_HO3-->


