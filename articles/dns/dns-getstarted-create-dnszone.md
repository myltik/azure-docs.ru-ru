---
title: "Приступая к работе с Azure DNS | Документация Майкрософт"
description: "Сведения о создании зон DNS в Azure DNS. Это пошаговое руководство описывает создание первой зоны DNS и управление ею с помощью Azure PowerShell."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>Создание зоны DNS с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Эта статья поможет вам создать зону DNS с помощью Azure PowerShell. Вы также можете создать зону DNS с помощью кроссплатформенного [интерфейса командной строки Azure](dns-getstarted-create-dnszone-cli.md) или [портала Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью командлета `New-AzureRmDnsZone`. В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>Проверка зоны DNS

### <a name="view-records"></a>Просмотр записей

Одновременно с зоной DNS создаются следующие записи DNS:

* *Начальная запись зоны* (SOA). Она находится в корне каждой зоны DNS.
* Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут быть назначены различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Чтобы просмотреть эти записи, используйте `Get-AzureRmDnsRecordSet`.

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> В наборах записей в корне (или на *вершине*) зоны DNS в качестве имени набора записей используется **@** .

### <a name="test-name-servers"></a>Проверка серверов доменных имен

Наличие зоны DNS на серверах доменных имен Azure DNS можно проверить с помощью таких инструментов DNS, как nslookup, dig или [командлет PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов доменных имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды `Get-AzureRmDnsRecordSet` выше). Обязательно вставьте в следующую команду правильные значения для зоны:

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset.md), чтобы создать записи DNS для домена Интернета.

