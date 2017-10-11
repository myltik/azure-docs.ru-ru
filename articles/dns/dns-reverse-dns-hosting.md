---
title: "Размещение зон обратного просмотра DNS в Azure DNS | Документация Майкрософт"
description: "Узнайте, как использовать Azure DNS, чтобы разместить зоны обратного просмотра DNS для диапазонов IP-адресов"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Размещение зон обратного просмотра DNS в Azure DNS

В этой статье описано размещение зон обратного просмотра DNS для назначенных диапазонов IP-адресов в Azure DNS. Диапазоны IP-адресов, представленные зоной обратного просмотра, должны быть назначены организации (как правило, поставщиком услуг Интернета).

Чтобы настроить обратные зоны DNS для принадлежащего Azure IP-адреса, назначенного службе Azure, см. раздел о [настройке обратного просмотра для IP-адресов, выделенных службе Azure](dns-reverse-dns-for-azure-services.md).

Перед прочтением этой статьи ознакомьтесь со статьей [Основные сведения об обратной зоне DNS и ее поддержке в Azure](dns-reverse-dns-overview.md).

Эта статья поможет вам создать свою первую зону обратного просмотра и обратную запись DNS с помощью портала Azure, Azure PowerShell, Azure CLI 1.0 или Azure CLI 2.0.

## <a name="create-a-reverse-lookup-dns-zone"></a>Создание зоны обратного просмотра DNS

