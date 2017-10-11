---
title: "Управление записями DNS в Azure DNS с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды CLI 1.0 для операций с наборами записей и записями."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
ms.openlocfilehash: 307b327e4c04a0461e39930114eb193791cbda9a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Управление записями DNS в Azure DNS с помощью Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Портал Azure](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

В этой статье описано, как управлять записями DNS для зоны DNS с помощью кроссплатформенного интерфейса командной строки (CLI) Azure, доступного для Windows, Mac и Linux. Записями DNS также можно управлять с помощью [Azure PowerShell](dns-operations-recordsets.md) или [портала Azure](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) — это интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
* [Azure CLI 2.0](dns-operations-recordsets-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager.

Для работы с этой статьей необходимо [установить Azure CLI 1.0, войти в учетную запись и создать зону DNS](dns-operations-dnszones-cli-nodejs.md).

## <a name="introduction"></a>Введение

Чтобы создавать записи DNS в Azure DNS, нужно понимать, как Azure DNS организует записи DNS в соответствующие наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Дополнительные сведения о записях DNS в Azure DNS см. в статье [Зоны и записи DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `azure network dns record-set add-record`. Чтобы получить справку, см. `azure network dns record-set add-record -h`.

Создавая запись, вам нужно определить для нее имя группы ресурсов, имя зоны, имя набора записей, тип записей и сведения о создаваемой записи. Имя набора записей должно быть *относительным*, т. е. оно не должно содержать имя зоны.

Если набора записей не существует, эта команда автоматически создаст его. Если набор записей существует, эта команда добавляет в него указанную запись.

При создании набора записей используется срок жизни (TTL) по умолчанию — 3600. Инструкции по использованию различных сроков жизни см. в разделе [Создание набора записей DNS](#create-a-dns-record-set).

В следующем примере будет создана запись A с именем *www* в зоне *contoso.com* в группе ресурсов *MyResourceGroup*. IP-адрес записи A — *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Чтобы создать запись на вершине зоны (в нашем примере — contoso.com), используйте имя записи @, включая кавычки:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Создание набора записей DNS

В приведенных выше примерах запись DNS добавлялась к существующему набору записей, или набор записей создавался *неявно*. Кроме того, можно *явно* создать набор записей, прежде чем добавлять в него записи. В Azure DNS поддерживаются пустые наборы записей, которые могут использоваться как заполнители для резервирования имен DNS перед созданием записей DNS. Пустые наборы записей отображаются на панели управления Azure DNS, но не отображаются на серверах имен Azure DNS.

Наборы записей создаются с помощью команды `azure network dns record-set create`. Чтобы получить справку, см. `azure network dns record-set create -h`.

При создании набора записей можно явно указать его свойства, например [срок жизни](dns-zones-records.md#time-to-live) и метаданные. [Метаданные набора записей](dns-zones-records.md#tags-and-metadata) используются для связывания данных приложения с каждым набором записей в виде пар "ключ — значение".

В следующем примере показано, как создать пустой набор записей со сроком жизни 60 секунд. Для этого используется параметр `--ttl` (краткая форма `-l`).

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

В следующем примере показано, как создать набор с двумя записями метаданных: dept=finance и environment=production. Для этого используется параметр `--metadata` (краткая форма `-m`).

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

После создания пустого набора записей в него можно добавить записи с помощью команды `azure network dns record-set add-record`, как описано в разделе [Создание записи DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Создание записей других типов

Вы уже узнали, как создавать записи типа А. В следующем примере показано, как создавать записи других типов, поддерживаемые в Azure DNS.

Параметры, используемые для указания данных записи, различаются в зависимости от типа записи. Например, для записи типа A необходимо указать адрес IPv4, используя параметр `-a <IPv4 address>`. Параметры для каждого типа записи можно перечислить с помощью команды `azure network dns record-set add-record -h`.

В каждом случае мы покажем, как создать одну запись. Запись добавляется в существующий набор или набор, созданный неявно. Дополнительные сведения о создании наборов записей и явной настройке параметра набора см. в разделе [Создание набора записей DNS](#create-a-dns-record-set).

Мы не включаем пример создания набора записей типа SOA, так как такие записи создаются и удаляются только вместе с соответствующей зоной DNS. Тем не менее [записи типа SOA можно изменять, как показано в примере ниже](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Создание записи типа AAAA

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-cname-record"></a>Создание записи CNAME

> [!NOTE]
> Стандарты DNS не допускают использование записей типа CNAME на вершине зоны (`-Name "@"`), а также использование наборов записей, содержащих более одной записи.
> 
> Дополнительные сведения см. в разделе [Записи типа CNAME](dns-zones-records.md#cname-records).

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Создание записи типа MX

Чтобы создать запись MX на вершине зоны (в данном случае "contoso.com"), в этом примере мы используем имя набора записей "@".

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Создание записи типа NS

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Создание записи типа PTR

В этом случае my-arpa-zone.com представляет зону ARPA вашего диапазона IP-адресов. Каждая запись PTR в этой зоне соответствует IP-адресу в этом диапазоне.  Имя записи 10 — это последний октет IP-адреса в этом диапазоне IP-адресов, представленном данной записью.

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>Создание записи типа SRV

Создавая [набор записей SRV](dns-zones-records.md#srv-records), укажите в его имени *\_службу* и *\_протокол*. Если набор записей SRV создается на вершине зоны, включать @ в имя набора записей не нужно.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>Создание записи типа TXT

В следующем примере показано, как создать запись типа ТХТ. Дополнительные сведения о максимальной длине строки, поддерживаемой в записях типа TXT, см. в разделе [Записи типа TXT](dns-zones-records.md#txt-records).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>Получение набора записей

Чтобы извлечь существующий набор записей, используйте команду `azure network dns record-set show`. Чтобы получить справку, см. `azure network dns record-set show -h`.

Как и при создании записи или набора записей, имя набора должно быть *относительным*, т. е. оно не должно содержать имя зоны. Необходимо также указать тип записи, зону, содержащую набор записей, и группу ресурсов, содержащую зону.

В следующем примере показано получение записи *www* типа А из зоны *contoso.com* в группе ресурсов *MyResourceGroup*.

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>Перечисление наборов записей

Команда `azure network dns record-set list` позволяет получить список всех записей в зоне DNS. Чтобы получить справку, см. `azure network dns record-set list -h`.

В этом примере возвращаются все наборы записей в зоне *contoso.com* в группе ресурсов *MyResourceGroup* вне зависимости от имени и типа записи:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

В этом примере возвращаются все наборы записей, соответствующие заданному типу записи (в данном случае это записи типа A).

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>Добавление записи в существующий набор записей

Команду `azure network dns record-set add-record` можно использовать как для создания записи в новом наборе записей, так и для добавления записи в существующий набор.

Дополнительные сведения см. в разделах [Создание записи DNS](#create-a-dns-record) и [Создание записей других типов](#create-records-of-other-types) выше.

## <a name="remove-a-record-from-an-existing-record-set"></a>Удаление записи из существующего набора записей

Для удаления записи DNS из существующего набора используйте команду `azure network dns record-set delete-record`. Чтобы получить справку, см. `azure network dns record-set delete-record -h`.

Эта команда удаляет запись DNS из набора записей. Удаление последней записи в наборе **не** приводит к удалению самого набора. В таком случае набор записей будет пустым. Чтобы удалить набор, см. инструкции в разделе [Удаление набора записей](#delete-a-record-set).

Необходимо указать удаляемую запись и зону, из которой ее следует удалить, используя те же параметры, что и при создании записи с помощью команды `azure network dns record-set add-record`. Эти параметры описаны в предыдущих разделах [Создание записи DNS](#create-a-dns-record) и [Создание записей других типов](#create-records-of-other-types).

Эта команда запрашивает подтверждение. Этот запрос можно скрыть с помощью параметра `--quiet` (краткая форма `-q`).

В следующем примере показано удаление записи типа A со значением "1.2.3.4" из набора записей с именем *www* в зоне *contoso.com* в группе ресурсов *MyResourceGroup*. Запрос подтверждения скрыт.

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>Обновление существующего набора записей

В каждом наборе записей указан [срок жизни](dns-zones-records.md#time-to-live), [метаданные](dns-zones-records.md#tags-and-metadata) и записи DNS. В следующих разделах описано, как изменять эти свойства.

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>Изменение записи типа A, AAAA, MX, NS, PTR, SRV или TXT

Чтобы изменить существующую запись типа A, AAAA, MX, NS, PTR, SRV или TXT, сначала следует добавить новую запись, а затем удалить существующую. Подробные инструкции об удалении и добавлении записей см. в предыдущих разделах этой статьи.

В следующем примере показано, как в записи типа А изменить IP-адрес 1.2.3.4 на IP-адрес 5.6.7.8.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>Изменение записи CNAME

Чтобы изменить запись CNAME, используйте команду `azure network dns record-set add-record` для добавления нового значения записи. В отличие от других типов записей, набор записей CNAME может содержать только одну запись. Таким образом, при добавлении новой записи существующая запись *заменяется*, и ее не нужно удалять отдельно.  Отобразится запрос на подтверждение этой замены.

В примере ниже показано изменение набора записей CNAME с именем *www* в зоне *contoso.com* в группе ресурсов *MyResourceGroup* таким образом, чтобы он указывал на www.fabrikam.net вместо существующего значения.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Изменение записи типа SOA

Чтобы изменить запись типа SOA для определенной зоны DNS, используйте команду `azure network dns record-set set-soa-record`. Чтобы получить справку, см. `azure network dns record-set set-soa-record -h`.

В примере ниже показано, как задать свойство email записи типа SOA для зоны *contoso.com* в группе ресурсов *MyResourceGroup*.

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>Изменение записи NS на вершине зоны

Набор записей типа NS на вершине зоны автоматически создается вместе с каждой зоной DNS. Он содержит имена DNS-серверов Azure, назначенные зоне.

Вы можете добавить дополнительные имена серверов в этот набор записей NS, обеспечив поддержку совместного размещения доменов с использованием более чем одного поставщика DNS. Вы также можете изменить срок жизни и метаданные для этого набора записей. При этом вы не можете удалить или изменить предварительно заполненные серверы доменных имен Azure DNS.

Обратите внимание, что это относится только к набору записей NS на вершине зоны. Другие наборы записей NS в зоне (используемые для делегирования дочерних зон) можно изменять без ограничений.

В следующем примере показано, как добавить дополнительный сервер в набор записей NS на вершине зоны:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Изменение срока жизни существующего набора записей

Для изменения срока жизни существующего набора записей используйте команду `azure network dns record-set set`. Чтобы получить справку, см. `azure network dns record-set set -h`.

В примере ниже показано, как изменить срок жизни набора записей. В этом случае устанавливается срок в 60 секунд.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Изменение метаданных существующего набора записей

[Метаданные набора записей](dns-zones-records.md#tags-and-metadata) используются для связывания данных приложения с каждым набором записей в виде пар "ключ — значение". Для изменения метаданных существующего набора записей используйте команду `azure network dns record-set set`. Чтобы получить справку, см. `azure network dns record-set set -h`.

В следующем примере показано, как изменить набор данных с двумя записями метаданных: dept=finance и environment=production. Для этого используется параметр `--metadata` (краткая форма `-m`). Обратите внимание, что все существующие метаданные *заменяются* заданными значениями.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>Удаление набора записей

Наборы записей можно удалить с помощью команды `azure network dns record-set delete`. Чтобы получить справку, см. `azure network dns record-set delete -h`. При удалении набора записей также удаляются все содержащиеся в нем записи.

> [!NOTE]
> Удалить наборы записей типа SOA и NS на вершине зоны (`-Name "@"`) нельзя.  Эти записи создаются и удаляются автоматически вместе с зоной.

В следующем примере показано удаление набора записей с именем *www* типа А из зоны *contoso.com* в группе ресурсов *MyResourceGroup*.

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

Отобразится запрос на подтверждение операции удаления. Чтобы скрыть этот запрос, используйте параметр `--quiet` (краткая форма `-q`).

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о [зонах и записях в Azure DNS](dns-zones-records.md).
<br>
Узнайте, как [защитить зоны и записи](dns-protect-zones-recordsets.md) при использовании Azure DNS.
