<properties
   pageTitle="Приступая к работе с Azure DNS с помощью интерфейса командной строки | Microsoft Azure"
   description="Поэтапно узнайте, как создать зоны DNS для Azure DNS, чтобы разместить домен DNS, используя интерфейс командной строки (CLI)."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Приступая к работе с Azure DNS с помощью интерфейса командной строки



> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)


Домен contoso.com может содержать несколько записей DNS, например mail.contoso.com (для почтового сервера) и www.contoso.com (для веб-сайта). Зона DNS используется для размещения DNS-записей определенного домена.

Для размещения вашего домена сначала нужно создать зону DNS. Записи DNS, создаваемые для определенного домена, будут находиться в зоне DNS этого домена.

В данных инструкциях используется интерфейс командной строки Microsoft Azure (CLI). Чтобы использовать команды Azure DNS, обновите интерфейс командной строки Azure до последней версии.

## Настройка интерфейса командной строки Azure

### Шаг 1.

Установите интерфейс командной строки Azure (доступны версии для Windows, Linux или Mac). Чтобы управлять Azure DNS с помощью интерфейса командной строки Azure, нужно выполнить определенные действия. Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md). Список команд поставщика сети для интерфейса командной строки можно вызвать с помощью следующей команды:

	Azure network


>[AZURE.IMPORTANT]Для выполнения команд DNS требуется интерфейс Azure CLI версии 0.9.8 или более поздней. Введите команду `azure -v`, чтобы узнать, какая версия Azure CLI установлена на этом компьютере.
 
### Шаг 2

Azure DNS использует диспетчер ресурсов Azure. Обязательно переключите интерфейс командной строки (CLI) в режим использования команд arm и DNS.

	Azure config mode arm

### Шаг 3

Войдите в учетную запись Azure.

    Azure login -u "username"

Вам будет предложено указать свои учетные данные для проверки подлинности. Обратите внимание, что вы можете использовать только учетные записи ORGID.

### Шаг 4.
Выберите подписку Azure.

    Azure account set "subscription name"

Чтобы вывести список доступных подписок, используйте команду azure account list.

### Шаг 5.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    Azure group create -n myresourcegroup --location "West US"

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

### Шаг 6

Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Эта операция выполняется один раз для каждой подписки.

	Azure provider register --namespace Microsoft.Network

## Теги

Теги отличаются от Etag. Теги — это собой список пар "имя-значение", которые используются диспетчером ресурсов Azure для отметки ресурсов в целях выставления счетов или группировки. Дополнительные сведения о тегах см. в статье [Использование тегов для организации ресурсов Azure](resource-group-using-tags.md). Интерфейс командной строки для Azure DNS поддерживает теги для зон и наборов записей. Эти теги задаются с помощью параметра -Tag. В следующем примере демонстрируется создание зоны DNS с двумя тегами, "project = demo" и "env = test":

	Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## Создание зоны DNS

Зона DNS создается с помощью команды `azure network dns zone create`. В примере ниже вы создадите зону DNS contoso.com в группе ресурсов MyResourceGroup.

    Azure network dns zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]В Azure DNS имена зон следует указывать без конечной точки ("."), например "contoso.com", а не "contoso.com.".


Ваша зона DNS теперь создана в Azure DNS. Одновременно с зоной DNS создаются следующие записи DNS:

- Запись SOA. Она находится в корне каждой зоны DNS.
- Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут назначаться различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Для вывода этих записей используйте команду azure network dns-record-set show.

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


В следующем примере при выполнении команды с группой ресурсов myresourcegroup, именем набора записей "@" (для корневой записи) и типом SOA будут получены следующие выходные данные.
 

	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Чтобы просмотреть созданные записи NS, используйте следующую команду:

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE]В наборах записей в корне (или на "вершине") зоны DNS в качестве имени набора записей используется "@".

После создания первой зоны DNS ее можно проверить с помощью средств работы с DNS, таких как nslookup, DIG или командлет PowerShell **Resolve-DnsName**. Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды azure network dns-record-set show выше). Обязательно вставьте правильные значения для вашей зоны в следующую команду.

В следующем примере мы выполним запрос DIG к домену contoso.com, используя серверы доменных имен, назначенные зоне DNS. В запросе DIG следует указать сервер имен, который мы использовали ранее (`@<name server for the zone>`), и имя зоны.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## Дальнейшие действия


[Приступая к созданию наборов записей и записей](dns-getstarted-create-recordset-cli.md)<BR> [Управление зонами DNS](dns-operations-dnszones-cli.md)<BR> [Управление DNS-записями](dns-operations-recordsets-cli.md)<BR> [Автоматизация операций Azure с помощью пакета SDK для .NET](dns-sdk.md)<BR> [Справочник по API REST для службы Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=Oct15_HO3-->