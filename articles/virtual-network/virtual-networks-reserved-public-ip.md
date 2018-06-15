---
title: Управление зарезервированными IP-адресами Azure (классическая модель) с помощью PowerShell | Документация Майкрософт
description: Сведения о зарезервированных IP-адресах (классическая модель) и управлении ими с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 25fe3c5361ff58f8d62d5d083b7a69f517d2a267
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798960"
---
# <a name="reserved-ip-addresses-classic"></a>Зарезервированные IP-адреса (классическая модель)

> [!div class="op_single_selector"]
> * [портал Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [интерфейс командной строки Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (классическая модель)](virtual-networks-reserved-public-ip.md)

IP-адреса в Azure делятся на две категории: динамические и зарезервированные. Общедоступные IP-адреса, управляемые Azure, являются динамическими по умолчанию. Это означает, что IP-адрес, используемый для указанной облачной службы (VIP) или для прямого доступа к виртуальной машине или экземпляру роли (ILPIP), время от времени может изменяться, при отключении или остановке (высвобождении) ресурсов.

Чтобы предотвратить изменение IP-адресов, можно зарезервировать IP-адрес. Зарезервированные IP-адреса можно использовать только в качестве виртуального IP-адреса (VIP). В этом случае IP-адрес облачной службы будет сохраняться даже при отключении или остановке (высвобождении) ресурсов. Кроме того, можно преобразовать существующие динамические IP-адреса, используемые в качестве виртуального IP-адреса, в зарезервированный IP-адрес.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Сведения о том, как зарезервировать статический общедоступный IP-адрес, используя модель развертывания с помощью Resource Manager, см. [здесь](virtual-network-ip-addresses-overview-arm.md).

Дополнительные сведения об IP-адресах в Azure см. в [этой статье](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Когда требуется зарезервированный IP-адрес?
* **Необходимо обеспечить резервирование IP-адреса в подписке**. Если вам нужен постоянный IP-адрес, который будет в любых обстоятельствах связан с подпиской, следует использовать зарезервированный общедоступный IP-адрес.  
* **Необходимо сохранять IP-адрес облачной службы даже в случае остановленного или высвобожденного состояния (виртуальных машин)**. Если требуется доступ к службе по IP-адресу, который не изменится, даже если виртуальные машины в облачной службе будут отключены или остановлены (высвобождены).
* **Необходимо обеспечить предсказуемый IP-адрес для исходящего трафика из Azure**. Возможно, настройки локального брандмауэра разрешают трафик только с определенных IP-адресов. Резервирование IP-адреса позволяет всегда знать IP-адрес источника и избавляет от необходимости изменять правила брандмауэра из-за смены адреса.

## <a name="faq"></a>Часто задаваемые вопросы
1. Можно ли использовать зарезервированный IP-адрес для всех служб Azure? <br>
    Нет. Зарезервированные IP-адреса можно использовать только для виртуальных машин и экземпляров ролей облачных служб, представляемых через виртуальный IP-адрес.
2. Сколько зарезервированных IP-адресов можно установить? <br>
    Дополнительные сведения см. в статье об [ограничениях в Azure](../azure-subscription-service-limits.md#networking-limits).
3. Взимается ли плата за зарезервированные IP-адреса? <br>
    Иногда. Сведения о ценах на зарезервированные IP-адреса см. на [этой странице](http://go.microsoft.com/fwlink/?LinkID=398482).
4. Как зарезервировать IP-адрес? <br>
    Чтобы зарезервировать IP-адрес в определенном регионе Azure, можно использовать PowerShell, [интерфейс REST API управления Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) или [портал Azure](https://portal.azure.com). Зарезервированный IP-адрес связывается с подпиской.
5. Можно ли использовать зарезервированный IP-адрес с виртуальными сетями на основе территориальной группы? <br>
    Нет. Зарезервированные IP-адреса поддерживаются только в региональных виртуальных сетях. Зарезервированные IP-адреса не поддерживаются для виртуальных сетей, которые связаны с территориальными группами. Дополнительные сведения о связывании виртуальной сети с регионом или территориальной группой см. в статье [Переход от территориальных групп к региональной виртуальной сети](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Управление зарезервированными виртуальными IP-адресами

Установите и настройте PowerShell, выполнив действия, описанные в [этой статье](/powershell/azure/overview). 

Прежде чем можно будет использовать зарезервированные IP-адреса, их необходимо добавить в подписку. Чтобы создать зарезервированный IP-адрес из пула общедоступных IP-адресов в *центральной части США*, выполните следующую команду:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Обратите внимание, однако, что нельзя указать, какой IP-адрес будет зарезервирован. Чтобы просмотреть, какие IP-адреса зарезервированы в подписке, выполните следующую команду PowerShell и обратите внимание на значения *ReservedIPName* и *Address*.

```powershell
Get-AzureReservedIP
```

Ожидаемые выходные данные:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Если вы создаете зарезервированный IP-адрес с помощью PowerShell, вы не можете указать группу ресурсов, в которой нужно создать зарезервированный IP-адрес. Azure по умолчанию помещает его в группу ресурсов с именем *Default-Networking*. Если вы создаете зарезервированный IP-адрес с помощью [портала Azure](http://portal.azure.com), вы можете указать для него любую группу ресурсов. Но если зарезервированный IP-адрес создается в любой другой группе ресурсов, кроме *Default-Networking*, все обращения к этому зарезервированному IP-адресу в любых командах, например `Get-AzureReservedIP` или `Remove-AzureReservedIP`, должны выполняться по полному имени *группа_имя_группы_ресурсов имя_зарезервированного_IP-адреса*.  Например, если вы создадите зарезервированный IP-адрес с именем *myReservedIP* в группе ресурсов с именем *myResourceGroup*, ссылки на него должны иметь такой вид: *Group myResourceGroup myReservedIP*.   

После того как IP-адрес будет зарезервирован, он останется связан с подпиской до тех пор, пока вы ее не удалите. Чтобы удалить зарезервированный IP-адрес, выполните следующую команду PowerShell:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Резервирование IP-адреса существующей облачной службы
Можно зарезервировать IP-адрес существующей облачной службы, добавив параметр `-ServiceName`. Чтобы зарезервировать IP-адрес облачной службы *TestService* в расположении *центральная часть США*, выполните следующую команду PowerShell:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Связывание зарезервированного IP-адреса с новой облачной службой
Приведенный ниже скрипт создает новый зарезервированный IP-адрес и связывает его с новой облачной службой с именем *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Если вы создадите зарезервированный IP-адрес для облачной службы, обращения к виртуальной машине для входящей связи по-прежнему выполняются в формате *виртуальный_IP-адрес:&lt;номер порта>*. Резервирование IP-адреса не означает возможность прямого подключения к виртуальной машине. Зарезервированный IP-адрес назначается облачной службе, в которой развернута виртуальная машина. Если требуется подключиться непосредственно к виртуальной машине по IP-адресу, следует настроить общедоступный IP-адрес уровня экземпляра. Общедоступный IP-адрес уровня экземпляра — это тип общедоступного IP-адреса (называемый ILPIP), который назначается непосредственно виртуальной машине. Его нельзя зарезервировать. Дополнительные сведения см. в статье [Общие сведения об общедоступных IP-адресах уровня экземпляра (классическая модель развертывания)](virtual-networks-instance-level-public-ip.md).
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Удаление зарезервированного IP-адреса из работающей развернутой системы
Чтобы удалить зарезервированный IP-адрес, добавленный в облачную службу, выполните следующую команду PowerShell:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> При удалении зарезервированного IP-адреса из работающей развернутой системы резервирование не удаляется из вашей подписки. IP-адрес просто освобождается для использования другим ресурсом в подписке.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Связывание зарезервированного IP-адреса с работающей развернутой системой
Следующие команды создают облачную службу с именем *TestService2*, к которой прикреплена новая виртуальная машина с именем *TestVM2*. Затем с этой облачной службой связывается существующий зарезервированный IP-адрес с именем *MyReservedIP*.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Связывание зарезервированного IP-адреса с облачной службой с помощью файла конфигурации службы
Зарезервированный IP-адрес можно также связать с облачной службой с помощью файла конфигурации службы (CSCFG). В приведенном ниже XML-коде показано, как настроить облачную службу для использования зарезервированного виртуального IP-адреса с именем *MyReservedIP*.

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Дополнительная информация
* Общие сведения об IP-адресах в классической модели развертывания см. в [этой статье](virtual-network-ip-addresses-overview-classic.md).
* Ознакомьтесь с информацией о [зарезервированных частных IP-адресах](virtual-networks-reserved-private-ip.md).
* Ознакомьтесь с информацией об [общедоступных IP-адресах уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md).

