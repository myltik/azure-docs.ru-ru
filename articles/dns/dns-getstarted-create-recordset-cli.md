---
title: "Создание записей DNS с помощью Azure CLI | Документация Майкрософт"
description: "Как создать записи узла для Azure DNS. Настройка наборов записей и записей с помощью интерфейса командной строки."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Создание записей DNS с помощью Azure CLI

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создавать записи и наборы записей с помощью Azure CLI.

## <a name="introduction"></a>Введение

Чтобы создавать записи DNS в Azure DNS, нужно понимать, как Azure DNS организует записи DNS в соответствующие наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Дополнительные сведения о записях DNS в Azure DNS см. в статье [Зоны и записи DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Создание набора записей и записи

В этом разделе описывается создание записей DNS в Azure DNS. Для работы с этими примерами необходимо [установить Azure CLI, войти в учетную запись и создать зону DNS](dns-getstarted-create-dnszone-cli.md).

Во всех примерах на этой странице используется запись DNS типа A. Сведения о других типах записей и дополнительную информацию об управлении записями DNS и наборами записей см. в статье [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `azure network dns record-set add-record`. Чтобы получить справку, см. `azure network dns record-set add-record -h`.

Создавая запись, вам нужно определить для нее имя группы ресурсов, имя зоны, имя набора записей, тип записей и сведения о создаваемой записи.

Если набора записей не существует, эта команда автоматически создаст его. Если набор записей существует, эта команда добавляет в него указанную запись. 

При создании набора записей используется срок жизни (TTL) по умолчанию — 3600. Инструкции по использованию разных сроков жизни см. в статье [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-cli.md).

В следующем примере будет создана запись A с именем *www* в зоне *contoso.com* в группе ресурсов *MyResourceGroup*. IP-адрес записи A — *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Чтобы создать набор записей на вершине зоны (в нашем примере — contoso.com), используйте имя записи "@",, включая кавычки:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Параметры, используемые для указания данных записи, различаются в зависимости от типа записи. Например, для записи типа A необходимо указать адрес IPv4, используя параметр `-a <IPv4 address>`. Чтобы получить список параметров для других типов записей, см. `azure network dns record-set add-record -h`. Примеры для каждого типа записи см. в статье [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Проверка разрешения имен

Записи DNS, представленные на серверах доменных имен Azure DNS, можно проверить с помощью таких средств DNS, как nslookup, dig или [командлет PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно [направить запрос DNS непосредственно к одному из серверов доменных имен для вашей зоны](dns-getstarted-create-dnszone.md#test-name-servers). Обязательно вставьте правильные значения для зоны записей в следующую команду.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Дальнейшие действия

[Делегирование домена в Azure DNS](dns-domain-delegation.md)

[Как управлять зонами DNS с помощью PowerShell](dns-operations-dnszones-cli.md)

[Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets-cli.md)




<!--HONumber=Dec16_HO3-->


