---
title: "Создание записей DNS с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как создать записи узла для Azure DNS и настроить наборы записей и записи с помощью Azure CLI 2.0."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca713647a9f795d7803c32eac7fba9b5b6ac95cf
ms.lasthandoff: 03/01/2017

---

# <a name="how-to-create-dns-records-using-the-azure-cli-20"></a>Создание записей DNS с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создавать записи и наборы записей с помощью Azure CLI.

## <a name="introduction"></a>Введение

Чтобы создавать записи DNS в Azure DNS, нужно понимать, как Azure DNS организует записи DNS в соответствующие наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Дополнительные сведения о записях DNS в Azure DNS см. в статье [Зоны и записи DNS](dns-zones-records.md).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) — интерфейс командной строки нового поколения для модели развертывания Resource Manager.

## <a name="create-a-record-set-and-record"></a>Создание набора записей и записи

В этом разделе описывается создание записей DNS в Azure DNS. Для работы с этими примерами необходимо [установить Azure CLI 2.0, войти в учетную запись и создать зону DNS](dns-getstarted-create-dnszone-cli.md).

Во всех примерах на этой странице используется запись DNS типа A. Сведения о других типах записей и дополнительную информацию об управлении записями DNS и наборами записей см. в статье [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `az network dns record-set ? add` (где ? — это тип записи). Чтобы получить справку, см. `az network dns record-set --help`.

Создавая запись, вам нужно определить для нее имя группы ресурсов, имя зоны, имя набора записей, тип записей и сведения о создаваемой записи.

Если набора записей не существует, эта команда автоматически создаст его. Если набор записей существует, эта команда добавляет в него указанную запись. 

При создании набора записей используется срок жизни (TTL) по умолчанию — 3600. Инструкции по использованию разных сроков жизни см. в статье [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-cli.md).

В следующем примере будет создана запись A с именем *www* в зоне *contoso.com* в группе ресурсов *MyResourceGroup*. IP-адрес записи A — *1.2.3.4*.

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Чтобы создать набор записей на вершине зоны (в нашем примере — contoso.com), используйте имя записи "@", включая кавычки:

```azurecli
az network dns record-set a --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

Параметры, используемые для указания данных записи, различаются в зависимости от типа записи. Например, для записи типа A необходимо указать адрес IPv4, используя параметр `--ipv4-address <IPv4 address>`. Чтобы получить список параметров для других типов записей, см. `az network dns record --help`. Примеры для каждого типа записи см. в статье [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Проверка разрешения имен

Записи DNS, представленные на серверах доменных имен Azure DNS, можно проверить с помощью таких средств DNS, как nslookup, dig или [командлет PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно [направить запрос DNS непосредственно к одному из серверов доменных имен для вашей зоны](dns-getstarted-create-dnszone.md#test-name-servers). Обязательно вставьте правильные значения для зоны записей в следующую команду.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Дальнейшие действия

[Делегирование домена в Azure DNS](dns-domain-delegation.md)

Ознакомьтесь со статьей [Как управлять зонами DNS в службе DNS Azure с помощью интерфейса командной строки Azure](dns-operations-dnszones-cli.md).

Ознакомьтесь со статьей [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-cli.md).


