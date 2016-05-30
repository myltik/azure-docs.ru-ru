<properties 
   pageTitle="Управление наборами записей и записями DNS в Azure DNS | Microsoft Azure" 
   description="Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды PowerShell для операций с наборами записей и записями." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Управление записями и наборами записей DNS с помощью PowerShell



> [AZURE.SELECTOR]
- [Портал Azure](dns-operations-recordsets-portal.md)
- [Интерфейс командной строки Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



В этой статье описывается, как управлять наборами записей и записями для зоны DNS с помощью Azure PowerShell.

Важно понимать различие между наборами записей DNS и отдельными записями DNS. Набор записей — это коллекция записей в зоне с одним именем и типом. Дополнительные сведения см. в статье [Общие сведения о наборах записей и записях](dns-getstarted-create-recordset-portal.md).

Для управления наборами записей и записями вам потребуется установить последнюю версию командлетов PowerShell Azure Resource Manager. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Дополнительные сведения о работе с PowerShell см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).



## Создание нового набора записей и записи

Чтобы создать набор записей на портале Azure, ознакомьтесь со статьей [Создание записей DNS с помощью портала Azure](dns-getstarted-create-recordset.md).

## Получение набора записей

Чтобы извлечь существующий набор записей, используйте команду `Get-AzureRmDnsRecordSet`. Укажите относительное имя набора записей, тип записей и зону.

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Как и для командлета `New-AzureRmDnsRecordSet`, имя набора записей должно быть относительным, т. е. не содержать имя зоны.

Зону можно задать с помощью имени зоны и имени группы ресурсов или с помощью объекта зоны.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
Командлет `Get-AzureRmDnsRecordSet` возвращает локальный объект, представляющий набор записей, созданный в Azure DNS.

## Перечисление наборов записей

Если опустить параметр *-Name* и (или) параметр *-RecordType*, командлет `Get-AzureRmDnsRecordSet` может также использоваться для вывода списка наборов записей.

### Вывод списка всех наборов записей 

Этот пример вернет все наборы записей, независимо от имени и типа записи.

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### Вывод списка наборов записей указанного типа

Этот пример вернет все наборы записей, соответствующие указанному типу записи. В этом случае это записи A.

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

Зону можно указать с помощью параметров *-ZoneName* и *-ResourceGroupName* (как показано) или с помощью объекта зоны.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## Добавление записи в набор записей

Записи добавляются в наборы записей с помощью командлета `Add-AzureRmDnsRecordConfig`. Это автономная операция — изменяется только локальный объект, представляющий набор записей.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа *A* вы сможете указать записи только с параметром *-IPv4Address*.

Дополнительные записи можно добавить в каждый набор записей с помощью новых вызовов `Add-AzureRmDnsRecordConfig`. В любой набор записей можно добавить до 20 записей. Однако наборы записей типа *CNAME* могут содержать не более одной записи, при этом набор записей не может содержать две одинаковые записи. Пустые наборы записей (с нулевым числом записей) могут быть созданы, но они не отображаются в серверами имен Azure DNS.

После того, как в набор записей помещена нужная коллекция записей, его нужно зафиксировать с помощью командлета `Set-AzureRmDnsRecordSet`. После фиксации набор записей заменяет собой существующий набор записей в Azure DNS.

### Создание набора записей A с одной записью

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

Последовательность операций для создания записи можно также направить в конвейер, передав объект набора записей с помощью конвейера, а не как параметр. Например:

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Дополнительные примеры типов записей

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## Изменение существующих наборов записей

Для изменения существующих наборов записей используется алгоритм, аналогичный процедуре создания записей. Последовательность операций выглядит следующим образом.

1.	Извлеките существующий набор записей, используя командлет `Get-AzureRmDnsRecordSet`.

2.	Измените набор записей, добавив записи, удалив записи, изменив параметры записи или срок жизни набора записей. Эти изменения производятся в автономном режиме — только в локальном объекте, который представляет изменяемый набор записей.

3.	Зафиксируйте изменения с помощью командлета `Set-AzureRmDnsRecordSet`. При этом существующий набор записей в Azure DNS заменяется предоставленным набором записей.


### Обновление записи в существующем наборе записей

В этом примере мы изменим IP-адрес существующей записи A.

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

Командлет `Set-AzureRmDnsRecordSet` использует проверки *Etag*, чтобы параллельные изменения не перезаписывались. Используйте параметр *-Overwrite* для подавления этих проверок. Дополнительные сведения см. в разделе [О Etag и тегах](dns-getstarted-creatednszone.md#tagetag).

### Изменение записи типа SOA

Невозможно добавить или удалить записи из автоматически созданного набора записей типа SOA (имя = "@"), но можно изменить любой параметр в записи SOA (кроме "Host") и значение TTL набора записей.

В следующем примере показано, как изменить свойство *Email* записи типа SOA.

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### Изменение записи NS на вершине зоны

Невозможно добавить, удалить или изменить записи в автоматически созданном наборе записей NS на вершине зоны (имя = "@"). Разрешено только одно изменение — изменить TTL набора записей.

В следующем примере показано, как изменить свойство TTL набора записей NS:

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### Добавление записей в существующий набор записей

В этом примере мы добавим две дополнительные записи MX в существующий набор записей.

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs 

## Удаление записи из существующего набора записей

Записи можно удалить из набора записей с помощью командлета `Remove-AzureRmDnsRecordConfig`. Обратите внимание, что удаляемая запись должна точно соответствовать существующей записи по всем параметрам. Изменения необходимо зафиксировать с помощью командлета `Set-AzureRmDnsRecordSet`.

При удалении последней записи из набора сам набор не удаляется. Дополнительные сведения см. в разделе [Удаление набора записей](#delete-a-record-set) ниже.


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

Последовательность операций для удаления записи можно также направить в конвейер, передав объект набора записей с помощью конвейера, а не как параметр. Например:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Удаление записи AAAA из набора записей

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Удаление записи CNAME из набора записей

Так как набор записей CNAME может содержать не более одной записи, ее удаление приведет к получению пустого набора.

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Удаление записи MX из набора записей

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Удаление записи NS из набора записей
	
	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Удаление записи SRV из набора записей

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Удаление записи TXT из набора записей

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## Удаление набора записей

Наборы записей можно удалять с помощью командлета `Remove-AzureRmDnsRecordSet`. Невозможно удалить наборы записей типа SOA и NS на вершине зоны (имя = "@"), которые создаются автоматически вместе с зоной. Они будут удалены автоматически при удалении зоны.

Используйте один из следующих трех способов для удаления набора записей.

### Укажите все параметры по имени.

Чтобы отключить запрос подтверждения, можно указать необязательный параметр *-Force*.

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### Укажите набор записей по имени и типу, укажите зону с помощью объекта.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Укажите набора записей объектом.

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

При указании набора записей с помощью объекта вы можете выполнить проверки Etag, чтобы убедиться, что параллельные изменения не удаляются. Необязательный параметр *-Overwrite* подавляет эти проверки. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#tagetag).

Объект набора записей также можно направить в конвейер, а не передать в качестве параметра:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## Дальнейшие действия

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md). Сведения об автоматизации DNS см. в статье [Создание зон DNS и наборов записей с помощью пакета SDK для .NET](dns-sdk.md).

Если вы хотите работать с обратными записями DNS, ознакомьтесь с разделом [Как управлять обратными записями DNS для служб с помощью PowerShell](dns-reverse-dns-record-operations-ps.md).
 

<!---HONumber=AcomDC_0518_2016-->