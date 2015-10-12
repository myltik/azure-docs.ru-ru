<properties 
   pageTitle="Управление наборами записей и записями DNS в Azure DNS с помощью интерфейса командной строки | Microsoft Azure" 
   description="Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды интерфейса командной строки для операций с наборами записей и записями." 
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Как управлять записями DNS с помощью интерфейса командной строки

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [Azure Powershell](dns-operations-recordsets.md)

В этом руководстве показано, как управлять наборами записей и записями для зоны DNS.

Важно понимать различие между наборами записей DNS и отдельными записями DNS. Набор записей — это коллекция записей в зоне с одним именем и типом. Подробнее: [Общие сведения о наборах записей и записях](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records).

## Создание набора записей

Наборы записей создаются с помощью команды `azure network dns record-set create`. Вам необходимо указать имя набора записей, зону, время жизни (TTL) и тип записи.

>[AZURE.NOTE]Имя набора записей должно быть относительным, т. е. не содержать имя зоны. Например, имя набора записей www в зоне contoso.com создаст наборе запись с полным именем www.contoso.com.

>В качестве имени набора записей в вершине зоны используйте "@", включая кавычки. Тогда полное имя набора записей будет идентично имени зоны (в этом случае "contoso.com").

Служба Azure DNS поддерживает следующие типы записей: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Наборы записей типа SOA создаются автоматически вместе с каждой зоной, их невозможно создать отдельно.

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT]Наборы записей CNAME не могут сосуществовать с другими наборами записей с тем же именем. Например, невозможно одновременно создать запись CNAME и запись A с относительным именем www. Поскольку вершина зоны (имя = "@") всегда содержит наборы записей NS и SOA, созданные при создании зоны, невозможно создать набор записей CNAME на вершине зоны. Это связано со стандартами DNS, а не ограничениями Azure DNS.

### Записи с подстановочными знаками

Azure DNS поддерживает [записи с подстановочными знаками](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Они возвращаются для любого запроса с совпадающим именем (пока не найдется еще более близкое совпадение из набора записей без подстановочных знаков).

>[AZURE.NOTE]Чтобы создать набор записей с подстановочными знаками, используйте знак "*" вместо имени набора записей или вместо первых символов имени, например "*.foo".

>Наборы записей с подстановочными знаками поддерживаются для всех типов записей, за исключением NS и SOA.

## Получение набора записей
Чтобы получить существующий набор записей, используйте командлет `azure network dns record-set show`, указав группу ресурсов, имя зоны, относительное имя набора записей и тип записи:

	azure network dns record-set show myresourcegroup contoso.com www A


## Перечисление наборов записей

Команда `azure network dns record-set list` позволяет получить список всех записей в зоне DNS:

### Вариант 1 
Вывод списка всех наборов записей. Возвращает все наборы записей, независимо от имени и типа записи:

	azure network dns record-set list myresourcegroup contoso.com

### Вариант 2 

Вывод списка наборов записей указанного типа. Возвращает все наборы записей, соответствующие заданному типу записей (в данном случае это записи A):


	azure network dns record-set list myresourcegroup contoso.com A 

В обоих случаях понадобится указать имя группы ресурсов и имя зоны.

## Добавление записи в набор записей

Записи добавляются в наборы записей с помощью команды `azure network dns record-set add-record`.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа A вы сможете указать записи только с параметром "-a `<IPv4 address>`".

В следующих примерах показано, как создать набор записей каждого типа, содержащий одну запись.

### Создание набора записей с одной записью A

Чтобы создать набор записей, используйте командлет `azure network dns record-set create`, указав группу ресурсов, имя зоны, относительное имя набора записей, тип записи и срок жизни:
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE]Если параметр --ttl не определен, используется значение по умолчанию 4 (секунды).


После создания набора записей A добавьте в набор записей адрес IPv4 с помощью команды `azure network dns record-set add-record`:

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### Создание набора записей AAAA с одной записью

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### Создание набора записей CNAME с одной записью

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE]Записи CNAME позволяют использовать только одно строковое значение.

### Создание набора записей MX с одной записью

Чтобы создать запись MX на вершине зоны (например, "contoso.com"), в этом примере мы используем имя набора записей "@". Так обычно и делается при создании записей MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### Создание набора записей NS с одной записью

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### Создание набора записей SRV с одной записью

При создании записи SRV в корне зоны просто укажите \_service и \_protocol в имени записи — включать ".@" в имя записи не нужно.

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### Создание набора записей TXT с одной записью

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## Изменение существующих наборов записей


Это показано в следующих примерах:

### Обновление записи в существующем наборе записей

В этом примере мы добавим другой IP-адрес (1.2.3.4) в существующий набор записей A (www):

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


Для удаления существующего значения из набора записей будет использоваться команда `azure network dns record-set delete-record`:
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Удаление записи из существующего набора записей

Записи можно удалить из набора записей с помощью команды `azure network dns record-set delete-record`. Обратите внимание, что удаляемая запись должна точно соответствовать существующей записи по всем параметрам.

При удалении последней записи из набора сам набор не удаляется. Подробнее см. в разделе [Удаление набора записей](#delete-a-record-set) ниже.


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Удаление записи AAAA из набора записей

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Удаление записи CNAME из набора записей

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Удаление записи MX из набора записей

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Удаление записи NS из набора записей
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Удаление записи SRV из набора записей

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Удаление записи TXT из набора записей

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## Удаление набора записей
Наборы записей можно удалить с помощью командлета Remove-AzureDnsRecordSet.

>[AZURE.NOTE]Невозможно удалить наборы записей SOA и NS на вершине зоны (имя = "@"), которые создаются автоматически вместе с зоной. Они будут удалены автоматически при удалении зоны.

В следующем примере из зоны DNS contoso.com будет удален набор записей A "test-a":

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

Чтобы отключить запрос подтверждения, можно указать необязательный параметр "-q".


##См. также

[Приступить к созданию наборов записей и записей](dns-getstarted-create-recordset-cli.md)<BR> [Выполнение операций с зонами DNS](dns-operations-dnszones-cli.md)<BR> [Автоматизации операций с помощью пакета SDK для .NET](dns-sdk.md)
 

<!---HONumber=Oct15_HO1-->