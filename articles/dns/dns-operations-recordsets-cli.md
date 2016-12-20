---
title: "Управление наборами записей и записями DNS в Azure DNS с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Управляйте наборами записей и записями DNS в службе Azure DNS при размещении вашего домена в Azure DNS. Все команды интерфейса командной строки для операций с наборами записей и записями."
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
ms.date: 09/22/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 2bc18d618cf8838209bea9f8a2d323e3b1042709

---

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Управление записями и наборами записей DNS с помощью CLI

> [!div class="op_single_selector"]
> * [Портал Azure](dns-operations-recordsets-portal.md)
> * [Интерфейс командной строки Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

В этой статье описано, как управлять наборами записей и записями для зоны DNS с помощью кроссплатформенного интерфейса командной строки Azure (CLI).

Важно понимать различие между наборами записей DNS и отдельными записями DNS. Набор записей — это коллекция записей в зоне, которые имеют одно имя и один тип. Дополнительные сведения см. в статье [Создание записей и наборов записей DNS с помощью интерфейса командной строки](dns-getstarted-create-recordset-cli.md).

## <a name="configure-the-cross-platform-azure-cli"></a>Настройка кроссплатформенного Azure CLI

DNS Azure является исключительно службой диспетчера ресурсов Azure. Она не поддерживает API управления службами Azure. Убедитесь, что Azure CLI настроен для использования режима Resource Manager, выполнив команду `azure config mode arm` .

Если вы видите сообщение " **ошибка: "dns" не является командой Azure**", скорее всего вы используете Azure CLI в режиме управления службами Azure, а не Resource Manager.

## <a name="create-a-new-record-set-and-record"></a>Создание нового набора записей и записи

Сведения о том, как создать набор записей на портале Azure, см. в статье [Создание записей и наборов записей DNS с помощью интерфейса командной строки](dns-getstarted-create-recordset-cli.md).

## <a name="retrieve-a-record-set"></a>Извлечение набора записей

Чтобы извлечь существующий набор записей, используйте команду `azure network dns record-set show`. Укажите группу ресурсов, имя зоны, относительное имя набора записей и тип записей. Используйте приведенный пример, подставив собственные значения.

```azurecli
azure network dns record-set show myresourcegroup contoso.com www A
```

## <a name="list-record-sets"></a>Перечисление наборов записей

Команда `azure network dns record-set list` позволяет получить список всех записей в зоне DNS. Нужно указать имя группы ресурсов и имя зоны.

### <a name="to-list-all-record-sets"></a>Вывод списка всех наборов записей

Этот пример возвращает все наборы записей, независимо от имени и типа записей.

```azurecli
azure network dns record-set list myresourcegroup contoso.com
```

### <a name="to-list-record-sets-of-a-given-type"></a>Вывод списка наборов записей указанного типа

Этот пример возвращает все наборы записей, соответствующие заданному типу записей (в данном случае это записи A).

```azurecli
azure network dns record-set list myresourcegroup contoso.com A
```

## <a name="add-a-record-to-a-record-set"></a>Добавление записи в набор записей

Для добавления записей в наборы записей используется команда `azure network dns record-set add-record`. Параметры для добавления записей в набор записей зависят от типа настраиваемой записи. Например, при использовании набора записей типа "A" вы сможете указать только записи с параметром `-a <IPv4 address>`.

Чтобы создать набор записей, используйте команду `azure network dns record-set create`. Укажите группу ресурсов, имя зоны, относительное имя набора записей, тип записи и срок жизни. Если параметр `--ttl` не определен, по умолчанию используется значение в 4 секунды.

```azurecli
azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

После создания набора записей A добавьте в него IPv4-адрес с помощью команды `azure network dns record-set add-record`.

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

В следующих примерах показано, как создать набор записей каждого типа. Каждый такой набор содержит одну запись.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="update-a-record-in-a-record-set"></a>Обновление записи в наборе записей

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Добавление еще одного IP-адреса (1.2.3.4) в существующий набор записей A (www)

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

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Удаление существующего значения из набора записей

Используйте `azure network dns record-set delete-record`.

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
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK

## <a name="remove-a-record-from-a-record-set"></a>Удаление записи из набора записей

Записи можно удалить из набора записей с помощью команды `azure network dns record-set delete-record`. Удаляемая запись должна точно соответствовать существующей записи по всем параметрам.

При удалении последней записи из набора сам набор не удаляется. Дополнительные сведения см. в разделе [Удаление набора записей](#delete) этой статьи.

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

azure network dns record-set delete myresourcegroup contoso.com www A
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Удаление записи AAAA из набора записей

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Удаление записи CNAME из набора записей

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Удаление записи MX из набора записей

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5
```

### <a name="remove-an-ns-record-from-record-set"></a>Удаление записи NS из набора записей

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

### <a name="remove-a-ptr-record-from-a-record-set"></a>Удаление записи типа PTR из набора записей

В этом случае my-arpa-zone.com представляет зону ARPA вашего диапазона IP-адресов.  Каждая запись типа PTR в этой зоне соответствует IP-адресу в этом диапазоне.

```azurecli
azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Удаление записи SRV из набора записей

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Удаление записи типа TXT из набора записей

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"
```

## <a name="a-namedeleteadelete-a-record-set"></a><a name="delete"></a>Удаление набора записей

Наборы записей можно удалять с помощью командлета `Remove-AzureRmDnsRecordSet` . Невозможно удалить наборы записей типа SOA и NS в вершине зоны (имя = "@")), созданные автоматически вместе с зоной. Они будут удалены автоматически при удалении зоны.

В следующем примере из зоны DNS contoso.com будет удален набор записей A test-a.

```azurecli
azure network dns record-set delete myresourcegroup contoso.com  "test-a" A
```

Чтобы отключить запрос подтверждения, можно указать необязательный параметр `-q`.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md). Сведения об автоматизации DNS см. в статье [Создание зон и наборов записей DNS с помощью пакета SDK для .NET](dns-sdk.md).

Если вы хотите работать с обратными записями DNS, ознакомьтесь с разделом [Как управлять обратными записями DNS для служб с помощью Azure CLI](dns-reverse-dns-record-operations-cli.md).



<!--HONumber=Nov16_HO3-->


