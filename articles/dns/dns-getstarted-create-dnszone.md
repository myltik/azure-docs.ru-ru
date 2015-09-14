<properties
   pageTitle="Приступая к работе с Azure DNS | Microsoft Azure"
	description="Узнайте, как создавать зоны DNS для Azure DNS. В этом пошаговом руководстве вы узнаете, как создать вашу первую зону DNS для размещения домена DNS."
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/02/2015"
	ms.author="joaoma"/>

# Приступая к работе с Azure DNS


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)

Домен contoso.com может содержать несколько записей DNS, например mail.contoso.com (для почтового сервера) и www.contoso.com (для веб-сайта). Зона DNS используется для размещения DNS-записей для определенного домена.<BR><BR> Для размещения вашего домена сначала необходимо создать зону DNS. Любые записи DNS, созданные для определенного домена, будут расположены в зоне DNS этого домена.<BR><BR> В данных инструкциях используется Microsoft Azure PowerShell. Обновитесь до последней версии Azure PowerShell, чтобы использовать командлеты Azure DNS. Те же действия можно также выполнить с помощью интерфейса командной строки Microsoft Azure, API-интерфейса REST или SDK.<BR><BR>

## Настройка PowerShell для Azure DNS

Для управления Azure DNS с помощью Azure PowerShell необходимо выполнить следующие действия.

### Шаг 1.
 Azure DNS использует диспетчер ресурсов Azure (ARM). Убедитесь, что выбран режим PowerShell для использования командлетов ARM. Подробнее: [Использование Windows Powershell с диспетчером ресурсов](powershell-azure-resource-manager.md).<BR><BR>

		PS C:\> Switch-AzureMode -Name AzureResourceManager

### Шаг 2.
 Войдите в учетную запись Azure.<BR><BR>

		PS C:\> Add-AzureAccount

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 3.
Выберите, какие подписки Azure будут использоваться. <BR>


		PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Чтобы просмотреть список доступных подписок, выполните командлет Get-AzureSubscription.<BR>

### Шаг 4.
Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).<BR>

		PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"


Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется по умолчанию для ресурсов в этой группе. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.<BR>

### Шаг 5.

Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Это однократная операция для каждой подписки.

	PS c:> Register-AzureProvider -ProviderNamespace Microsoft.Network



## Etag и теги
### Теги Etag
Предположим, что два человека или два процесса пробуют изменить запись DNS одновременно. У какого из них это получится? И будет ли этот человек или процесс знать, что они только что заменили изменения кого-то другого?<BR><BR> Служба Azure DNS использует теги Etag для безопасной обработки параллельных изменений одного ресурса. С каждым ресурсом DNS (зоной или набором записей) связан Etag. При извлечении ресурса также передается его значение Etag. При обновлении ресурса вы можете вернуть Etag, чтобы служба Azure DNS могла сопоставить с Etag на сервере. Так как каждое обновление ресурса приводит к повторному созданию Etag, несовпадение Etag указывает на параллельное изменение. Теги Etag также используются при создании нового ресурса для проверки того, что ресурс не существует.

По умолчанию PowerShell для Azure DNS использует теги Etag для блокировки одновременных изменений зон и наборов записей. С помощью необязательного параметра "-Overwrite" можно отключить проверки тегов Etag. В этом случае все одновременные изменения будут перезаписываться.<BR><BR> На уровне API REST службы Azure DNS теги Etag указываются с помощью заголовков HTTP. Их поведение описывается в следующей таблице:

|Заголовок|Поведение|
|------|--------|
|None|PUT всегда завершается успешно (без проверки Etag)|
|If-match <etag>|PUT завершается успешно, только если ресурс существует и Etag соответствует|
|If-match * |PUT завершается успешно, только если ресурс существует|
|If-none-match |* PUT завершается успешно, только если ресурс не существует|

### Теги
Теги отличаются от Etag. Теги — это собой список пар "имя-значение", которые используются диспетчером ресурсов Azure для отметки ресурсов в целях выставления счетов или группировки. Дополнительные сведения о тегах см. в статье "Использование тегов для организации ресурсов Azure". PowerShell для Azure DNS поддерживает теги для зон и наборов записей, указанных с помощью параметра "-Tag". В следующем примере демонстрируется создание зоны DNS с двумя тегами, "project = demo" и "env = test":

	PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## Создание зоны DNS
Зона DNS создается с помощью командлета New-AzureDnsZone. В примере ниже мы создадим зону DNS contoso.com в группе ресурсов MyResourceGroup:<BR>

		PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE]В Azure DNS имена зон должны указываться без конечной точки ("."). Например, следует указать "contoso.com" вместо "contoso.com.".<BR>


Ваша зона DNS теперь создана в Azure DNS. При создании зоны DNS также создаются следующие записи DNS.<BR>



- Запись SOA. Она находится в корне каждой зоны DNS.
- Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут быть назначены различные серверы имен. Подробнее: [Делегирование домена Azure DNS](dns-domain-delegation.md).<BR>

Для просмотра этих записей используйте командлет Get AzureDnsRecordSet:

		PS C:\> Get-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

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

>[AZURE.NOTE]Наборы записей в корне (или на "вершине") зоны DNS используются "@" в качестве имени набора записей.


После создания первой зоны DNS ее можно проверить с помощью средств работы с DNS, таких как nslookup и dig, или [командлета PowerShell Resolve-DnsName](https://technet.microsoft.com/ru-RU/library/jj590781.aspx).<BR>

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам необходимо направить DNS-запрос непосредственно одному из серверов имен для вашей зоны. Серверы имен для вашей зоны указаны в записях NS, которые отображаются командлетом Get-AzureDnsRecordSet выше. Укажите правильные значения для вашей зоны в команде ниже.<BR>

		C:\> nslookup
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


[Приступая к созданию наборов записей и записей](dns-getstarted-create-recordset.md)<BR> [Управление зонами DNS](dns-operations-dnszones.md)<BR> [Управление DNS-записями](dns-operations-recordsets.md)<BR> [Автоматизация операций Azure с помощью пакета SDK для .NET](dns-sdk.md)<BR> [Справочник по API REST для службы Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=September15_HO1-->