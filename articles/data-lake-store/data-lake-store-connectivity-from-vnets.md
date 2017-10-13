---
title: "Подключение к Azure Data Lake Store из виртуальных сетей | Документация Майкрософт"
description: "Подключение к Azure Data Lake Store из виртуальных сетей Azure"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 029939eabc7283d7591576fa1b14731a9e626a88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Доступ к Azure Data Lake Store из виртуальных машин в виртуальной сети Azure
Azure Data Lake Store — это служба PaaS, которая использует общедоступные IP-адреса в Интернете. Любой сервер с доступом к общедоступному Интернету, как правило, может подключаться к конечным точкам Azure Data Lake Storе. По умолчанию все виртуальные машины, которые находятся в виртуальных сетях Azure, могут подключиться к Интернету, а значит и получить доступ к Azure Data Lake Store. Тем не менее для виртуальных машин в виртуальной сети можно настроить ограничение доступа к Интернету. Такие виртуальные машины также не смогут подключаться к Azure Data Lake Store. Блокировать доступ к общедоступному Интернету для виртуальных машин в виртуальных сетях Azure можно с помощью одного из следующих методов.

* Настройка групп безопасности сети (NSG).
* Настройка определяемых пользователем маршрутов (UDR).
* Обмен маршрутами через BGP (стандартный протокол динамической маршрутизации) при использовании подключения ExpressRoute, блокирующего доступ к Интернету.

В этой статье вы узнаете, как включить доступ к Azure Data Lake Store из виртуальных машин Azure, для которых был ограничен доступ к ресурсам, с помощью одного из трех перечисленных выше методов.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Настройка подключения к Azure Data Lake Store из виртуальных машин с ограниченным доступом
Чтобы обеспечить подключение к Azure Data Lake Store из таких виртуальных машин, для них необходимо настроить доступ к IP-адресу, по которому доступна учетная запись Azure Data Lake Store. IP-адреса для учетных записей Data Lake Store данных можно определить, разрешая DNS-имена учетных записей (`<account>.azuredatalakestore.net`). Для этого можно использовать такие средства, как **nslookup**. Откройте окно командной строки на компьютере и выполните следующую команду:

    nslookup mydatastore.azuredatalakestore.net

Результат будет выглядеть, как показано ниже. Значение свойства **Address** — это IP-адрес, связанный с учетной записью Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Настройка подключения из виртуальных машин, ограниченных с помощью NSG
При использовании правила NSG для блокировки доступа к Интернету можно создать другую группу безопасности сети, обеспечивающую доступ к IP-адресу Data Lake Store. Дополнительные сведения о правилах NSG см. в статье [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md). Инструкции по созданию группы безопасности сети см. в статье [Как управлять сетевыми группами безопасности с помощью портала предварительной версии](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Настройка подключения из виртуальных машин, ограниченных с помощью UDR или ExpressRoute
При использовании маршрутов UDR или маршрутов, обмениваемых через BGP, для блокирования доступа к Интернету необходимо настроить специальный маршрут, с помощью которого виртуальные машины в таких подсетях смогут получать доступ к конечным точкам Data Lake Store. Дополнительные сведения см. в статье [Что такое определяемые пользователем маршруты и IP-пересылка?](../virtual-network/virtual-networks-udr-overview.md) Инструкции по созданию определяемых пользователем маршрутов см. в статье [Создание определяемых пользователем маршрутов (UDR) в Resource Manager с помощью PowerShell](../virtual-network/virtual-network-create-udr-arm-ps.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Настройка подключения из виртуальных машин, ограниченных с помощью ExpressRoute
Если настроен канал ExpressRoute, локальные серверы могут получать доступ к Data Lake Store через общедоступный пиринг. Дополнительные сведения о настройке ExpressRoute для общедоступного пиринга см. в статье [Вопросы и ответы по ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Дополнительные материалы
* [Обзор хранилища озера данных Azure](data-lake-store-overview.md)
* [Обеспечение безопасности в Azure Data Lake Store](data-lake-store-security-overview.md)

