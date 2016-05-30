<properties
   pageTitle="Создание набора записей и записей зоны DNS с помощью PowerShell | Microsoft Azure"
   description="Как создать записи узла для Azure DNS. Настройка наборов записей и записей с помощью PowerShell"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Создание наборов записей и записей DNS с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создать записи и наборы записей с помощью PowerShell. После создания зоны DNS необходимо добавить DNS-записи для вашего домена. Для этого сначала требуется изучить DNS-записи и наборы записей.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Перед началом работы

Убедитесь, что у вас установлена последняя версия командлетов PowerShell для Azure Resource Manager. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Создание набора записей и записи

В этом разделе будет показано, как создать записи и набор записей.


### 1\. Подключение к подписке 

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

	Login-AzureRmAccount

Просмотрите подписки учетной записи.

	Get-AzureRmSubscription 

Укажите подписку, которую нужно использовать.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Дополнительные сведения о работе с PowerShell см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).


### 2\. Создание набора записей

Наборы записей создаются с помощью командлета `New-AzureRmDnsRecordSet`. При создании набора записей необходимо указать его имя, зону, срок жизни и тип записей.

Чтобы создать набор записей на вершине зоны (в этом случае "contoso.com"), используйте имя записи "@", включая кавычки. Это общее соглашение при работе с DNS.

В примере ниже создается набор записей с относительным именем *www* в зоне DNS *contoso.com*. Полное доменное имя записи будет *www.contoso.com*, тип записей — *A*, и срок жизни — 60 секунд. По завершении этого шага будет создан пустой набор записей *www*, назначенный переменной *$rs*.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Если набор записей уже существует

Если набор записей уже существует, команда завершится ошибкой, если не указать параметр *-Overwrite*. Параметр *-Overwrite* отображает запрос подтверждения, который можно отменить с помощью параметра *-Force*.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


В приведенном выше примере зона задается с помощью имени зоны и имени группы ресурсов. Кроме того, можно указать объект зоны, возвращенный `Get-AzureRmDnsZone` или `New-AzureRmDnsZone`.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

Командлет `New-AzureRmDnsRecordSet` возвращает локальный объект, представляющий набор записей, созданный в Azure DNS.

### 3\. Добавление записи

Чтобы использовать недавно созданный набор записей *www*, в него нужно добавить записи. Вы можете добавить в набор записей *www* записи *A* (IPv4), используя пример ниже. Этот пример основан на переменной $rs, заданной на предыдущем шаге.

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


Можно также использовать программу nslookup или другие средства DNS для запроса нового набора записей.

Если вы еще не делегировали домен серверам доменных имен Azure DNS, вам потребуется явно указать адрес сервера доменных имен для зоны.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## Дополнительные примеры типов записей


В следующих примерах показано, как создать набор записей каждого типа, содержащий одну запись.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## Дальнейшие действия

[Управление записями DNS](dns-operations-dnszones.md)

[Управление записями DNS](dns-operations-recordsets.md)

[Автоматизация операций Azure с помощью пакета SDK для .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0518_2016-->