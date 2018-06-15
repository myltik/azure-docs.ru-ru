---
title: Приступая к работе со службой DNS Azure с помощью PowerShell | Документация Майкрософт
description: Узнайте, как создать зону и запись DNS в службе DNS Azure. Это пошаговое руководство описывает создание первых зоны и записи DNS, а также управление ими с помощью PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: 050111f4a5e8459e89d049ccb879b5079ff68527
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30175330"
---
# <a name="get-started-with-azure-dns-using-powershell"></a>Приступая к работе со службой DNS Azure с помощью PowerShell

> [!div class="op_single_selector"]
> * [портал Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Эта статья поможет вам создать свою первую зону и первую запись DNS с помощью Azure PowerShell. Эти действия также можно выполнить с помощью портала Azure или кроссплатформенного интерфейса командной строки Azure (Azure CLI). Azure DNS также поддерживает создание частных доменов. Пошаговые инструкции о том, как создать закрытую зону DNS и запись, см. в разделе [Приступая к работе с частными зонами Azure DNS с помощью PowerShell](private-dns-getstarted-powershell.md).

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Наконец, чтобы опубликовать зону DNS в Интернете, необходимо настроить серверы доменных имен для домена. Каждый из этих шагов описан ниже.

При выполнении этих инструкций предполагается, что вы уже установили Azure PowerShell и выполнили вход. Чтобы получить справку, см. статью [Как управлять зонами DNS с помощью PowerShell](dns-operations-dnszones.md).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создается группа ресурсов, которая будет включать эту зону DNS. Ниже показана команда для создания группы ресурсов.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью командлета `New-AzureRmDnsZone`. В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```
Теперь Azure DNS также поддерживает частные зоны DNS (сейчас в виде общедоступной предварительной версии).  Дополнительные сведения об использовании частных зон DNS см. в статье [Using Azure DNS for private domains](private-dns-overview.md) (Использование Azure DNS для частных доменов). Инструкции по созданию частной зоны DNS см. в статье [Get started with Azure DNS private zones using PowerShell](./private-dns-getstarted-powershell.md) (Начало работы с частными зонами Azure DNS с помощью PowerShell).

## <a name="create-a-dns-record"></a>Создание записи DNS

Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. В следующем примере создается запись с относительным именем www в зоне DNS contoso.com, в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — www.contoso.com. Тип записи — A, IP-адрес — 1.2.3.4, а срок жизни составляет 3600 секунд.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Сведения о других типах записей, наборах записей с несколькими записями, а также об изменении существующих записей см. в статье [Управление записями DNS в службе DNS Azure с помощью Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Обновление серверов доменных имен

Когда вы убедитесь, что зона и записи DNS настроены правильно, необходимо настроить доменное имя для использования серверов доменных имен службы DNS Azure. Это позволит другим пользователям в Интернете находить ваши записи DNS.

Для получения списка серверов доменных имен для определенной зоны используется командлет `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Эти серверы доменных имен необходимо настроить с помощью регистратора доменных имен (у которого было приобретено доменное имя). Регистратор предложит вам вариант настройки серверов доменных имен для домена. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы удалить все ресурсы, созданные при работе с этой статьей, сделайте следующее:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о службе DNS Azure см. в статье [Обзор Azure DNS](dns-overview.md).

Дополнительные сведения об управлении зонами DNS в службе DNS Azure см. в статье [Как управлять зонами DNS с помощью PowerShell](dns-operations-dnszones.md).

Дополнительные сведения об управлении записями DNS в службе DNS Azure см. в статье [Управление записями DNS в службе DNS Azure с помощью Azure PowerShell](dns-operations-recordsets.md).

