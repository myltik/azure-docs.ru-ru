---
title: Создание зоны DNS с помощью интерфейса командной строки | Microsoft Docs
description: Поэтапно узнайте, как создать зоны DNS для Azure DNS, чтобы разместить домен DNS, используя интерфейс командной строки (CLI).
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee

---
# Создание зоны Azure DNS с помощью интерфейса командной строки
> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-dnszone-cli.md)
> 
> 

Эта статья поможет вам создать зону DNS с помощью интерфейса командной строки. Зону DNS также можно создать с помощью PowerShell или портала Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## Перед началом работы
В данных инструкциях используется интерфейс командной строки Microsoft Azure (CLI). Чтобы использовать команды Azure DNS, обновите интерфейс командной строки Azure до последней версии (0.9.8 или более поздней). Введите команду `azure -v`, чтобы узнать, какая версия интерфейса командной строки Azure установлена на компьютере.

## Шаг 1. Настройка интерфейса командной строки Azure
### 1\. Установка Azure CLI
(доступны версии для Windows, Linux или Mac). Чтобы управлять Azure DNS с помощью интерфейса командной строки Azure, нужно выполнить определенные действия. Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md). Для выполнения команд DNS требуется интерфейс командной строки Azure версии 0.9.8 или более поздней.

Список команд поставщика сети для интерфейса командной строки можно вызвать с помощью следующей команды:

    azure network

### 2) Переключение режима интерфейса командной строки
Azure DNS использует диспетчер ресурсов Azure. Обязательно переключите интерфейс командной строки (CLI) в режим использования команд ARM.

    azure config mode arm

### 3) Вход в учетную запись Azure
Вам будет предложено указать свои учетные данные для проверки подлинности. Обратите внимание, что вы можете использовать только учетные записи ORGID.

    azure login -u "username"

### 4\. Выбор подписки
Выберите подписку Azure.

    azure account set "subscription name"

### 5\. Создание группы ресурсов
В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

Если используется существующая группа ресурсов, можно пропустить этот шаг.

    azure group create -n myresourcegroup --location "West US"


### 6\. регистрация;
Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Эта операция выполняется один раз для каждой подписки.

    azure provider register --namespace Microsoft.Network


## Шаг 2. Создание зоны DNS
Зона DNS создается с помощью команды `azure network dns zone create`. Кроме того, зону DNS можно создать с помощью тегов. Теги — это список пар "имя-значение", которые используются Azure Resource Manager для пометки ресурсов в целях выставления счетов или группировки. Дополнительные сведения о тегах см. в статье [Использование тегов для организации ресурсов Azure](../resource-group-using-tags.md).

В Azure DNS имена зон нужно указывать без точки в конце (**.**). Например, следует указать **contoso.com**, а не **contoso.com.**.

### Создание зоны DNS
В примере ниже будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*.

Пример можно использовать для создания своей зоны DNS. Просто замените указанные значения собственными.

    azure network dns zone create myresourcegroup contoso.com

### Создание зоны DNS и тегов
Интерфейс командной строки для Azure DNS поддерживает теги зон DNS, заданные с помощью необязательного параметра *-Tag*. В следующем примере демонстрируется создание зоны DNS с двумя тегами: project = demo и env = test.

Пример ниже можно использовать для создания зоны DNS и тегов. Просто замените указанные значения собственными.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Просмотр записей
Одновременно с зоной DNS создаются следующие записи DNS:

* Запись SOA. Она находится в корне каждой зоны DNS.
* Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут назначаться различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Чтобы просмотреть эти записи, используйте `azure network dns-record-set show`.<BR> *Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>*

В следующем примере при выполнении команды с группой ресурсов *myresourcegroup*, именем набора записей *@* (для корневой записи) и типом *SOA* будут получены следующие выходные данные:

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
<BR> Чтобы просмотреть записи NS, созданные в зоне, используйте следующую команду:

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

> [!NOTE]
> В наборах записей в корне (или на *вершине*) зоны DNS в качестве имени набора записей используется **@**.
> 
> 

## Тест
Зону DNS можно проверить с помощью таких средств DNS, как nslookup, DIG или командлет PowerShell `Resolve-DnsName`.

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды azure network dns-record-set show выше). Обязательно вставьте правильные значения для вашей зоны в следующую команду.

В следующем примере мы выполним запрос DIG к домену contoso.com, используя серверы доменных имен, назначенные зоне DNS. В запросе DIG следует указать сервер доменных имен, использованный ранее (*@<сервер доменных имен для зоны>*), и имя зоны.

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
Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset-cli.md), чтобы начать разрешение имен для домена Интернета.

<!---HONumber=AcomDC_1005_2016-->