---
title: "Создание зоны DNS с помощью интерфейса командной строки | Документация Майкрософт"
description: "Сведения о создании зон DNS в Azure DNS. Это пошаговое руководство описывает создание первой зоны DNS и управление ею с помощью интерфейса командной строки Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Создание зоны Azure DNS с помощью интерфейса командной строки

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-dnszone-cli.md)

В этой статье описано, как создать зону DNS с помощью кроссплатформенного интерфейса командной строки Azure, доступного для Windows, Mac и Linux. Зону DNS также можно создать с помощью [Azure PowerShell](dns-getstarted-create-dnszone.md) или [портала Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `azure network dns zone create`. Чтобы просмотреть справку для этой команды, введите `azure network dns zone create -h`.

В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Проверка зоны DNS

### <a name="view-records"></a>Просмотр записей

Одновременно с зоной DNS создаются следующие записи DNS:

* *Начальная запись зоны* (SOA). Она находится в корне каждой зоны DNS.
* Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут быть назначены различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Чтобы просмотреть эти записи, используйте `azure network dns-record-set list`.

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> В наборах записей в корне (или на *вершине*) зоны DNS в качестве имени набора записей используется **@** .

### <a name="test-name-servers"></a>Проверка серверов доменных имен

Зону DNS, представленную на серверах доменных имен Azure DNS, можно проверить с помощью таких средств DNS, как nslookup, dig или командлет PowerShell `Resolve-DnsName`.

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов доменных имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды `azure network dns record-set list`).

В следующем примере мы выполним запрос dig к домену contoso.com, используя серверы доменных имен, назначенные зоне DNS. Обязательно вставьте правильные значения для вашей зоны.

     > dig @ns1-01.azure-dns.com contoso.com
     
     <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
     Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
     flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
     WARNING: recursion requested but not available

     OPT PSEUDOSECTION:
     EDNS: version: 0, flags:; udp: 4000
      QUESTION SECTION:
    contoso.com.                        IN      A

     AUTHORITY SECTION:
    contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, [создайте набор записей и записи DNS](dns-getstarted-create-recordset-cli.md) в своей зоне.




<!--HONumber=Dec16_HO3-->


