---
title: Создание пользовательских записей DNS для веб-приложения | Документация Майкрософт
description: Как создать записи DNS личного домена для веб-приложения с помощью Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: 00a56a2683e95e70bb13acd6b936e766f044e1cd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Создание записей DNS для веб-приложения в пользовательском домене

Службу Azure DNS можно использовать для размещения пользовательского домена для ваших веб-приложений. Например, вы создаете веб-приложение Azure и хотите, чтобы пользователи получали к нему доступ, вводя полное доменное имя contoso.com или www.contoso.com.

Для этого необходимо создать две записи:

* корневую запись A, указывающую на contoso.com;
* запись CNAME для имени www, которая указывает на запись A.

Помните, что если создать запись A для веб-приложения в Azure, ее необходимо обновить вручную, если основной IP-адрес веб-приложения изменится.

## <a name="before-you-begin"></a>Перед началом работы

Прежде чем начать, нужно создать зону DNS в Azure DNS и делегировать зону у вашего регистратора в Azure DNS.

1. Чтобы создать зону DNS, выполните действия, описанные в разделе [Создание зоны DNS](dns-getstarted-create-dnszone.md).
2. Для делегирования DNS в Azure DNS выполните действия, описанные в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

После создания зоны и ее делегирования в Azure DNS можно создать записи для личного домена.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Создание записи A для пользовательского домена

Запись A используется для сопоставления имени с IP-адресом. В примере ниже мы назначим @ как запись A IPv4-адресу.

### <a name="step-1"></a>Шаг 1

Создайте запись A и назначьте ее переменной $rs.

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Шаг 2

Добавьте значение IPv4 в ранее созданный набор записей @ с помощью назначенной переменной $rs. Присвоенное значение IPv4 будет IP-адресом вашего веб-приложения.

Чтобы найти IP-адрес для веб-приложения, выполните действия, описанные в статье [Настройка личного доменного имени для службы приложений Azure](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Шаг 3.

Зафиксируйте изменения в наборе записей. Выполните командлет `Set-AzureRMDnsRecordSet`, чтобы передать изменения набора записей в Azure DNS.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Создание записи CNAME для личного домена

Если вашим доменом уже управляет Azure DNS (см. статью [Делегирование домена в Azure DNS](dns-domain-delegation.md)), можно использовать следующий пример, чтобы создать запись CNAME для contoso.azurewebsites.net.

### <a name="step-1"></a>Шаг 1

Откройте PowerShell, создайте набор записей CNAME и назначьте его переменной $rs. В этом примере будет создан набор записей CNAME со сроком жизни 600 секунд в зоне DNS contoso.com.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

Ниже приведен пример ответа.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Шаг 2

После создания набора записей CNAME вам необходимо создать значение псевдонима, которое будет указывать на веб-приложение.

Используя ранее назначенную переменную $rs, можно выполнить следующую команду PowerShell, чтобы создать псевдоним для доменного имени contoso.azurewebsites.net веб-приложения.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

Ниже приведен пример ответа.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Шаг 3.

Зафиксируйте изменения с помощью командлета `Set-AzureRMDnsRecordSet` .

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Вы можете проверить, была ли запись правильно создана, запросив www.contoso.com с помощью nslookup, как показано ниже:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Создание записи awverify для веб-приложений

Если вы решили использовать запись A для веб-приложения, вам необходимо пройти проверку и подтвердить, что именно вы являетесь владельцем данного пользовательского домена. Для прохождения этой проверки требуется создать специальную запись CNAME с именем awverify. Этот раздел относится только к записям A.

### <a name="step-1"></a>Шаг 1

Создайте запись awverify. В следующем примере мы создадим запись awverify для contoso.com, чтобы проверить владельца для личного домена.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

Ниже приведен пример ответа.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Шаг 2

После создания набора записей awverify назначьте псевдоним набора записей CNAME. В приведенном ниже примере в качестве псевдонима набора записей CNAME мы назначим awverify.contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

Ниже приведен пример ответа.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Шаг 3.

Зафиксируйте изменения с помощью командлета `Set-AzureRMDnsRecordSet cmdlet`, как показано ниже.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Дополнительная информация

Следуйте указаниям в разделе [Настройка личного доменного имени для службы приложений Azure](../app-service/app-service-web-tutorial-custom-domain.md) , чтобы настроить веб-приложение для использования личного домена.
