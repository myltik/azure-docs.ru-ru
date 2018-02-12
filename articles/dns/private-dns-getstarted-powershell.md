---
title: "Приступая к работе с частными зонами службы Azure DNS с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создать частную зону и запись DNS в службе Azure DNS. Это пошаговое руководство описывает создание первой частной зоны и записи DNS, а также управление ими с помощью PowerShell."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 4613e152336eda7ce7cdc4c44b0c6b5e96abac10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Приступая к работе с частными зонами Azure DNS с помощью PowerShell

Эта статья поможет вам создать свою первую частную зону и первую запись DNS с помощью Azure PowerShell.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Чтобы опубликовать частную зону DNS в виртуальной сети, укажите список виртуальных сетей, которые могут разрешать записи в зоне.  Мы называем их сетями разрешения.  Кроме того, можно указать набор виртуальных сетей, для которых служба Azure DNS будет поддерживать записи имени узла всякий раз, когда виртуальная машина создается, меняет IP-адрес или удаляется.  Мы называем их сетями регистрации.

Так как эта функция в настоящее время находится в управляемой предварительной версии, будет предоставлен модуль PowerShell предварительной версии. Для этого отправьте сообщение электронное почты на адрес [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com).

## <a name="get-the-preview-powershell-module"></a>Получение предварительной версии модуля PowerShell

Чтобы создать частную зону DNS с помощью PowerShell, отправьте сообщение электронной почты на адрес [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com). После этого вы получите предварительную версию модуля PowerShell.

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создается группа ресурсов, которая будет включать эту зону DNS. Ниже показана команда для создания группы ресурсов.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью командлета `New-AzureRmDnsZone`. В следующем примере в группе ресурсов *MyResourceGroup* создается зона DNS *contoso.local*, которая будет доступна для виртуальной сети *MyAzureVnet*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

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

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы удалить все ресурсы, созданные при работе с этой статьей, сделайте следующее:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании частных зон DNS см. в статье [Using Azure DNS for private domains](private-dns-overview.md) (Использование Azure DNS для частных доменов).

Дополнительные сведения об управлении записями DNS в службе DNS Azure см. в статье [Управление записями DNS в службе DNS Azure с помощью Azure PowerShell](dns-operations-recordsets.md).

