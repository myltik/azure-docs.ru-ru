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
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: 194da45bade88c2866aaf6066d312d125f1ac2cb

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Создание наборов записей и записей DNS с помощью портала Azure

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)

В этой статье показано, как создавать записи и наборы записей с помощью портала Azure. После создания зоны DNS можно добавить DNS-записи для домена. Для этого сначала требуется изучить DNS-записи и наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Создание набора записей и записи

Следующий пример демонстрирует создание набора записей и записей с помощью портала Azure. Мы используем запись DNS типа "A".

1. Войдите на портал.
2. Перейдите к колонке **Зона DNS** , где требуется создать набор записей.
3. В верхней части этой колонки **Зона DNS** щелкните **Набор записей**, чтобы открыть колонку **Добавление набора записей**.

    ![Новый набор записей](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. В колонке **Add record set** (Добавление набора записей) укажите имя набора записей. Например, набор записей можно назвать**www**.

    ![Add record set](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Выберите тип создаваемой записи. Например, выберите **А**.
6. Задайте значение **Срок жизни**. Срок жизни по умолчанию на портале составляет один час.
7. Добавьте IP-адреса, по одному на строке. Если вы использовали предложенное имя набора записей и тип записи, то следует добавить IPv4-адреса в запись **A** для набора записей www.
8. Завершив добавление IP-адресов, нажмите кнопку **ОК** в нижней части колонки. Создается набор записей DNS.

## <a name="next-steps"></a>Дальнейшие действия

Сведения об управлении записями и набором записей см. в статье [Управление записями и наборами записей DNS с помощью портала Azure](dns-operations-recordsets-portal.md).

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md).



<!--HONumber=Nov16_HO3-->


