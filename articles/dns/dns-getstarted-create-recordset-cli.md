---
title: "Создание набора записей и записей зоны DNS с помощью интерфейса командной строки | Документация Майкрософт"
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
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Создание записей и наборов записей DNS с помощью интерфейса командной строки

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создавать записи и наборы записей с помощью интерфейса командной строки. Для этого сначала требуется изучить DNS-записи и наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

В этом разделе описывается создание записей DNS в Azure DNS. Для работы с этими примерами необходимо [установить Azure CLI, войти в учетную запись и создать зону DNS](dns-getstarted-create-dnszone-cli.md).

Во всех примерах на этой странице используется запись DNS типа A. Сведения о других типах записей и дополнительную информацию об управлении записями DNS и наборами записей см. в статье [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets-cli.md).

## <a name="create-a-record-set-and-record"></a>Создание набора записей и записи

В этом разделе показано, как создавать записи и набор записей. В этом примере создается набор записей с относительным именем www в зоне DNS contoso.com. Полное доменное имя записи — www.contoso.com. Тип записи — A, а срок жизни (TTL) составляет 60 секунд. По завершении этого шага будет создан пустой набор записей.

Чтобы создать набор записей на вершине зоны (в нашем примере — contoso.com), используйте имя записи "@",, включая кавычки. Это общее соглашение при работе с DNS.

### <a name="1-create-a-record-set"></a>1. Создание набора записей

Если у новой записи такое же имя и тип, как у существующей записи, необходимо добавить ее в имеющийся набор записей. Можно пропустить этот шаг и перейти к разделу [Добавление записей](#add-records) ниже. Если имя и тип новой записи отличаются от всех существующих записей, необходимо создать новый набор записей.

Для создания наборов записей используется команда `azure network dns record-set create`. Чтобы получить справку, см. `azure network dns record-set create -h`.  

При создании набора записей необходимо указать его имя, зону, срок жизни и тип записей. 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

По завершении этого шага будет создан пустой набор записей www. Чтобы использовать созданный набор записей www, сначала в него нужно добавить записи.

### <a name="2-add-records"></a>2. Добавление записей

Это можно сделать с помощью `azure network dns record-set add-record`. Чтобы получить справку, см. `azure network dns record-set add-record -h`.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа A вы сможете указать только записи с параметром `-a <IPv4 address>`. Чтобы получить список параметров для других типов записей, см. `azure network dns record-set add-record -h`.

Запись типа A можно добавить в созданный выше набор записей www с помощью следующей команды:

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>Проверка разрешения имен

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




<!--HONumber=Dec16_HO2-->


