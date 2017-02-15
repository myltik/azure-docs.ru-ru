---
title: "Создание набора записей и записей зоны DNS с помощью портала Azure | Документация Майкрософт"
description: "Сведения о создании записей узла для Azure DNS, а также наборов записей и записей с помощью портала Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: b3951106fe2e8607e65bd0ae47fa2ea3346b8ca5

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Создание наборов записей и записей DNS с помощью портала Azure

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создавать записи и наборы записей с помощью портала Azure. Для этого сначала требуется изучить DNS-записи и наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Во всех примерах на этой странице используется запись DNS типа A. Процедура для других типов записей точно такая же.

Если вы создаете запись с такими же именем и типом, как у существующей записи, эту новую запись нужно добавить в существующий набор записей.&mdash; Ознакомьтесь с разделом [Управление записями и наборами записей DNS с помощью портала Azure](dns-operations-recordsets-portal.md). Если имя и тип новой записи отличаются от всех существующих записей, то необходимо создать новый набор записей, как описано ниже.

## <a name="create-records-in-a-new-record-set"></a>Создание записей в новом наборе записей

Следующий пример демонстрирует создание набора записей и записей с помощью портала Azure.

1. Войдите на портал.
2. Перейдите к колонке **Зона DNS** , где требуется создать набор записей.
3. В верхней части этой колонки **Зона DNS** щелкните **Набор записей**, чтобы открыть колонку **Добавление набора записей**.

    ![Новый набор записей](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. В колонке **Add record set** (Добавление набора записей) укажите имя набора записей. Например, набор записей можно назвать**www**.

    ![Add record set](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Выберите тип создаваемой записи. Например, выберите **А**.
6. Задайте значение **Срок жизни**. Срок жизни по умолчанию на портале составляет один час.
7. Добавьте сведения о каждой записи в наборе записей. В этом случае, так как тип записи — "A", необходимо добавить IP-адреса записей A, указывая по одному IP-адресу в каждой строке.
8. Завершив добавление IP-адресов, нажмите кнопку **ОК** в нижней части колонки. Создается набор записей DNS.

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

Сведения об управлении записями и набором записей см. в статье [Управление записями и наборами записей DNS с помощью портала Azure](dns-operations-recordsets-portal.md).



<!--HONumber=Dec16_HO2-->


