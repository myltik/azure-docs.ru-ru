<properties
   pageTitle="Создание набора записей и записей зоны DNS с помощью интерфейса командной строки | Microsoft Azure"
   description="Как создать записи узла для Azure DNS. Настройка наборов записей и записей с помощью интерфейса командной строки."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="Adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/20/2016"
   ms.author="joaoma"/>


# Создание записей DNS с помощью интерфейса командной строки

> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-recordset-cli.md)
- [PowerShell](dns-getstarted-create-recordset.md)



После создания зоны DNS необходимо добавить DNS-записи для вашего домена. Для этого сначала требуется изучить DNS-записи и наборы записей.


## Общие сведения о наборах записей и записях
У каждой записи DNS есть имя и тип.

_Полное_ имя включает в себя имя зоны, а _относительное_ имя — нет. Например, относительное имя записи "www" в зоне "contoso.com" дает запись с полным именем "www.contoso.com".

>[AZURE.NOTE]В Azure DNS записи указываются с помощью относительных имен.

Записи бывают разных типов в зависимости от данных, которые они содержат. Наиболее распространенный тип — запись "A", которая сопоставляет имя с IPv4-адресом. Другим тип — запись "MX", которая сопоставляет имя с почтовым сервером.

Azure DNS поддерживает все общие типы DNS-записей: A, AAAA, CNAME, MX, NS, SOA, SRV и TXT. (Обратите внимание, что [записи SPF должны создаваться с использованием типа записи TXT](http://tools.ietf.org/html/rfc7208#section-3.1)).

В некоторых случаях необходимо создать несколько записей DNS с заданным именем и типом. Например, предположим, что веб-сайт www.contoso.com размещается по двум разным IP-адресам. Для этого требуется две разные записи A, по одной для каждого IP-адреса:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Вот пример набора записей. Набор записей — это коллекция DNS-записей в зоне с одним именем и типом. Большинство наборов записей обычно содержат одну запись, но встречаются и наборы с несколькими записями (как в примере выше). (Наборы записей типа SOA и CNAME служат исключением, стандарты DNS не допускают несколько записей с одним именем для этих типов.)

Срок жизни или TTL указывает продолжительность кэширования каждой записи клиентами до их повторно запроса. В приведенном выше примере TTL равен 3600 секундам или 1 часу. TTL указывается для набора записей, а не для каждой записи, поэтому одно значение используется для всех записей в рамках данного набора записей.

>[AZURE.NOTE]Azure DNS управляет наборы DNS-записями, используя наборы записей.



## Создание наборов записей и записей 

В примере, приведенном ниже, мы демонстрируем, как создать записи и набор записей. В примере мы используем тип записи DNS «A». Сведения о других типах записей см. в статье [Управление записями DNS](dns-operations-recordsets-cli.md).


### Шаг 1

Создание набора записей

	Usage: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

Относительное имя набора записей в зоне DNS "contoso.com" — "www", поэтому полным доменным именем записей будет "www.contoso.com". Тип записи — "A", а срока жизни составляет 60 секунд.

>[AZURE.NOTE]Чтобы создать набор записей на вершине зоны (в этом случае "contoso.com"), используйте имя записи "@", включая кавычки. Это общее соглашение при работе с DNS.

Набор записей является пустым, поэтому, чтобы использовать недавно созданный набор записей «www», нам нужно будет добавить записи.<BR>

### Шаг 2.

Добавьте записи A (IPv4) в набор записей "www" с помощью следующей команды:

	Usage: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46
	

Изменения завершены. Вы можете получить набор записей из Azure DNS с помощью команды azure network dns-record-set show:


	azure network dns record-set show myresourcegroup "contoso.com" www A
	
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "www"
	data:    Id                              : /subscriptions/########################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 300
	data:    A records:
	data:        IPv4 address                : 134.170.185.46
	data:
	info:    network dns record-set show command OK


Можно также использовать программу nslookup или другие средства DNS для запроса нового набора записей.

>[AZURE.NOTE]Как и при создании зоны, если вы еще не делегировали домен серверам имен Azure DNS, вам потребуется явно указать адрес сервера имен для зоны.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        



## Дальнейшие действия
[Управление записями DNS](dns-operations-dnszones-cli.md)

[Управление записями DNS](dns-operations-recordsets-cli.md)<BR>

[Автоматизация операций Azure с помощью пакета SDK для .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0121_2016-->