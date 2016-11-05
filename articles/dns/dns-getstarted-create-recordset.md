---
title: Создание набора записей и записей зоны DNS с помощью PowerShell | Microsoft Docs
description: Как создать записи узла для Azure DNS. Настройка наборов записей и записей с помощью PowerShell
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Создание наборов записей и записей DNS с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)
> 
> 

В этой статье показано, как создавать записи и наборы записей с помощью Windows PowerShell. После создания зоны DNS следует добавить записи DNS для домена. Для этого сначала требуется изучить DNS-записи и наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Убедитесь, что у вас установлена последняя версия PowerShell.
Убедитесь, что у вас установлена последняя версия командлетов PowerShell для Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Создание набора записей и записи
В этом разделе будет описано, как создать запись и набор записей.

### 1\. Подключение к подписке
Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

    Login-AzureRmAccount

Просмотрите подписки учетной записи.

    Get-AzureRmSubscription

Укажите подписку, которую нужно использовать.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Дополнительные сведения о работе с PowerShell см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### 2) Создание набора записей
Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. При создании набора записей необходимо указать его имя, зону, срок жизни и тип записей.

Чтобы создать набор записей в вершине зоны (в этом случае "contoso.com"), используйте имя записи "@", включая кавычки. Это общее соглашение при работе с DNS.

В следующем примере создается набор записей с относительным именем www в зоне DNS contoso.com. Полное доменное имя набора записей — www.contoso.com. Тип записи — "A", а срок жизни составляет 60 секунд. По завершении этого шага будет создан пустой набор записей www, назначенный переменной *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Если набор записей уже существует
Если набор записей уже существует, команда завершится ошибкой, если не указать параметр *-Overwrite*. Параметр *-Overwrite* активирует запрос подтверждения, который можно отменить с помощью параметра *-Force*.

    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


В этом примере следует указать зону с помощью имени зоны и имени группы ресурсов. Кроме того, можно указать объект зоны, возвращенный `Get-AzureRmDnsZone` или `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

Командлет `New-AzureRmDnsRecordSet` возвращает локальный объект, представляющий набор записей, созданный в Azure DNS.

### 3\. Добавление записи
Чтобы использовать созданный набор записей www, в него нужно добавить записи. Записи *A* (IPv4) можно добавить в набор записей www с помощью следующего примера. Этот пример основан на переменной *$rs*, заданной на предыдущем шаге.

Добавление записей в набор записей с помощью `Add-AzureRmDnsRecordConfig` — это операция, выполняемая вне сети. Обновляется только локальная переменная *$rs*.

    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\. Фиксация изменений
Зафиксируйте изменения в наборе записей. Выполните командлет `Set-AzureRmDnsRecordSet`, чтобы передать изменения набора записей в Azure DNS.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\. Извлечение набора записей
Вы можете извлечь набор записей из Azure DNS с помощью команды `Get-AzureRmDnsRecordSet`, как показано ниже.

    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Можно также использовать программу nslookup или другие инструменты DNS для запроса нового набора записей.

Если вы еще не делегировали домен DNS-серверам Azure, то требуется явно указать имя, сервер и адрес для зоны.

    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## Создание набора записей каждого типа с одной записью
В следующих примерах показано, как создать набор записей каждого типа. Каждый такой набор содержит одну запись.

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## Дальнейшие действия
[Как управлять зонами DNS с помощью PowerShell](dns-operations-dnszones.md)

[Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md)

[Автоматизация операций Azure с помощью пакета SDK для .NET](dns-sdk.md)

<!---HONumber=AcomDC_0817_2016-->