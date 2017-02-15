---
title: "Управление зонами DNS с помощью интерфейса командной строки | Документация Майкрософт"
description: "Зонами DNS можно управлять с помощью Azure CLI. Как обновлять, удалять и создавать зоны DNS в Azure DNS"
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
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>Как управлять записями DNS с помощью интерфейса командной строки

> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

В этом руководстве показано, как управлять ресурсами зоны DNS с помощью кроссплатформенного интерфейса командной строки Azure (Azure CLI).

В данных инструкциях используется интерфейс командной строки Microsoft Azure (CLI). Чтобы использовать приведенные команды Azure DNS, обновите интерфейс командной строки Azure до последней версии. (доступны версии для Windows, Linux или Mac). Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md).

DNS Azure является исключительно службой диспетчера ресурсов Azure. В этой службе отсутствует классическая модель развертывания. Требуется убедиться, что Azure CLI настроен для использования режима Resource Manager. Для этого воспользуйтесь командой `azure config mode arm`.

Если вы видите сообщение "*ошибка: "dns" не является командой Azure*", скорее всего вы используете Azure CLI в режиме управления службами Azure, а не Resource Manager.

Все команды CLI, относящиеся к Azure DNS, начинаются с `azure network dns`. Справку для каждой команды можно отобразить с помощью параметра `--help` (краткая форма: `-h`).  Например:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `azure network dns zone create`. Чтобы получить справку, см. `azure network dns zone create -h`.

В приведенных ниже примерах показано, как создать зону DNS с или без [тегов Azure Resource Manager](dns-zones-records.md#tags).

### <a name="to-create-a-dns-zone"></a>Создание зоны DNS

В примере ниже будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания своей зоны DNS. Просто замените указанные значения собственными.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Создание зоны DNS с тегами

В следующем примере демонстрируется создание зоны DNS с двумя тегами, *project = demo* и *env = test*, с помощью параметра `--tags` (краткая форма: `-t`).

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Получение зоны DNS

Чтобы получить зону DNS, используйте команду `azure network dns zone show`. Чтобы получить справку, см. `azure network dns zone show -h`.

В следующем примере возвращается зона DNS *contoso.com* и связанные с ней данные из группы ресурсов *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

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

Это команда не обновляет ни один набор записей DNS в пределах зоны (прочитайте статью [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В настоящее время к этим свойствам относятся только [теги Azure Resource Manager](dns-zones-records.md#tags) для ресурса зоны.

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
<br>
Узнайте, как [делегировать свой домен в Azure DNS](dns-domain-delegation.md).




<!--HONumber=Dec16_HO2-->


