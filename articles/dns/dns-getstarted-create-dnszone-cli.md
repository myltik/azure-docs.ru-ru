---
title: "Создание зоны DNS с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Сведения о создании зон DNS в Azure DNS. Это пошаговое руководство описывает создание первой зоны DNS и управление ею с помощью интерфейса командной строки Azure 2.0."
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Создание зоны Azure DNS с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

В этой статье описано, как создать зону DNS с помощью кроссплатформенного интерфейса командной строки Azure, доступного для Windows, Mac и Linux. Зону DNS также можно создать с помощью [Azure PowerShell](dns-getstarted-create-dnszone.md) или [портала Azure](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) — интерфейс командной строки нового поколения для модели развертывания Resource Manager.

## <a name="introduction"></a>Введение

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Настройка Azure CLI 2.0 для Azure DNS

### <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).

* Установите последнюю версию интерфейса командной строки Azure для Windows, Linux или Mac. Дополнительные сведения см. в статье [Install the Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2) (Установка Azure CLI 2.0).

### <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

Откройте окно консоли и пройдите проверку подлинности с помощью своих учетных данных. Дополнительные сведения см. в статье "Вход в Azure из интерфейса командной строки".

```azurecli
az login
```

### <a name="select-the-subscription"></a>Выбор подписки

Просмотрите подписки учетной записи.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Выберите подписку Azure.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Но так как все ресурсы DNS глобальные, а не региональные, выбор расположения группы ресурсов не влияет на Azure DNS.

Если используется существующая группа ресурсов, можно пропустить этот шаг.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `az network dns zone create`. Чтобы просмотреть справку для этой команды, введите `az network dns zone create --help`.

В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Проверка зоны DNS

### <a name="view-records"></a>Просмотр записей

Одновременно с зоной DNS создаются следующие записи DNS:

* *Начальная запись зоны* (SOA). Она находится в корне каждой зоны DNS.
* Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут быть назначены различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

Чтобы просмотреть эти записи, используйте `az network dns record-set list`.

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

Ответ для `az network dns record-set list` приведен ниже.

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> В наборах записей в корне (или на *вершине*) зоны DNS в качестве имени набора записей используется **@** .

### <a name="test-name-servers"></a>Проверка серверов доменных имен

Зону DNS, представленную на серверах доменных имен Azure DNS, можно проверить с помощью таких средств DNS, как nslookup, dig или командлет PowerShell `Resolve-DnsName`.

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов доменных имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены в записях NS (см. результат выполнения команды `az network dns record-set list`).

В следующем примере мы выполним запрос dig к домену contoso.com, используя серверы доменных имен, назначенные зоне DNS. Обязательно вставьте правильные значения для вашей зоны.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
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
```

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, [создайте набор записей и записи DNS](dns-getstarted-create-recordset-cli.md) в своей зоне.