1. Войдите на [портал Azure](https://portal.azure.com)
1. В главном меню последовательно выберите **Создать** > **Сети**, а затем щелкните **Зона DNS**, чтобы открыть колонку **Создание зоны DNS**.

   ![Зона DNS](./media/dns-reverse-dns-hosting/figure1.png)

1. В колонке **Создание зоны DNS** укажите имя зоны DNS. Для префиксов IPv4 и IPv6 имя зоны формируется по-разному. Чтобы указать имя зоны, выполните инструкции для [IPv4](#ipv4) или [IPv6](#ipv6). По завершении нажмите кнопку **Создать**, чтобы создать зону.

### <a name="ipv4"></a>IPv4

Имя зоны обратного просмотра IPv4 формируется на основе диапазона IP-адресов, который она представляет. Имя должно быть указано в формате `<IPv4 network prefix in reverse order>.in-addr.arpa`. Примеры см. в [обзоре записей в обратной зоне DNS и поддержки в Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Создавая бесклассовую зону обратного просмотра DNS в Azure DNS, в имени зоны необходимо использовать дефис (`-`) вместо косой черты (/).
>
> Например, для диапазона IP-адресов 192.0.2.128/26 в качестве имени зоны нужно использовать `128-26.2.0.192.in-addr.arpa`, а не `128/26.2.0.192.in-addr.arpa`.
>
> Стандарты DNS поддерживают оба варианта, но имена зон DNS, содержащие символ косой черты (`/`), не поддерживаются в Azure DNS.

В примере ниже показано, как создать зону обратного просмотра DNS класса C с именем `2.0.192.in-addr.arpa` в Azure DNS при помощи портала Azure:

 ![Создание зоны DNS](./media/dns-reverse-dns-hosting/figure2.png)

Параметр "Расположение группы ресурсов" относится к расположению группы ресурсов и никак не влияет на расположение зоны DNS. Расположение зоны DNS всегда является глобальным и не отображается.

Ниже приведены примеры выполнения этой задачи с помощью Azure PowerShell и Azure CLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Имя зоны обратного просмотра IPv6 должно быть указано в формате `<IPv6 network prefix in reverse order>.ip6.arpa`.  Примеры см. в [обзоре записей в обратной зоне DNS и поддержки в Azure](dns-reverse-dns-overview.md#ipv6).


В примере ниже показано, как создать зону обратного просмотра DNS IPv6 с именем `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` в Azure DNS при помощи портала Azure:

 ![Создание зоны DNS](./media/dns-reverse-dns-hosting/figure3.png)

Параметр "Расположение группы ресурсов" относится к расположению группы ресурсов и никак не влияет на расположение зоны DNS. Расположение зоны DNS всегда является глобальным и не отображается.

Ниже приведены примеры выполнения этой задачи с помощью Azure PowerShell и Azure CLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Делегирование зоны обратного просмотра DNS

Создав зону обратного просмотра DNS, убедитесь, что она делегирована из родительской зоны. Делегирование DNS позволяет процессу разрешения имен DNS находить серверы имен, на которых размещена зона обратного просмотра DNS. После этого серверы имен могут отвечать на обратные запросы DNS для IP-адресов в указанном диапазоне.

Для зон прямого просмотра делегирование зоны DNS описано в разделе [Делегирование домена в Azure DNS](dns-delegate-domain-azure-dns.md). Делегирование для зон обратного просмотра выполняется аналогичным образом. Единственное различие — серверы имен настраиваются с помощью поставщика услуг Интернета, предоставившим вам диапазон IP-адресов, а не с помощью регистратора доменных имен.

## <a name="create-a-dns-ptr-record"></a>Создание записи DNS типа PTR

### <a name="ipv4"></a>IPv4

Следующий пример демонстрирует создание записи типа PTR для зоны обратного просмотра DNS в Azure DNS. Сведения о других типах записей и об изменении существующих записей см. в статье [Управление записями и наборами записей DNS с помощью портала Azure](dns-operations-recordsets-portal.md).

1.  В верхней части этой колонки **Зона DNS** щелкните **Набор записей**, чтобы открыть колонку **Добавление набора записей**.

 ![Зона DNS](./media/dns-reverse-dns-hosting/figure4.png)

1. Откройте колонку **Добавление набора записей**. 
1. Выберите **PTR** в меню **Тип**.  
1. Имя набора записей для записи типа PTR должно представлять собой оставшуюся часть адреса IPv4 в обратном порядке. В этом примере первые три октета уже указаны как часть имени зоны (.2.0.192). Таким образом, в поле имени содержится только последний октет. Например, для ресурса с IP-адресом 192.0.2.15 можно назвать набор записей **15**.  
1. В поле **Доменное имя** введите полное доменное имя (FQDN) ресурса, использующего IP-адрес.
1. В нижней части колонки нажмите кнопку **ОК**, чтобы создать запись DNS.

 ![Добавление набора записей](./media/dns-reverse-dns-hosting/figure5.png)

Ниже приведены примеры выполнения этой задачи с помощью PowerShell и Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>Azure CLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Следующий пример демонстрирует процесс создания записи типа PTR. Сведения о других типах записей и об изменении существующих записей см. в статье [Управление записями и наборами записей DNS с помощью портала Azure](dns-operations-recordsets-portal.md).

1. В верхней части колонки **Зона DNS** щелкните **Набор записей**, чтобы открыть колонку **Добавление набора записей**.

  ![Колонка зоны DNS](./media/dns-reverse-dns-hosting/figure6.png)

2. Откройте колонку **Добавление набора записей**. 
3. Выберите **PTR** в меню **Тип**.  
4. Имя набора записей для записи типа PTR должно представлять собой оставшуюся часть адреса IPv6 в обратном порядке. Сжатие за счет нулей не должно использоваться. В этом примере первые 64 бита IPv6 уже указаны как часть имени зоны (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Таким образом, в поле имени содержатся только последние 64 бита. Последние 64 бита IP-адреса вводятся в обратном порядке, в качестве разделителя между каждым шестнадцатеричным числом используется точка. Например, для ресурса с IP-адресом 2001:0db8:abdc:0000:f524:10bc:1af9:405e набор записей можно назвать **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**.  
5. В поле **Доменное имя** введите полное доменное имя (FQDN) ресурса, использующего IP-адрес.
6. В нижней части колонки нажмите кнопку **ОК**, чтобы создать запись DNS.

![Колонка добавления набора записей](./media/dns-reverse-dns-hosting/figure7.png)

Ниже приведены примеры выполнения этой задачи с помощью PowerShell и Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>Azure CLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть созданные записи, перейдите к зоне DNS на портале Azure. В нижней части колонки **Зона DNS** отображаются записи для зоны DNS. Должны отобразиться записи типа NS и SOA по умолчанию, которые создаются в каждой зоне, а также все новые записи, созданные вами.

### <a name="ipv4"></a>IPv4

Колонка зоны DNS с записями IPv4 типа PTR:

![Колонка зоны DNS](./media/dns-reverse-dns-hosting/figure8.png)

В примерах ниже показано, как просмотреть записи типа PTR с помощью PowerShell или Azure CLI:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Колонка зоны DNS с записями IPv6 типа PTR:

![Колонка зоны DNS](./media/dns-reverse-dns-hosting/figure9.png)

В примерах ниже показано, как просмотреть записи с помощью PowerShell и Azure CLI

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Часто задаваемые вопросы

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Можно ли разместить зоны обратного просмотра DNS для блоков IP-адресов, назначенных поставщиком услуг Интернета, в DNS Azure?

Да. Размещение зон обратного просмотра DNS для пользовательских диапазонов IP-адресов в Azure DNS полностью поддерживается.

Создайте зону обратного просмотра в Azure DNS, как описано в этой статье, а затем [делегируйте ее](dns-domain-delegation.md) с помощью поставщика услуг Интернета.  Вы можете управлять записями типа PTR для каждого обратного просмотра точно так же, как и другими записями другого типа.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Сколько стоит размещение зоны обратного просмотра DNS?

Размещение зоны обратного просмотра DNS в Azure DNS для блока IP-адресов, назначенного поставщиком услуг Интернета, оплачивается по [стандартным тарифам Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Можно ли разместить в Azure DNS зоны обратного просмотра DNS для адресов IPv4 и IPv6?

Да. В этой статье объясняется, как создать зоны обратного просмотра DNS для адресов IPv4 и IPv6 в Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Можно ли импортировать существующую зону обратного просмотра DNS?

Да. Вы можете импортировать существующие зоны DNS в Azure DNS при помощи интерфейса командной строки Azure. Он работает как с зонами прямого просмотра, так и с зонами обратного просмотра.

Дополнительные сведения см. в разделе [Импорт и экспорт файла зоны DNS с помощью Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. [в статье Википедии об обратном просмотре DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Узнайте, как [управлять записями обратной зоны DNS для служб Azure](dns-reverse-dns-for-azure-services.md).
