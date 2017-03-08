---
title: "Управление записями обратного преобразования DNS в службе DNS Azure с помощью PowerShell | Документация Майкрософт"
description: "Служба DNS Azure позволяет управлять записями обратного преобразования DNS или записями типа PTR для служб Azure с помощью PowerShell в Resource Manager."
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 78de3b8dd2d8bd0992bfbacb1079825dca486442
ms.lasthandoff: 02/10/2017


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>Управление записями обратного преобразования DNS для служб Azure с помощью PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Дополнительные сведения о классической модели развертывания см. в статье [Как управлять обратными записями DNS для служб Azure (классическая модель) с помощью Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Проверка обратных записей DNS

Чтобы гарантировать, что третья сторона не сможет создать сопоставление обратных записей DNS с вашими доменами DNS, Azure позволяет создавать только обратные записи DNS, для которых выполняется одно из следующих условий:

* ReverseFqdn совпадает с Fqdn ресурса общедоступного IP-адреса, для которого он был указан, либо с Fqdn любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — contosoapp1.northus.cloudapp.azure.com.
* При переадресации значение ReverseFqdn разрешается в имя или IP-адрес общедоступного IP-адреса, для которого оно было указано, либо в Fqdn или IP-адрес любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — app1.contoso.com, представляющий собой псевдоним CName для contosoapp1.northus.cloudapp.azure.com.

Проверки выполняются только в том случае, если задается или меняется свойство обратного DNS для общедоступного IP-адреса. Периодическая повторная проверка не выполняется.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Добавление обратного DNS для существующих общедоступных IP-адресов

Вы можете добавить обратный DNS для существующего общедоступного IP-адреса с помощью командлета `Set-AzureRmPublicIpAddress`:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Если вы хотите добавить обратный DNS для существующего общедоступного IP-адреса, у которого еще нет DNS-имени, необходимо также указать DNS-имя. Для этого можно использовать командлет Set-AzureRmPublicIpAddress:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Создание общедоступного IP-адреса с обратным DNS

Вы можете добавить новый общедоступный IP-адрес с заданным свойством обратного DNS с помощью командлета `New-AzureRmPublicIpAddress`:

```powershell
New-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Просмотр обратного DNS существующих общедоступных IP-адресов

Вы можете просмотреть значение, настроенное для существующего общедоступного IP-адреса, с помощью командлета `Get-AzureRmPublicIpAddress`:

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS"
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Удаление обратного DNS для существующих общедоступных IP-адресов

Вы можете удалить свойство обратного DNS для существующего общедоступного IP-адреса с помощью командлета `Set-AzureRmPublicIpAddress`. Для этого нужно указать пустое значение свойства ReverseFqdn.

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


