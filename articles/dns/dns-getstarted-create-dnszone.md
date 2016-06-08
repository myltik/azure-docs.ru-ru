<properties
   pageTitle="Приступая к работе с Azure DNS | Microsoft Azure"
   description="Узнайте, как создавать зоны DNS для Azure DNS. В этом пошаговом руководстве мы рассмотрим, как создать зону DNS для размещения домена DNS, используя PowerShell."
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
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Создание зоны DNS с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Интерфейс командной строки Azure](dns-getstarted-create-dnszone-cli.md)

Эта статья поможет вам создать зону DNS с помощью интерфейса командной строки. Зону DNS также можно создать с помощью PowerShell или портала Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>О Etag и тегах

### <a name="etags"></a>Теги Etag

Предположим, что два человека или два процесса пробуют изменить запись DNS одновременно. У какого из них это получится? И будет ли этот человек или процесс знать, что они только что заменили изменения кого-то другого?

Служба Azure DNS использует теги Etag для безопасной обработки параллельных изменений одного ресурса. С каждым ресурсом DNS (зоной или набором записей) связан Etag. При извлечении ресурса также передается его значение Etag. При обновлении ресурса вы можете вернуть Etag, чтобы служба Azure DNS могла сопоставить с Etag на сервере. Так как каждое обновление ресурса приводит к повторному созданию Etag, несовпадение Etag указывает на параллельное изменение. Теги Etag также используются при создании нового ресурса для проверки того, что ресурс не существует.

По умолчанию PowerShell для Azure DNS использует теги Etag для блокировки одновременных изменений зон и наборов записей. С помощью необязательного параметра *-Overwrite* можно отключить проверки тегов Etag. В этом случае все одновременные изменения будут перезаписываться.

На уровне API REST службы Azure DNS теги Etag указываются с помощью заголовков HTTP. Их поведение описывается в следующей таблице:

|Заголовок|Поведение|
|------|--------|
|None|PUT всегда завершается успешно (без проверки Etag)|
|If-match <etag>|PUT завершается успешно, только если ресурс существует и Etag соответствует|
|If-match * | PUT завершается успешно, только если ресурс существует|
|If-none-match* |	PUT завершается успешно, только если ресурс не существует|

### <a name="tags"></a>Теги

Теги отличаются от Etag. Теги — это список пар "имя-значение", которые используются Azure Resource Manager для пометки ресурсов в целях выставления счетов или группировки. Дополнительные сведения о тегах см. в статье [Использование тегов для организации ресурсов Azure](../resource-group-using-tags.md).

PowerShell для Azure DNS поддерживает теги для зон и наборов записей, указанных с помощью параметра `-Tag`.


## Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующие компоненты.
	
- Подписка Azure. Если у вас нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрировать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
	
- Вам потребуется установить последнюю версию командлетов PowerShell Azure Resource Manager (1.0 или более позднюю версию). Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Шаг 1. Вход

Откройте консоль PowerShell и подключитесь к своей учетной записи. Дополнительную информацию см. в разделе [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

Для подключения используйте следующий пример.

	Login-AzureRmAccount

Просмотрите подписки учетной записи.

	Get-AzureRmSubscription 

Укажите подписку, которую нужно использовать.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Шаг 2. Создание группы ресурсов

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

Если используется существующая группа ресурсов, можно пропустить этот шаг.

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## Шаг 3. Регистрация

Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Эта операция выполняется один раз для каждой подписки.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## Шаг 4. Создание зоны DNS

Зона DNS создается с помощью командлета `New-AzureRmDnsZone`. Ниже приведены примеры создания зоны DNS с тегами и без них. Дополнительные сведения о тегах см. в разделе о [тегах](#tags) этой статьи.

>[AZURE.NOTE] В Azure DNS имена зон нужно указывать без точки в конце (**.**). Например, следует указать **contoso.com**, а не **contoso.com.**.

### Создание зоны DNS

В примере ниже будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### Создание зоны DNS с тегами

В следующем примере демонстрируется создание зоны DNS с двумя тегами: *project = demo* и *env = test*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## Просмотр записей

Одновременно с зоной DNS создаются следующие записи DNS:

- *Начальная запись зоны* (SOA). Она находится в корне каждой зоны DNS.

- Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут быть назначены различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Чтобы просмотреть эти записи, используйте `Get-AzureRmDnsRecordSet`.

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


В наборах записей в корне (или на *вершине*) зоны DNS в качестве имени набора записей используется **@**.


## Тест

Зону DNS можно проверить с помощью таких инструментов DNS, как nslookup, dig или [командлет PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам необходимо направить DNS-запрос непосредственно одному из серверов имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды `Get-AzureRmDnsRecordSet` выше). Обязательно вставьте правильные значения для вашей зоны в следующую команду.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## Дальнейшие действия

Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset.md), чтобы начать разрешение имен для домена Интернета.

<!---HONumber=AcomDC_0525_2016-->