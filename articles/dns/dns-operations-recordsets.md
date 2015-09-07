<properties 
   pageTitle="Управление наборами записей и записями DNS в Azure DNS | Microsoft Azure"
	description="Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды PowerShell для операций с наборами записей и записями."
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="Adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="en"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/02/2015"
	ms.author="joaoma"/>

# Управление записями DNS


> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [Azure Powershell](dns-operations-recordsets.md)


В этом руководстве показано, как управлять наборами записей и записями для зоны DNS.

Важно понимать различие между наборами записей DNS и отдельными записями DNS. Набор записей — это коллекция записей в зоне с одним именем и типом. Подробнее: [Общие сведения о наборах записей и записях](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Создание набора записей

Наборы записей создаются с помощью командлета New-AzureDnsRecordSet. Вам необходимо указать имя набора записей, зону, время жизни (TTL) и тип записи.

>[AZURE.NOTE]Имя набора записей должно быть относительным, т. е. не содержать имя зоны. Например, имя набора записей www в зоне contoso.com создаст наборе запись с полным именем www.contoso.com.

>В качестве имени набора записей в вершине зоны используйте "@", включая кавычки. Тогда полное имя набора записей будет идентично имени зоны (в этом случае "contoso.com").

Служба Azure DNS поддерживает следующие типы записей: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Наборы записей типа SOA создаются автоматически вместе с каждой зоной, их невозможно создать отдельно.

	PS C:\> $rs = New-AzureDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Если набор записей уже существует, команда завершится ошибкой, если не указать параметр -Overwrite. Параметр -Overwrite отображает запрос подтверждения, который можно отменить с помощью параметра -Force.

В примере выше зона задается с помощью объекта zone, возвращенным командлетом Get-AzureDnsZone или New-AzureDnsZone. Кроме того, зону можно также указать по имени зоны и имени группы ресурсов:

	PS C:\> $rs = New-AzureDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Командлет New-AzureDnsRecordSet возвращает локальный объект, представляющий набор записей, созданный в Azure DNS.

>[AZURE.NOTE]Наборы записей CNAME не могут сосуществовать с другими наборами записей с тем же именем. Например, невозможно одновременно создать запись CNAME и запись A с относительным именем www. Поскольку вершина зоны (имя = "@") всегда содержит наборы записей NS и SOA, созданные при создании зоны, невозможно создать набор записей CNAME на вершине зоны. Это связано со стандартами DNS, а не ограничениями Azure DNS.

### Записи с подстановочными знаками
Azure DNS поддерживает [записи с подстановочными знаками](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Они возвращаются для любого запроса с совпадающим именем (пока не найдется еще более близкое совпадение из набора записей без подстановочных знаков).

>[AZURE.NOTE]Чтобы создать набор записей с подстановочными знаками, используйте знак "*" вместо имени набора записей или вместо первых символов имени, например "*.foo".

>Наборы записей с подстановочными знаками поддерживаются для всех типов записей, за исключением NS и SOA.

## Получение набора записей
Чтобы получить существующий набор записей, используйте командлет Get-AzureDnsRecordSet, указав относительное имя набора записей, тип записи и зону:

	PS C:\> $rs = Get-AzureDnsRecordSet -Name www –RecordType A -Zone $zone

Как и для командлета New-AzureDnsRecordSet, имя записи должно быть относительным, т. е. не содержать имя зоны. Зону можно задать с помощью объекта zone (как описано выше) или по имени зоны и имени группы ресурсов:

	PS C:\> $rs = Get-AzureDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Командлет Get-AzureDnsRecordSet возвращает локальный объект, представляющий набор записей, созданный в Azure DNS.

## Перечисление наборов записей
Если опустить параметр -Name или -RecordType, командлет Get-AzureDnsRecordSet также может использоваться для перечисления наборов записей.

### Вариант 1 
Вывод списка всех наборов записей. Возвращает все наборы записей, независимо от имени и типа записи:

	PS C:\> $list = Get-AzureDnsRecordSet -Zone $zone
### Вариант 2 

Вывод списка наборов записей указанного типа. Возвращает все наборы записей, соответствующие заданному типу записей (в данном случае это записи A):

	PS C:\> $list = Get-AzureDnsRecordSet –RecordType A -Zone $zone 

В обоих примерах выше зону можно указать с помощью объекта zone (как показано) или с помощью параметров -ZoneName и -ResourceGroupName.

## Добавление записи в набор записей
Записи добавляются в наборы записей с помощью командлета Add-AzureDnsRecordConfig. Это автономная операция — изменяется только локальный объект, представляющий набор записей.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа A вы сможете указать записи только с параметром IPv4Address.

Дополнительные записи можно добавить для каждого набора записей с помощью новых вызовов Add-AzureDnsRecordConfig. В любой набор записей можно добавить до 100 записей. Однако наборы записей типа CNAME могут содержать не более одной записи, при этом набор записей не может содержать две одинаковые записи. Пустые наборы записей (с нулевым числом записей) могут быть созданы, но они не отображаются в серверами имен Azure DNS.

Когда набор записей содержит нужную коллекцию записей, его необходимо зафиксировать с помощью командлета Set-AzureDnsRecordSet, который заменяет существующий набор записей в Azure DNS на предоставленный набор. В следующих примерах показано, как создать набор записей каждого типа, содержащий одну запись.

### Создание набора записей с одной записью A

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

Последовательность операций для создания записи можно также направить в конвейер, передав объект набора записей с помощью конвейера, а не как параметр. Например:

	PS C:\> New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet

### Создание набора записей AAAA с одной записью

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Создание набора записей CNAME с одной записью

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Создание набора записей MX с одной записью
Чтобы создать запись MX на вершине зоны (например, "contoso.com"), в этом примере мы используем имя набора записей "@". Так обычно и делается при создании записей MX.

	PS C:\> $rs = New-AzureDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Создание набора записей NS с одной записью

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs
### Создание набора записей SRV с одной записью

При создании записи SRV в корне зоны просто укажите \_service и \_protocol в имени записи — включать ".@" в имя записи не нужно.

	PS C:\> $rs = New-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Создание набора записей TXT с одной записью

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## Изменение существующих наборов записей
Для изменения существующих наборов записей использует шаблон, аналогичный процедуре создания записей. Последовательность операций выглядит следующим образом.

1.	Получите существующий набор записей с помощью командлета Get-AzureDnsRecordSet.
2.	Измените набор записей, добавив записи, удалив записи, изменив параметры записи или срок жизни набора записей. Эти изменения производятся в автономном режиме — только в локальном объекте, который представляет изменяемый набор записей.
3.	Примените изменения, используя командлет Set-AzureDnsRecordSet. При этом существующий набор записей в Azure DNS заменяется на представленный набор.

Это показано в следующих примерах:

### Обновление записи в существующем наборе записей
В этом примере мы изменим IP-адрес существующей записи A:

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

Командлет Set-AzureDnsRecordSet использует проверки etag, чтобы параллельные изменения не перезаписывались. Используйте флажок -Overwrite для подавления этих проверок. Подробные сведения см. в разделе "Etag и теги".

### Изменение записи SOA

>[AZURE.NOTE]Невозможно добавить или удалить записи из автоматически созданного набора записей SOA (имя = "@"), но можно изменить параметры в записи SOA и значение TTL набора записей.

В следующем примере показано, как изменить свойство Email записи SOA:

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### Изменение записи NS на вершине зоны

>[AZURE.NOTE]Невозможно добавить, удалить или изменить записи в автоматически созданном наборе записей NS на вершине зоны (имя = "@"). Разрешено только одно изменение — изменить TTL набора записей.

В следующем примере показано, как изменить свойство TTL набора записей NS:

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### Добавление записей в существующий набор записей
В этом примере мы добавим две дополнительные записи MX в существующий набор записей:

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

## Удаление записи из существующего набора записей

Записи можно удалить из набора с помощью командлета Remove-AzureDnsRecordConfig. Обратите внимание, что удаляемая запись должна точно соответствовать существующей записи по всем параметрам. Изменения необходимо применить с помощью командлета Set-AzureDnsRecordSet.

При удалении последней записи из набора сам набор не удаляется. Подробнее см. в разделе [Удаление набора записей](#delete-a-record-set) ниже.


	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

Последовательность операций для удаления записи можно также направить в конвейер, передав объект набора записей с помощью конвейера, а не как параметр. Например:

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet
### Удаление записи AAAA из набора записей

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Удаление записи CNAME из набора записей

Так как набор записей CNAME может содержать не более одной записи, ее удаление приведет к получению пустого набора.

	PS C:\> $rs =  Get-AzureDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Удаление записи MX из набора записей

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Удаление записи NS из набора записей
	
	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Удаление записи SRV из набора записей

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Удаление записи TXT из набора записей

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## Удаление набора записей
Наборы записей можно удалить с помощью командлета Remove-AzureDnsRecordSet.

>[AZURE.NOTE]Невозможно удалить наборы записей SOA и NS на вершине зоны (имя = "@"), которые создаются автоматически вместе с зоной. Они будут удалены автоматически при удалении зоны.

Используйте один из следующих трех способов для удаления набора записей.
### Вариант 1
Укажите все параметры по имени:

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]
Чтобы отключить запрос подтверждения, можно указать необязательный параметр -Force.

### Вариант 2
Укажите набор записей по имени и типу, укажите зону объектом:

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Вариант 3
Укажите набора записей объектом.

	PS C:\> Remove-AzureDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

При указании набора записей с помощью объекта вы можете выполнить проверки etag, чтобы убедиться, что параллельные изменения не удаляются. Необязательный флажок -Overwrite подавляет эти проверки. Подробные сведения см. в разделе [Etag и теги](../dns-getstarted-create-dnszone#Etags-and-tags).

Объект набора записей также можно направить в конвейер, а не передать в качестве параметра:

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordSet [-Overwrite] [-Force]

##См. также

[Приступить к созданию наборов записей и записей](../dns-getstarted-create-recordset)<BR> [Выполнение операций с зонами DNS](../dns-operations-dnszones)<BR> [Автоматизации операций с помощью пакета SDK для .NET](../dns-sdk)
 

<!---HONumber=August15_HO9-->