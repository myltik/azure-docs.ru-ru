---
title: "Создание зоны DNS и управление ею на портале Azure | Документация Майкрософт"
description: "Сведения о создании зон DNS в Azure DNS. Это пошаговое руководство описывает создание первой зоны DNS и управление ею с помощью портала Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 7119ce18e04b427c268d4d8636b1a5fa02ed0cc3
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Создание зоны DNS на портале Azure

> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Эта статья поможет вам создать зону DNS с помощью портала Azure. Зону DNS также можно создать с помощью PowerShell или интерфейса командной строки.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>Создание зоны DNS

1. Выполните вход на портал Azure.
2. В главном меню щелкните **Создать > Сети**, а затем щелкните **Зона DNS**, чтобы открыть колонку "Создать зону DNS".

    ![Зона DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. В колонке **Создание зоны DNS** укажите имя зоны DNS. Например, *contoso.com*.
 
    ![Создание зоны](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. Выберите группу ресурсов. Можно создать новую группу ресурсов или выбрать уже существующую. Если вы решили создать группу ресурсов, используйте раскрывающийся список **Расположение**, чтобы указать ее расположение. Обратите внимание, что этот параметр относится к расположению группы ресурсов и никак не влияет на расположение зоны DNS. Расположение зоны DNS всегда является "глобальным" и не отображается.

6. Флажок **Закрепить на панели мониторинга** можно оставить установленным, если вы хотите упростить поиск новой зоны на панели мониторинга. Затем щелкните **Создать**.

    ![Закрепить на панели мониторинга](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. После этого отображается новая зона, настраиваемая на панели мониторинга.

    ![Создание](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. При создании зоны для нее открывается колонка на панели мониторинга.

## <a name="view-records"></a>Просмотр записей

При создании зоны DNS также создаются следующие записи:

* Запись SOA. Она находится в корне каждой зоны DNS.
* Полномочные записи серверов имен (NS). Они показывают, на каких серверах размещается зона. Azure DNS использует пул серверов имен, поэтому разным зонам в Azure DNS могут быть назначены различные серверы имен. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

В нижней части колонки "Зона DNS" отображаются наборы записей для зоны DNS.

![зона](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>Проверка серверов доменных имен

Наличие зоны DNS на серверах доменных имен Azure DNS можно проверить с помощью таких инструментов DNS, как nslookup, dig или [командлет PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Если вы еще не делегировали домен для использования новой зоны в Azure DNS, вам нужно направить запрос DNS непосредственно одному из серверов доменных имен для вашей зоны. Серверы доменных имен для вашей зоны перечислены на портале Azure.
    
![зона](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

Обязательно вставьте правильные серверы доменных имен для своей зоны в приведенную ниже команду.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset-portal.md), чтобы создать записи DNS для домена Интернета.


