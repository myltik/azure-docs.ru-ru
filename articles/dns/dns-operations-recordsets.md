---
title: Управление записями DNS в службе DNS Azure с помощью Azure PowerShell | Документация Майкрософт
description: Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды PowerShell для операций с наборами записей и записями.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: kumud
ms.openlocfilehash: 511af342727dc46369ae70d60a7e9a3171bf986d
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "32778841"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Управление записями и наборами записей DNS в службе DNS Azure с помощью Azure PowerShell

> [!div class="op_single_selector"]
> * [портал Azure](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

В этой статье описывается, как управлять записями DNS для зоны DNS с помощью Azure PowerShell. Записями DNS также можно управлять с помощью кроссплатформенного [интерфейса командной строки Azure](dns-operations-recordsets-cli.md) или [портала Azure](dns-operations-recordsets-portal.md).

Для работы с руководством необходимо [установить Azure PowerShell, войти в учетную запись и создать зону DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Введение

Чтобы создавать записи DNS в Azure DNS, нужно понимать, как Azure DNS организует записи DNS в соответствующие наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Дополнительные сведения о записях DNS в Azure DNS см. в статье [Зоны и записи DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Создание записи DNS

Если вы создаете запись с такими же именем и типом, как у существующей записи, эту новую запись нужно [добавить в существующий набор записей](#add-a-record-to-an-existing-record-set). Если имя и тип новой и существующих записей отличаются, вам нужно создать другой набор записей. 

### <a name="create-a-records-in-a-new-record-set"></a>Создание записей А в новом наборе записей

Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. Создавая набор записей, вам нужно определить для него имя, зону, срок жизни (TTL), тип записей и сами создаваемые записи.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа A вам нужно указать IP-адрес с использованием параметра `-IPv4Address`. Другие параметры используются для других типов записей (см. [примеры других типов записей](#additional-record-type-examples)).

В следующем примере создается набор записей с относительным именем www в зоне DNS contoso.com. Полное доменное имя набора записей — www.contoso.com. Тип записи — A, а срок жизни — 3600 секунд. Каждый такой набор содержит одну запись с IP-адресом 1.2.3.4.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Чтобы создать набор записей на вершине зоны (в нашем примере — contoso.com), используйте имя записи \@ (без кавычек):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Чтобы создать набор записей, содержащий несколько записей, сначала создайте локальный массив и добавьте записи, а затем передайте этот массив в `New-AzureRmDnsRecordSet`:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Метаданные набора записей](dns-zones-records.md#tags-and-metadata) используются для связывания данных приложения с каждым набором записей в виде пар "ключ — значение". В следующем примере показано, как создать набор с двумя записями метаданных: dept=finance и environment=production.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

В Azure DNS также поддерживаются пустые наборы записей, которые могут использоваться как заполнители для резервирования имен DNS перед созданием записей DNS. Пустые наборы записей, отображаются на панели управления Azure DNS и серверах имен Azure DNS. В следующем примере создается пустой набор записей.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Создание записей других типов

Вы уже узнали, как создавать записи типа А. В следующем примере показано, как создавать записи других типов, поддерживаемые в Azure DNS.

Мы покажем, как создать набор записей каждого типа, который будет содержать одну запись. Создавать наборы записей других типов (пустые наборы записей или содержащие несколько записей с метаданными) можно на основе представленных выше примеров с записями типа A.

Мы не включаем пример создания набора записей типа SOA, так как такие записи создаются и удаляются только вместе с соответствующей зоной DNS. Тем не менее [записи типа SOA можно изменять, как показано в примере ниже](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Создание набора записей типа AAAA с одной записью

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Создание набора записей типа CAA с одной записью

```powershell
New-AzureRmDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Создание набора записей типа CNAME с одной записью

> [!NOTE]
> Стандарты DNS не допускают использование записей типа CNAME на вершине зоны (`-Name '@'`), а также использование наборов записей, содержащих более одной записи.
> 
> Дополнительные сведения см. в разделе [Записи типа CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Создание набора записей типа MX с одной записью

Чтобы создать запись MX на вершине зоны (в данном случае — contoso.com), в этом примере мы используем имя набора записей \@.


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Создание набора записей типа NS с одной записью

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Создание набора записей типа PTR с одной записью

В этом случае my-arpa-zone.com представляет зону обратного просмотра ARPA вашего диапазона IP-адресов. Каждая запись PTR в этой зоне соответствует IP-адресу в этом диапазоне. Имя записи 10 — это последний октет IP-адреса в этом диапазоне IP-адресов, представленном данной записью.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Создание набора записей типа SRV с одной записью

Создавая [набор записей SRV](dns-zones-records.md#srv-records), укажите в его имени *\_службу* и *\_протокол*. Если набор записей SRV создается на вершине зоны, включать \@ в имя набора записей не нужно.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Создание набора записей типа TXT с одной записью

В следующем примере показано, как создать запись типа ТХТ. Дополнительные сведения о максимальной длине строки, поддерживаемой в записях типа TXT, см. в разделе [Записи типа TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Получение набора записей

Чтобы извлечь существующий набор записей, используйте команду `Get-AzureRmDnsRecordSet`. Этот командлет возвращает локальный объект, представляющий набор записей в Azure DNS.

Как и в случае с командлетом `New-AzureRmDnsRecordSet`, имя записи должно быть *относительным*, т. е. оно не должно содержать имя зоны. Также необходимо определить тип записи и зону, содержащую набор записей.

В следующем примере показано, как получить набор записей. В этом примере зона определяется с помощью `-ZoneName` и `-ResourceGroupName` параметров.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Кроме того, зону можно указать с помощью объекта зоны, переданного с помощью параметра `-Zone`.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Перечисление наборов записей

Чтобы перечислить наборы записей в зоне, можно также использовать `Get-AzureRmDnsZone`, опустив параметры `-Name` или `-RecordType`.

В следующем примере возвращаются все наборы записей в зоне:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

В следующем примере показано, как можно получить, все наборы записей нужного типа, указав тип набора записей и опустив его имя:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Чтобы получить все наборы записей разных типов с нужным именем, необходимо получить все наборы записей, а затем отфильтровать результаты:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Во всех этих примерах зону можно определить с помощью параметров `-ZoneName` и `-ResourceGroupName` (как и показано) или с помощью объекта зоны:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Добавление записи в существующий набор записей

Чтобы добавить запись в существующий набор записей, сделайте следующее.

1. Получите существующий набор записей.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Добавьте новую запись в локальный набор записей. Эта операция выполняется в автономном режиме.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Зафиксируйте изменения в службе Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

С помощью `Set-AzureRmDnsRecordSet` существующий набор записей в Azure DNS (и все записи, которые он содержит) *будет заменен* указанным набором записей. [Проверки Etag](dns-zones-records.md#etags) помогают избежать перезаписи параллельных изменений. Чтобы отменить эти проверки, укажите необязательный параметр `-Overwrite`.

Последовательность операций также можно *направить в конвейер*. Это значит, что объект набора записей передается с помощью конвейера, а не как параметр:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Приведенные выше примеры иллюстрируют добавление записи типа А в существующий набор записей типа A. Аналогичная последовательность операций используется и для добавления записей в наборы записей других типов. Для этого в `Add-AzureRmDnsRecordConfig` нужно просто заменить параметр `-Ipv4Address` параметром, соответствующим нужному типу записи. Как видно в [примерах с другими типами записей](#additional-record-type-examples) (см. выше), используемые для каждого типа записи параметры соответствуют параметрам в командлете `New-AzureRmDnsRecordConfig`.

Наборы записей типа CNAME или SOA не могут содержать более одной записи. Это ограничение определяется общими стандартами DNS, а не Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Удаление записи из существующего набора записей

Процедура удаления записи из существующего набора записей аналогична процедуре добавления в такой набор.

1. Получите существующий набор записей.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Удалите запись из объекта локального набора записей. Эта операция выполняется в автономном режиме. Удаляемая запись должна точно соответствовать существующей записи по всем параметрам.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Зафиксируйте изменения в службе Azure DNS. Чтобы отменить [проверки Etag](dns-zones-records.md#etags) (проверки перезаписи параллельных изменений), используйте необязательный параметр `-Overwrite`.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

Используя приведенную выше последовательность для удаления последней записи из набора, вы не удалите набор, а оставите его пустым. Как удалить сам набор записей, описано в разделе [Удаление набора записей](#delete-a-record-set).

Как и при добавлении записей в набор записей, последовательность операций для удаления набора записей также можно объединить в конвейер:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Поддержку разных типов записей можно реализовать, передав в `Remove-AzureRmDnsRecordSet` соответствующие каждому типу параметры. Как видно в [примерах с другими типами записей](#additional-record-type-examples) (см. выше), используемые для каждого типа записи параметры соответствуют параметрам в командлете `New-AzureRmDnsRecordConfig`.


## <a name="modify-an-existing-record-set"></a>Обновление существующего набора записей

Процедура изменения существующего набора записей аналогична процедуре добавления записей в набор записей и их удаления оттуда.

1. Извлеките существующий набор записей, используя командлет `Get-AzureRmDnsRecordSet`.
2. Чтобы изменить локальный объект набора записей, сделайте следующее:
    * добавьте или удалите записи;
    * измените параметры существующих записей;
    * измените метаданные и время жизни (TTL) для набора записей.
3. Зафиксируйте изменения с помощью командлета `Set-AzureRmDnsRecordSet` . Существующий набор записей в Azure DNS *будет заменен* указанным набором.

При использовании `Set-AzureRmDnsRecordSet` избежать перезаписи параллельных изменений помогут [проверки Etag](dns-zones-records.md#etags). Чтобы отменить эти проверки, укажите необязательный параметр `-Overwrite`.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Обновление записи в существующем наборе записей

В данном примере мы изменим IP-адрес существующей записи типа A:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Изменение записи типа SOA

В автоматически созданном наборе записей типа SOA на вершине зоны (`-Name "@"`, включая кавычки) добавлять или удалять записи нельзя. Однако вы можете изменить любые параметры в записи типа SOA (за исключением параметра "Узел") и TTL набора записей.

В следующем примере показано, как изменить свойство *Email* записи типа SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Изменение записи NS на вершине зоны

Набор записей типа NS на вершине зоны автоматически создается вместе с каждой зоной DNS. Он содержит имена DNS-серверов Azure, назначенные зоне.

Вы можете добавить дополнительные имена серверов в этот набор записей NS, обеспечив поддержку совместного размещения доменов с использованием более чем одного поставщика DNS. Вы также можете изменить срок жизни и метаданные для этого набора записей. При этом вы не можете удалить или изменить предварительно заполненные серверы доменных имен Azure DNS.

Обратите внимание, что это относится только к набору записей NS на вершине зоны. Другие наборы записей NS в зоне (используемые для делегирования дочерних зон) можно изменять без ограничений.

В следующем примере показано, как добавить дополнительный сервер в набор записей NS на вершине зоны:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Изменение метаданных набора записей

[Метаданные набора записей](dns-zones-records.md#tags-and-metadata) используются для связывания данных приложения с каждым набором записей в виде пар "ключ — значение".

В примере ниже показано, как изменить метаданные существующего набора записей:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Удаление набора записей

Наборы записей можно удалять с помощью командлета `Remove-AzureRmDnsRecordSet` . При удалении набора записей также удаляются все содержащиеся в нем записи.

> [!NOTE]
> Удалить наборы записей типа SOA и NS на вершине зоны (`-Name '@'`) нельзя.  Эти записи создаются и удаляются в Azure DNS автоматически вместе с зоной.

В примере ниже показано, как удалить набор записей. В этом примере имя и тип набора записей, имя зоны и группа ресурсов указываются явным образом.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Также набор записей можно указать по имени, типу и зоне (определяется с помощью объекта):

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Либо же можно указать сам набор записей, используя объект набора записей:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Указывая набор удаляемых записей с помощью объекта, вы можете выполнить [проверки Etag](dns-zones-records.md#etags), чтобы избежать удаления параллельных изменений. Чтобы отменить эти проверки, укажите необязательный параметр `-Overwrite`.

Объект набора записей также можно направить в конвейер, а не передать в качестве параметра:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Запросы на подтверждение

Командлеты `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` и `Remove-AzureRmDnsRecordSet` поддерживают запросы на подтверждение.

Каждый командлет запрашивает подтверждение, если привилегированная переменная `$ConfirmPreference` в PowerShell имеет значение `Medium` или ниже. Так как по умолчанию переменной `$ConfirmPreference` присвоено значение `High`, эти запросы не используются при использовании стандартных параметров PowerShell.

Текущее значение `$ConfirmPreference` можно переопределить с помощью параметра `-Confirm`. Если вы определите `-Confirm` или `-Confirm:$True`, командлет будет запрашивать подтверждение перед выполнением. Если вы определите `-Confirm:$False`, командлет не будет запрашивать подтверждение. 

Дополнительные сведения об элементах `-Confirm` и `$ConfirmPreference` см. в статье о [привилегированных переменных](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [зонах и записях в Azure DNS](dns-zones-records.md).
<br>
Узнайте, как [защитить зоны и записи](dns-protect-zones-recordsets.md) при использовании Azure DNS.
<br>
Просмотрите [справочную документацию по Azure DNS PowerShell](/powershell/module/azurerm.dns).
