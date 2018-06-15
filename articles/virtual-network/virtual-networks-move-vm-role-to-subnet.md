---
title: Перемещение (классической) виртуальной машины или экземпляра роли облачных служб в другую подсеть с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как переместить (классические) виртуальные машины и экземпляры ролей облачных служб в другую подсеть с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb1576b7b842537dbdc49e6b9d402ef47ad9fae9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792653"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Перемещение (классической) виртуальной машины или экземпляра роли облачных служб в другую подсеть с помощью PowerShell
С помощью PowerShell можно переместить (классические) виртуальные машины из одной подсети в другую в той же виртуальной сети (VNet). Экземпляры ролей можно перемещать путем редактирования файла CSCFG вместо использования PowerShell.

> [!NOTE]
> В этой статье описывается процесс перемещения виртуальных машин, развернутых только с помощью классической модели развертывания.
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
