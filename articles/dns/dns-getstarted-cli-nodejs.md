---
title: "Приступая к работе со службой DNS Azure с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Узнайте, как создать зону и запись DNS в службе DNS Azure. Это пошаговое руководство описывает создание первых зоны и записи DNS, а также управление ими с помощью интерфейса командной строки Azure CLI 1.0."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 75ddb1ff6dde4a83e775b54c15c86b18d2b1cf7a
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-azure-cli-10"></a>Начало работы со службой DNS Azure с помощью Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

В этой статье описано, как создать первую зону DNS и первую запись DNS, используя кроссплатформенный интерфейс командной строки Azure CLI 1.0, доступный для Windows, Mac и Linux. Эти действия также можно выполнить с помощью портала Azure или Azure PowerShell.

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Наконец, чтобы опубликовать зону DNS в Интернете, необходимо настроить серверы доменных имен для домена. Каждый из этих шагов описан ниже.

При выполнении этих инструкций предполагается, что вы уже установили Azure CLI 1.0 и выполнили вход. Чтобы получить справку, см. статью [Как управлять зонами DNS в службе DNS Azure с помощью Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md).


## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `azure network dns zone create`. Чтобы просмотреть справку для этой команды, введите `azure network dns zone create -h`.

В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```


## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `azure network dns record-set add-record`. Чтобы получить справку, см. `azure network dns record-set add-record -h`.

В следующем примере создается запись с относительным именем www в зоне DNS contoso.com, в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — www.contoso.com. Используется тип записи A с IP-адресом 1.2.3.4 и сроком жизни по умолчанию 3600 секунд (1 час).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Сведения о других типах записей, наборах записей с несколькими записями, других значениях срока жизни, а также об изменении существующих записей см. в статье [Управление записями DNS в Azure DNS с помощью Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).


## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```


## <a name="update-name-servers"></a>Обновление серверов доменных имен

Когда вы убедитесь, что зона и записи DNS настроены правильно, необходимо настроить доменное имя для использования серверов доменных имен службы DNS Azure. Это позволит другим пользователям в Интернете находить ваши записи DNS.

Для получения списка серверов доменных имен для определенной зоны используется команда `azure network dns zone show`.

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 3
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            :
info:    network dns zone show command OK
```

Эти серверы доменных имен необходимо настроить с помощью регистратора доменных имен (у которого было приобретено доменное имя). Регистратор предложит вам вариант настройки серверов доменных имен для домена. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о службе DNS Azure см. в статье [Обзор Azure DNS](dns-overview.md).

Дополнительные сведения об управлении зонами DNS в службе DNS Azure см. в статье [Как управлять зонами DNS в службе DNS Azure с помощью Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md).

Дополнительные сведения об управлении записями DNS в службе DNS Azure см. в статье [Управление записями DNS в Azure DNS с помощью Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md).


