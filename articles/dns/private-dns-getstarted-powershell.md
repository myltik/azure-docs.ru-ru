---
title: Приступая к работе с частными зонами службы Azure DNS с помощью PowerShell | Документация Майкрософт
description: Узнайте, как создать частную зону и запись DNS в службе Azure DNS. Это пошаговое руководство описывает создание первой частной зоны и записи DNS, а также управление ими с помощью PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30177660"
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Приступая к работе с частными зонами Azure DNS с помощью PowerShell

Эта статья поможет вам создать свою первую частную зону и первую запись DNS с помощью Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Чтобы опубликовать частную зону DNS в виртуальной сети, укажите список виртуальных сетей, которые могут разрешать записи в зоне.  Мы называем их виртуальными сетями разрешения.  Кроме того, можно указать виртуальные сети, для которых служба Azure DNS будет поддерживать записи имени узла всякий раз, когда виртуальная машина создается, меняет IP-адрес или удаляется.  Мы называем это виртуальной сетью регистрации.

# <a name="get-the-preview-powershell-modules"></a>Получение предварительной версии модулей PowerShell
При выполнении этих инструкций предполагается, что вы установили Azure PowerShell и выполнили вход, а также установили необходимые модули для частной зоны. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создается группа ресурсов, которая будет включать эту зону DNS. В следующем примере показана команда для создания группы ресурсов.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Создание частной зоны DNS

Чтобы создать зону DNS, используйте командлет `New-AzureRmDnsZone` и установите для параметра ZoneType значение Private. В следующем примере в группе ресурсов *MyResourceGroup* создается зона DNS *contoso.local*, которая будет доступна для виртуальной сети *MyAzureVnet*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

Обратите внимание: если не указать параметр ZoneType, будет создана общедоступная зона. Поэтому этот параметр является обязательным, если вам нужно создать частную зону. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Если необходимо, чтобы в Azure автоматически создавались записи имени узла в зоне, используйте параметр *RegistrationVirtualNetworkId* вместо *ResolutionVirtualNetworkId*.  Зарегистрированные виртуальные сети автоматически могут разрешать записи.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>Создание записи DNS

Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. В следующем примере создается запись с относительным именем db в зоне DNS contoso.local в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — db.contoso.local. Тип записи — A, IP-адрес — 10.0.0.4, а срок жизни составляет 3600 секунд.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Сведения о других типах записей, наборах записей с несколькими записями, а также об изменении существующих записей см. в статье [Управление записями DNS в службе DNS Azure с помощью Azure PowerShell](dns-operations-recordsets.md). 

## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Список частных зон DNS

Если опустить имя зоны в командлете `Get-AzureRmDnsZone`, можно получить список всех зон в группе ресурсов. Эта операция возвращает массив объектов зоны.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Если опустить имя зоны и имя группы ресурсов в командлете `Get-AzureRmDnsZone`, то можно получить список всех зон в подписке Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Обновление частной зоны DNS

Изменить ресурс зоны DNS можно с помощью командлета `Set-AzureRmDnsZone`. При этом ни один набор записей DNS в пределах зоны командлетом не обновляется (см. статью [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. Сейчас к записываемым свойствам зоны относятся только [теги Azure Resource Manager для ресурса зоны](dns-zones-records.md#tags) и параметры RegistrationVirtualNetworkId и ResolutionVirtualNetworkId для частных зон.

В примере ниже виртуальная сеть регистрации, связанная с зоной, заменяется на новую сеть MyNewAzureVnet.

Обратите внимание, что для обновления не нужно указывать параметр ZoneType, в отличие от создания. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

В примере ниже виртуальная сеть разрешения, связанная с зоной, заменяется новой сетью MyNewAzureVnet.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Удаление частной зоны DNS

Частные зоны DNS, как и общедоступные зоны, можно удалить с помощью командлета `Remove-AzureRmDnsZone`.

> [!NOTE]
> При удалении зоны DNS также удаляются все записи DNS в этой зоне. Эту операцию нельзя отменить. Если зона DNS используется, то после ее удаления произойдет сбой служб, которые ее используют.
>
>Сведения о защите от случайного зоны удаления см. в разделе [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Как защитить зоны и записи DNS).

Используйте один из двух описанных ниже способов удаления зоны DNS.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Укажите зону с помощью имени зоны и имени группы ресурсов.

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Укажите зону с помощью объекта $zone.

Удаляемую зону можно указать с помощью объекта `$zone`, который возвращается командлетом `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Объект зоны также можно направить в конвейер, а не передавать в качестве параметра:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Запросы на подтверждение

Командлеты `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` и `Remove-AzureRmDnsZone` поддерживают запросы на подтверждение.

Оба командлета (`New-AzureRmDnsZone` и `Set-AzureRmDnsZone`) запрашивают подтверждение, если привилегированная переменная `$ConfirmPreference` в PowerShell имеет значение `Medium` или ниже. Из-за того, что удаление зоны DNS может иметь значительные последствия, командлет `Remove-AzureRmDnsZone` запрашивает подтверждение, если переменная PowerShell `$ConfirmPreference` имеет любое значение, отличное от `None`.

Поскольку переменная `$ConfirmPreference` по умолчанию имеет значение `High`, то по умолчанию подтверждение запрашивается только командлетом `Remove-AzureRmDnsZone`.

Текущее значение `$ConfirmPreference` можно переопределить с помощью параметра `-Confirm`. Если вы определите `-Confirm` или `-Confirm:$True`, командлет будет запрашивать подтверждение перед выполнением. Если вы определите `-Confirm:$False`, командлет не будет запрашивать подтверждение.

Дополнительные сведения об элементах `-Confirm` и `$ConfirmPreference` см. в статье о [привилегированных переменных](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).


## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы удалить все ресурсы, созданные при работе с этой статьей, сделайте следующее:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании частных зон DNS см. в статье [Using Azure DNS for private domains](private-dns-overview.md) (Использование Azure DNS для частных доменов).

Ознакомьтесь с распространенными [сценариями для частных зон](./private-dns-scenarios.md), которые можно реализовать с использованием частных зон в Azure DNS.

Дополнительные сведения об управлении записями DNS в службе DNS Azure см. в статье [Управление записями DNS в службе DNS Azure с помощью Azure PowerShell](dns-operations-recordsets.md).

