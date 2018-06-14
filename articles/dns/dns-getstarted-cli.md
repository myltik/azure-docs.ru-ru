---
title: Приступая к работе со службой DNS Azure с помощью Azure CLI 2.0 | Документация Майкрософт
description: Узнайте, как создать зону и запись DNS в службе DNS Azure. Это пошаговое руководство описывает создание первых зоны и записи DNS, а также управление ими с помощью интерфейса командной строки Azure CLI 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: d24eaa4974f8bff09b337384e4fd139edb6ebd70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30175245"
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Начало работы со службой DNS Azure с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [портал Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

В этой статье описано, как создать первую зону DNS и первую запись DNS, используя кроссплатформенный интерфейс командной строки Azure CLI 2.0, доступный для Windows, Mac и Linux. Эти действия также можно выполнить с помощью портала Azure или Azure PowerShell.

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Наконец, чтобы опубликовать зону DNS в Интернете, необходимо настроить серверы доменных имен для домена. Каждый из этих шагов описан ниже.

При выполнении этих инструкций предполагается, что вы уже установили Azure CLI 2.0 и выполнили вход. Чтобы получить справку, см. статью [Как управлять зонами DNS в службе DNS Azure с помощью Azure CLI 2.0](dns-operations-dnszones-cli.md).

Теперь Azure DNS также поддерживает частные зоны DNS (сейчас в виде общедоступной предварительной версии). Дополнительные сведения об использовании частных зон DNS см. в статье [Using Azure DNS for private domains](private-dns-overview.md) (Использование Azure DNS для частных доменов). Инструкции по созданию частной зоны DNS см. в статье [Get started with Azure DNS private zones using CLI](./private-dns-getstarted-cli.md) (Начало работы с частными зонами Azure DNS с помощью CLI).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создается группа ресурсов, которая будет включать эту зону DNS. Ниже показана команда для создания группы ресурсов.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `az network dns zone create`. Чтобы просмотреть справку для этой команды, введите `az network dns zone create -h`.

В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `az network dns record-set [record type] add-record`. Чтобы получить справку, например по записям типа A, см. `azure network dns record-set A add-record -h`.

В следующем примере создается запись с относительным именем www в зоне DNS contoso.com, в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — www.contoso.com. Используется тип записи A с IP-адресом 1.2.3.4 и сроком жизни по умолчанию 3600 секунд (1 час).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Сведения о других типах записей, наборах записей с несколькими записями, других значениях срока жизни, а также об изменении существующих записей см. в статье [Управление записями и наборами записей DNS в Azure DNS с помощью Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Обновление серверов доменных имен

Когда вы убедитесь, что зона и записи DNS настроены правильно, необходимо настроить доменное имя для использования серверов доменных имен службы DNS Azure. Это позволит другим пользователям в Интернете находить ваши записи DNS.

Для получения списка серверов доменных имен для определенной зоны используется команда `az network dns zone show`. Чтобы просмотреть имена серверов доменных имен, используйте выходные данные JSON, как показано в следующем примере:

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Эти серверы доменных имен необходимо настроить с помощью регистратора доменных имен (у которого было приобретено доменное имя). Регистратор предложит вам вариант настройки серверов доменных имен для домена. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Удаление всех ресурсов
 
Чтобы удалить все ресурсы, созданные при работе с этой статьей, сделайте следующее:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о службе DNS Azure см. в статье [Обзор Azure DNS](dns-overview.md).

Дополнительные сведения об управлении зонами DNS в службе DNS Azure см. в статье [Как управлять зонами DNS в службе DNS Azure с помощью Azure CLI 2.0](dns-operations-dnszones-cli.md).

Дополнительные сведения об управлении записями DNS в службе DNS Azure см. в статье [Управление записями и наборами записей DNS в Azure DNS с помощью Azure CLI 2.0](dns-operations-recordsets-cli.md).
