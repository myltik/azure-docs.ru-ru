---
title: "Создание зоны DNS с помощью интерфейса командной строки | Документация Майкрософт"
description: "Сведения о создании зон DNS в Azure DNS. Это пошаговое руководство описывает создание первой зоны DNS и управление ею с помощью интерфейса командной строки Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Создание зоны Azure DNS с помощью интерфейса командной строки

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-dnszone-cli.md)

В этой статье описано, как создать зону DNS с помощью кроссплатформенного интерфейса командной строки Azure, доступного для Windows, Mac и Linux. Зону DNS также можно создать с помощью PowerShell или портала Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Необходимо установить последнюю версию интерфейса командной строки Azure для Windows, Linux или Mac. Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md).

## <a name="step-1---sign-in-and-create-a-resource-group"></a>Шаг 1. Вход и создание группы ресурсов

### <a name="switch-cli-mode"></a>Переключение режима интерфейса командной строки

Azure DNS использует диспетчер ресурсов Azure. Обязательно переключите интерфейс командной строки (CLI) в режим использования команд ARM.

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

Вам будет предложено указать свои учетные данные для проверки подлинности. Обратите внимание, что вы можете использовать только учетные записи OrgID.

```azurecli
azure login
```

### <a name="select-the-subscription"></a>Выбор подписки

Просмотрите подписки учетной записи.

```azurecli
azure account list
```

Выберите подписку Azure.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

Если используется существующая группа ресурсов, можно пропустить этот шаг.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Регистрация поставщика ресурсов

Служба Azure DNS управляется поставщиком ресурсов Microsoft.Network. Вашу подписку Azure необходимо зарегистрировать, чтобы использовать этот поставщик ресурсов, прежде чем работать с Azure DNS. Эта операция выполняется один раз для каждой подписки.

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>Шаг 2. Создание зоны DNS

Зона DNS создается с помощью команды `azure network dns zone create`. Чтобы просмотреть справку для этой команды, введите `azure network dns zone create -h`.

В примере ниже будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>Шаг 3. Проверка

### <a name="view-records"></a>Просмотр записей

Одновременно с зоной DNS создаются следующие записи DNS:

* Начальная запись зоны (SOA). Она находится в корне каждой зоны DNS.
* Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут назначаться различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Чтобы просмотреть эти записи, используйте `azure network dns-record-set list`.

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> В наборах записей в корне (или на *вершине*) зоны DNS в качестве имени набора записей используется **@** .

### <a name="test-name-servers"></a>Проверка серверов доменных имен

Зону DNS, представленную на серверах доменных имен Azure DNS, можно проверить с помощью таких средств DNS, как nslookup, dig или командлет PowerShell `Resolve-DnsName`.

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов доменных имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды azure network dns-record-set show выше). Обязательно вставьте правильные значения для вашей зоны в следующую команду.

В следующем примере мы выполним запрос dig к домену contoso.com, используя серверы доменных имен, назначенные зоне DNS. В запросе dig следует указать сервер доменных имен, использованный ранее (*@\<сервер доменных имен для зоны\>*), и имя зоны.

     <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
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
    contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset-cli.md), чтобы создать записи DNS для домена Интернета.




<!--HONumber=Dec16_HO2-->


