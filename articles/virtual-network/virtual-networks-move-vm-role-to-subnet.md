---
title: "Перемещение виртуальной машины или экземпляра роли в другую подсеть"
description: "Узнайте, как переместить виртуальные машины и экземпляры ролей в другую подсеть"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 264e02fa48486acd7a9701c497c4e1fa95a1ce4e


---
# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Перемещение виртуальной машины или экземпляра роли в другую подсеть
С помощью PowerShell можно переместить виртуальные машины из одной подсети в другую в той же виртуальной сети (VNet). Экземпляры ролей можно перемещать путем редактирования CSCFG вместо использования PowerShell.

> [!NOTE]
> В этой статье содержатся сведения, относящиеся только к классическим развертываниям Azure.
> 
> 

Зачем перемещать виртуальные машины в другую подсеть? Миграция между подсетями полезна в тех случаях, когда старая подсеть слишком мала и не может быть расширена из-за существующих работающих виртуальных машин в этой подсети. В этом случае можно создать другую подсеть большего размера и переместить виртуальные машины в новую подсеть, а затем после завершения миграции старую пустую подсеть можно удалить.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Перемещение виртуальной машины в другую подсеть
Чтобы переместить виртуальную машину, запустите командлет PowerShell Set-AzureSubnet, используя приведенный ниже пример в качестве шаблона. В приведенном ниже примере выполняется перемещение TestVM из текущей подсети в подсеть Subnet-2. Не забудьте отредактировать пример в соответствии с вашей средой. Обратите внимание, что при каждом выполнении командлета Update-AzureVM в рамках процедуры он будет перезапускать виртуальную машину в рамках процесса обновления.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Если для виртуальной машины задан статический внутренний частный IP-адрес, необходимо будет удалить эту настройку перед перемещением виртуальной машины в новую подсеть. В этом случае используйте следующую команду:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Перемещение экземпляра роли в другую подсеть
Чтобы переместить экземпляр роли, измените файл CSCFG. В приведенном ниже примере выполняется перемещение роли Role0 в виртуальной сети *VNETName* из текущей подсети в подсеть *Subnet-2*. Поскольку экземпляр роли уже развернут, достаточно изменить имя подсети = Subnet-2. Не забудьте отредактировать пример в соответствии с вашей средой.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 



<!--HONumber=Nov16_HO3-->


