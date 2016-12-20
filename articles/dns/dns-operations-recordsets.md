---
title: "Управление наборами записей и записями DNS с помощью портала Azure | Документация Майкрософт"
description: "Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды PowerShell для операций с наборами записей и записями."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: e3f7967843b3d9e38c79b45d90e333192fd3a900

---

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Управление записями и наборами записей DNS с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал Azure](dns-operations-recordsets-portal.md)
> * [Интерфейс командной строки Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

В этой статье описывается, как управлять наборами записей и записями для зоны DNS с помощью Windows PowerShell.

Важно понимать различие между наборами записей DNS и отдельными записями DNS. Набор записей — это коллекция записей в зоне, которые имеют одно и то же имя и тип. Дополнительные сведения см. в статье [Создание наборов записей и записей DNS с помощью портала Azure](dns-getstarted-create-recordset-portal.md).

Для управления наборами записей и записями вам потребуется последняя версия командлетов PowerShell Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Дополнительные сведения о работе с PowerShell см. в статье [Manage Azure resources with PowerShell and Resource Manager](../powershell-azure-resource-manager.md) (Управление ресурсами Azure с помощью PowerShell и Resource Manager).

## <a name="create-a-new-record-set-and-a-record"></a>Создание нового набора записей и записи

Сведения о создании набора записей с помощью PowerShell см. в [этой статье](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Получение набора записей

Чтобы извлечь существующий набор записей, используйте команду `Get-AzureRmDnsRecordSet`. Укажите относительное имя набора записей, тип записей и зону.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

Как и в случае с командлетом `New-AzureRmDnsRecordSet`, имя записи должно быть относительным, т. е. не содержать имя зоны.

Вы можете указать зону с помощью имени зоны и имени группы ресурсов или с помощью объекта зоны:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

`Get-AzureRmDnsRecordSet` возвращает локальный объект, представляющий набор записей, созданный в Azure DNS.

## <a name="list-record-sets"></a>Перечисление наборов записей

Если опустить параметр `Get-AzureRmDnsRecordSet`-Name* и (или) параметр *-RecordType*, вы можете также использовать командлет * для вывода списка наборов записей.

### <a name="to-list-all-record-sets"></a>Вывод списка всех наборов записей

Этот пример возвращает все наборы записей, независимо от имени и типа записей.

```powershell
$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-list-record-sets-of-a-given-record-type"></a>Вывод списка наборов записей указанного типа

Этот пример возвращает все наборы записей, соответствующие указанному типу записи. В этом случае возвращаемый набор записей содержит записи типа A:

```powershell
$list = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

Зону можно указать с помощью параметров `-ZoneName` и `-ResourceGroupName` (как показано) или с помощью объекта зоны:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$list = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-a-record-set"></a>Добавление записи в набор записей

Чтобы добавить записи в наборы данных, выполните командлет `Add-AzureRmDnsRecordConfig` . Эта операция выполняется в автономном режиме. Изменяется только локальный объект, представляющий набор записей.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа A вы сможете указать только записи с параметром *-IPv4Address*.

Дополнительные записи можно добавлять в каждый набор записей с помощью новых вызовов командлета `Add-AzureRmDnsRecordConfig`. В любой набор записей можно добавить до 20 записей. Однако наборы записей типа CNAME могут содержать не более одной записи. Кроме того, набор записей не может содержать две идентичные записи. Можно создавать пустые наборы записей (с нулевым числом записей), но они не отображаются на серверах имен Azure DNS.

Когда в набор записей будет включена нужная коллекция записей, его нужно зафиксировать с помощью командлета `Set-AzureRmDnsRecordSet`. После этого существующий набор записей в Azure DNS заменяется зафиксированным набором.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Создание набора записей A с одной записью

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

Последовательность операций создания записи можно также *направить в конвейер*, т. е. передать объект набора записей с помощью конвейера, а не как параметр. Например:

```powershell
New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="additional-record-type-examples"></a>Дополнительные примеры типов записей

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Изменение существующих наборов записей

Действия по изменению существующего набора записей похожи на этапы создания записей. Последовательность операций выглядит следующим образом.

1. Извлеките существующий набор записей, используя командлет `Get-AzureRmDnsRecordSet`.
2. Измените набор записей путем добавления или удаления записей, изменения параметров записи или срока жизни набора записей. Эта операция выполняется в автономном режиме. Изменяется только локальный объект, представляющий набор записей.
3. Зафиксируйте изменения с помощью командлета `Set-AzureRmDnsRecordSet` . Этот набор записей заменит существующий набор записей в Azure DNS.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Обновление записи в существующем наборе записей

В данном примере мы изменим IP-адрес существующей записи типа A:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Ipv4Address = "134.170.185.46"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

Командлет `Set-AzureRmDnsRecordSet` использует проверки Etag, чтобы параллельные изменения не перезаписывались. Используйте параметр *-Overwrite*, чтобы не выполнять эти проверки. Дополнительные сведения см. в разделе [Об Etag и тегах](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Изменение записи типа SOA

Невозможно добавить или удалить записи из автоматически созданного набора записей типа SOA (имя = "@").), но можно изменить любой параметр в записи типа SOA (кроме Host) и TTL набора записей.

В следующем примере показано, как изменить свойство *Email* записи типа SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Изменение записи NS на вершине зоны

Невозможно добавить, удалить или изменить записи в автоматически созданном наборе записей NS на вершине зоны (имя = "@").). Изменить можно только TTL набора записей.

В следующем примере показано, как изменить свойство TTL набора записей NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-add-records-to-an-existing-record-set"></a>Добавление записей в существующий набор записей

В этом примере мы добавим две дополнительные записи MX в существующий набор записей:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="remove-a-record-from-an-existing-record-set"></a>Удаление записи из существующего набора записей

Записи можно удалить из набора записей с помощью командлета `Remove-AzureRmDnsRecordConfig`. Удаляемая запись должна точно соответствовать существующей записи по всем параметрам. Изменения необходимо зафиксировать с помощью командлета `Set-AzureRmDnsRecordSet`.

При удалении последней записи из набора сам набор не удаляется. Дополнительные сведения см. в разделе [Удаление набора записей](#delete-a-record-set) ниже.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

Последовательность операций для удаления записи из набора записей можно также направить в конвейер, т. е. передать объект набора записей с помощью конвейера, а не как параметр. Например:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Удаление записи AAAA из набора записей

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Удаление записи CNAME из набора записей

Так как набор записей CNAME может содержать не более одной записи, ее удаление приведет к получению пустого набора.

```powershell
$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Удаление записи MX из набора записей

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-ns-record-from-record-set"></a>Удаление записи NS из набора записей

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Удаление записи SRV из набора записей

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Удаление записи типа TXT из набора записей

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="delete-a-record-set"></a>Удаление набора записей

Наборы записей можно удалять с помощью командлета `Remove-AzureRmDnsRecordSet` . Невозможно удалить наборы записей типа SOA и NS в вершине зоны (имя = "@")), созданные автоматически вместе с зоной. Они будут удалены автоматически при удалении зоны.

Используйте один из следующих трех способов для удаления набора записей.

### <a name="specify-all-the-parameters-by-name"></a>Выбор всех параметров по имени

Чтобы отключить запрос подтверждения, можно указать необязательный параметр *-Force* .

```powershell
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Выбор набора записей по имени и типу, а также выбор зоны с помощью объекта

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]
```

### <a name="specify-the-record-set-by-object"></a>Выбор набора записей с помощью объекта

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs [-Overwrite] [-Force]
```

Указывая набор записей с помощью объекта, вы можете выполнить проверки Etag, чтобы убедиться, что параллельные изменения не удаляются. Необязательный параметр *-Overwrite* позволяет не выполнять эти проверки. Подробные сведения см. в разделе [Об Etag и тегах](dns-getstarted-create-dnszone.md#tagetag).

Объект набора записей также можно направить в конвейер, а не передать в качестве параметра:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md). Сведения об автоматизации DNS см. в статье [Создание зон и наборов записей DNS с помощью пакета SDK для .NET](dns-sdk.md).

Дополнительные сведения об обратных записях DNS см. в статье [Как управлять обратными записями DNS для служб с помощью PowerShell](dns-reverse-dns-record-operations-ps.md).



<!--HONumber=Nov16_HO3-->


