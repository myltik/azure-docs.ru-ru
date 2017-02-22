---
title: "Управление зонами DNS в службе DNS Azure с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Зонами DNS можно управлять с помощью Azure CLI. В этой статье показано, как обновлять, удалять и создавать зоны DNS в службе DNS Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 8519812977c7cc042243e47b2e80bb9605fc9ddc

---

# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Как управлять зонами DNS в службе DNS Azure с помощью интерфейса командной строки Azure

> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

В этом руководстве показано, как управлять зонами DNS с помощью кроссплатформенного интерфейса командной строки Azure, доступного для Windows, Mac и Linux. Зонами DNS также можно управлять с помощью [Azure PowerShell](dns-operations-dnszones.md) или портала Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>Получение справки

Все команды CLI, относящиеся к Azure DNS, начинаются с `azure network dns`. Справку для каждой команды можно отобразить с помощью параметра `--help` (краткая форма: `-h`).  Например:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `azure network dns zone create`. Чтобы получить справку, см. `azure network dns zone create -h`.

В следующем примере создается зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*:

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Создание зоны DNS с тегами

В следующем примере демонстрируется создание зоны DNS с двумя [тегами Azure Resource Manager](dns-zones-records.md#tags) (*project = demo* и *env = test*) с помощью параметра `--tags` (краткая форма: `-t`):

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Получение зоны DNS

Чтобы получить зону DNS, используйте команду `azure network dns zone show`. Чтобы получить справку, см. `azure network dns zone show -h`.

В следующем примере возвращается зона DNS *contoso.com* и связанные с ней данные из группы ресурсов *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

Ниже приведен пример ответа.

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Обратите внимание, что записи DNS не возвращаются командой `azure network dns zone show`. Для вывода списка записей DNS используйте `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Перечисление зон DNS

Чтобы перечислить зоны DNS, используйте `azure network dns zone list`. Чтобы получить справку, см. `azure network dns zone list -h`.

Если указать группу ресурсов, то будут перечислены только зоны в этой группе ресурсов.

```azurecli
azure network dns zone list MyResourceGroup
```

Если не указать группу ресурсов, то будут перечислены все зоны в подписке.

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Обновление зоны DNS

Изменить ресурс зоны DNS можно с помощью командлета `azure network dns zone set`. Чтобы получить справку, см. `azure network dns zone set -h`.

Это команда не обновляет ни один набор записей DNS в пределах зоны (прочитайте статью [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets-cli.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В настоящее время к этим свойствам относятся только [теги Azure Resource Manager](dns-zones-records.md#tags) для ресурса зоны.

В приведенном ниже примере показано, как обновить теги для зоны DNS. Существующие теги заменяются указанным значением.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Удаление зоны DNS

Зоны DNS можно удалить с помощью команды `azure network dns zone delete`. Чтобы получить справку, см. `azure network dns zone delete -h`.

> [!NOTE]
> При удалении зоны DNS также удаляются все записи DNS в этой зоне. Эту операцию нельзя отменить. Если зона DNS используется, то после ее удаления произойдет сбой служб, которые ее используют.
>
>Сведения о защите от случайного зоны удаления см. в разделе [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Как защитить зоны и записи DNS).

Эта команда запрашивает подтверждение. Необязательный параметр `--quiet` (краткая форма: `-q`) позволяет отключить этот запрос.

В следующем примере показано, как удалить зону *contoso.com* из группы ресурсов *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [управлять наборами записей и записями](dns-getstarted-create-recordset-cli.md) в зоне DNS.

Узнайте, как [делегировать свой домен в Azure DNS](dns-domain-delegation.md).




<!--HONumber=Feb17_HO2-->


