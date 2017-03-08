---
title: "Создание набора записей и записей зоны DNS с помощью PowerShell | Документация Майкрософт"
description: "Как создавать записи узлов для Azure DNS. Настройка наборов записей и записей с помощью PowerShell"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 67b780d66eac4199b0a2367f575477191542cfa7
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Создание наборов записей и записей DNS с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создавать записи и наборы записей с помощью Azure PowerShell.

## <a name="introduction"></a>Введение

Чтобы создавать записи DNS в Azure DNS, нужно понимать, как Azure DNS организует записи DNS в соответствующие наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Дополнительные сведения о записях DNS в Azure DNS см. в статье [Зоны и записи DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Создание набора записей и записи

В этом разделе описывается создание записей DNS в Azure DNS. Для работы с примерами необходимо [установить Azure PowerShell, войти в учетную запись и создать зону DNS](dns-getstarted-create-dnszone.md).

Во всех примерах на этой странице используется запись DNS типа A. Сведения о других типах записей и дополнительную информацию об управлении записями DNS и наборами записей см. в статье [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md).

Если вы создаете запись с такими же именем и типом, как у существующей записи, эту новую запись нужно [добавить в существующий набор записей](#add-a-record-to-an-existing-record-set). Если имя и тип новой и существующих записей отличаются, вам нужно [создать другой набор записей](#create-records-in-a-new-record-set). 

### <a name="create-records-in-a-new-record-set"></a>Создание записей в новом наборе записей

Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. Создавая набор записей, вам нужно определить для него имя, зону, срок жизни (TTL), тип записей и сами создаваемые записи.

Чтобы создать набор записей на вершине зоны (в нашем примере — contoso.com), используйте имя записи "@", включая кавычки. Это общее соглашение при работе с DNS.

В следующем примере создается набор записей с относительным именем www в зоне DNS contoso.com. Полное доменное имя набора записей — www.contoso.com. Тип записи — A, а срок жизни составляет 3600 секунд. Каждый такой набор содержит одну запись с IP-адресом&1;.2.3.4.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Чтобы создать другой набор, содержащий несколько записей, сначала создайте локальный массив и добавьте записи.  Затем передайте этот массив в `New-AzureRmDnsRecordSet`:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>Добавление записи в существующий набор записей

Чтобы добавить запись в существующий набор записей, сделайте следующее.

1. Получите существующий набор записей.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Добавьте новую запись в локальный набор записей. Эта операция выполняется в автономном режиме.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Зафиксируйте изменения в службе Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>Проверка разрешения имен

Записи DNS, представленные на серверах доменных имен Azure DNS, можно проверить с помощью таких средств DNS, как nslookup, dig или [командлет PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно [направить запрос DNS непосредственно к одному из серверов доменных имен для вашей зоны](dns-getstarted-create-dnszone.md#test-name-servers). Обязательно вставьте в следующую команду правильные значения для зоны записей:

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Дальнейшие действия

[Делегирование домена в Azure DNS](dns-domain-delegation.md)

Узнайте, как [управлять записями DNS с помощью PowerShell](dns-operations-dnszones.md).

Узнайте, как [управлять записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md).



