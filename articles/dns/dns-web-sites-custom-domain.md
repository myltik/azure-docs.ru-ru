<properties 
   pageTitle="Создание пользовательских записей DNS для веб-приложения | Microsoft Azure" 
   description="Как создать пользовательские записи DNS для веб-приложения с помощью Azure DNS. Пошаговые инструкции для проверки владельца домена с помощью записи CNAME или A" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/23/2015"
   ms.author="joaoma"/>

# Создание записи DNS для веб-приложения в пользовательском домене

Службу Azure DNS можно использовать для размещения пользовательского домена для ваших веб-приложений. Например, представьте, что вы создаете веб-приложение Azure и хотите, чтобы пользователи получали к нему доступ, вводя полное доменное имя contoso.com или www.contoso.com. В этом случае вам необходимо создать две записи: корневую запись A, указывающую на contoso.com, и запись CNAME для www-имени, указывающую на запись A.

> [AZURE.NOTE]Помните, что если создать запись A для веб-приложения в Azure, ее необходимо обновить вручную, если основной IP-адрес веб-приложения изменится.

Перед созданием записей для вашего домена требуется создать зону DNS в службе Azure DNS и делегировать зону службе Azure DNS у регистратора. Чтобы создать зону DNS, выполните действия, описанные в статье [Приступая к работе с Azure DNS](../dns-getstarted-create-dnszone/#Create-a-DNS-zone). Для делегирования DNS службе Azure DNS выполните действия, описанные в статье [Делегирование домена Azure DNS](../dns-domain-delegation).
 
## Создание записи A для пользовательского домена

Запись A используется для сопоставления имени с IP-адресом. В следующем примере мы назначим @ как запись A IPv4-адресу:

### Шаг 1.
 
Создайте запись A и назначьте ее переменной $rs.
	
	PS C:\>$rs=New-AzureDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Шаг 2.

Добавьте значение IPv4 в ранее созданный набор записей @ с помощью назначенной переменной $rs. Присвоенное значение IPv4 будет IP-адресом вашего веб-приложения.

> [AZURE.NOTE]Чтобы найти IP-адрес для веб-приложения, выполните действия, описанные в статье [Настройка пользовательского имени домена в службе приложений Azure](../web-sites-custom-domain-name/#Find-the-virtual-IP-address).

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Шаг 3.

Зафиксируйте изменения в наборе записей. Выполните командлет Set-AzureDnsRecordSet, чтобы отправить изменения набора записей в службу Azure DNS:

	Set-AzureDnsRecordSet -RecordSet $rs

## Создание записи CNAME для пользовательского домена

Если предположить, что вашим доменом уже управляет служба Azure DNS (см. раздел [Делегирование домена DNS](../dns-domain-delegation)), можно использовать следующий пример, чтобы создать запись CNAME для contoso.azurewebsites.net:

### Шаг 1.

Откройте PowerShell, создайте набор записей CNAME и назначьте его переменной $rs:

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

При этом будет создан набор записей CNAME со сроком жизни 600 секунд в зоне DNS contoso.com.

### Шаг 2.

После создания набора записей CNAME вам необходимо создать значение псевдонима, которое будет указывать на веб-приложение.

Используя ранее назначенную переменную $rs, можно выполнить следующую команду PowerShell, чтобы создать псевдоним для доменного имени contoso.azurewebsites.net веб-приложения.

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Шаг 3.

Примените изменения, используя командлет Set-AzureDnsRecordSet:

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

Вы можете проверить, была ли запись правильно создана, запросив www.contoso.com с помощью nslookup, как показано ниже:

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

## Создание записи awverify для веб-приложений (только записи A)

Если вы решили использовать запись A для веб-приложения, вам необходимо подтвердить в Azure, что именно вы владелец данного пользовательского домена. Для прохождения этой проверки требуется создать специальную запись CNAME с именем awverify.

В следующем примере запись awverify будет создана для contoso.com, чтобы проверить владельца для пользовательского домена:

### Шаг 1.

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Шаг 2.

После создания набора записей awverify необходимо назначить псевдоним набора записей CNAME домену awverify.contoso.azurewebsites.net, как показано в следующем примере:

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Шаг 3.

Примените изменения с помощью командлета Set-AzureDnsRecordSet, как показано ниже:

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

Теперь вы можете перейти к инструкциям в разделе [Настройка пользовательского имени домена для службы приложений](../web-sites-custom-domain-name), чтобы применить пользовательский домен для веб-приложения.

## См. также

[Управление зонами DNS](../dns-operations-dnszones)

[Управление зонами DNS](../dns-operations-recordsets)

[Обзор диспетчера трафика](../traffic-manager-overview)

[Автоматизация операций Azure с помощью пакета SDK для .NET](../dns-sdk)


 

<!---HONumber=Oct15_HO3-->