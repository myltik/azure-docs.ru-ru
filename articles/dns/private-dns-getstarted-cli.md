---
title: Начало работы с Частными зонами Azure DNS с помощью Azure CLI 2.0 | Документация Майкрософт
description: Узнайте, как создать Частную зону и запись DNS в службе Azure DNS. В этом пошаговом руководстве описывается создание Частной зоны и записи DNS, а также управление ими с помощью Azure CLI 2.0.
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
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191455"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Начало работы с Частными зонами Azure DNS с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

В этой статье описано, как создать первую Частную зону DNS и первую запись DNS, используя кроссплатформенный интерфейс командной строки Azure CLI 2.0, доступный для Windows, Mac и Linux. Эти действия также можно выполнить с помощью Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Чтобы опубликовать частную зону DNS в виртуальной сети, укажите список виртуальных сетей, которые могут разрешать записи в зоне.  Мы называем их виртуальными сетями разрешения.  Кроме того, можно указать виртуальные сети, для которых служба Azure DNS будет поддерживать записи имени узла всякий раз, когда виртуальная машина создается, меняет IP-адрес или удаляется.  Мы называем это виртуальной сетью регистрации.

При выполнении этих инструкций предполагается, что вы уже установили интерфейс Azure CLI 2.0 и вошли в него, а также что вы установили необходимое расширение CLI, которое поддерживает Частные зоны. Чтобы получить справку, см. статью [Как управлять зонами DNS в службе DNS Azure с помощью Azure CLI 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Установка и использование функции "Частные зоны Azure DNS" (предварительная версия)
Функция "Частные зоны Azure DNS" выпущена в общедоступной предварительной версии через расширение Azure CLI. Установите расширение dns для Azure CLI: 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создается группа ресурсов, которая будет включать эту зону DNS. Ниже показана команда для создания группы ресурсов.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Создание Частной зоны DNS

Создайте Частную зону DNS с помощью команды `az network dns zone create`. Чтобы просмотреть справку для этой команды, введите `az network dns zone create --help`.

В следующем примере в группе ресурсов *MyResourceGroup* создается Частная зона DNS *contoso.local*, которая будет доступна для виртуальной сети *MyAzureVnet* (связана с ней) с помощью параметра resolution-vnets. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Примечание. В приведенном выше примере виртуальная сеть MyAzureVnet принадлежит к той же группе ресурсов и подписке, что и частная зона. Если требуется связать виртуальную сеть, которая принадлежит к другой группе ресурсов или подписке, для параметра resolution-vnets необходимо указать полный идентификатор Azure Resource Manager, а не только имя виртуальной сети. 

Если необходимо, чтобы платформа Azure автоматически создавала записи имени узла в зоне, используйте параметр *registration-vnets* вместо *resolution-vnets*.  Зарегистрированные виртуальные сети автоматически могут разрешать записи.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `az network dns record-set [record type] add-record`. Чтобы получить справку, например по записям типа A, см. `azure network dns record-set A add-record --help`.

В следующем примере создается запись с относительным именем ip1 в зоне DNS contoso.local в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — ip1.contoso.local. Тип записи — A, а ее IP-адрес — 10.0.0.1.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Сведения о других типах записей, наборах записей с несколькими записями, других значениях срока жизни, а также об изменении существующих записей см. в статье [Управление записями и наборами записей DNS в Azure DNS с помощью Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Получение Частной зоны DNS

Чтобы получить Частную зону DNS, используйте `az network dns zone show`. Чтобы получить справку, см. `az network dns zone show --help`.

В следующем примере возвращается зона DNS *contoso.local* и связанные с ней данные из группы ресурсов *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

Ниже приведен пример ответа.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

Обратите внимание, что записи DNS не возвращаются командой `az network dns zone show`. Для вывода списка записей DNS используйте `az network dns record-set list`.


## <a name="list-dns-zones"></a>Перечисление зон DNS

Чтобы перечислить зоны DNS, используйте `az network dns zone list`. Чтобы получить справку, см. `az network dns zone list --help`.

Если указать группу ресурсов, то будут перечислены только зоны в этой группе ресурсов.

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Если не указать группу ресурсов, то будут перечислены все зоны в подписке.

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Обновление зоны DNS

Изменить ресурс зоны DNS можно с помощью командлета `az network dns zone update`. Чтобы получить справку, см. `az network dns zone update --help`.

Это команда не обновляет ни один набор записей DNS в пределах зоны (прочитайте статью [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets-cli.md)). Эта команда используется только для обновления свойств самого ресурса зоны. Вы можете обновить виртуальные сети регистрации или разрешения, связанные с частной зоной. 

В следующем примере показано, как обновить виртуальную сеть разрешения, связанную с Частной зоной DNS. Существующая виртуальная сеть разрешения заменяется новой виртуальной сетью.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Удаление зоны DNS

Зоны DNS можно удалить с помощью команды `az network dns zone delete`. Чтобы получить справку, см. `az network dns zone delete --help`.

> [!NOTE]
> При удалении зоны DNS также удаляются все записи DNS в этой зоне. Эту операцию нельзя отменить. Если зона DNS используется, то после ее удаления произойдет сбой служб, которые ее используют.
>
>Сведения о защите от случайного зоны удаления см. в разделе [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Как защитить зоны и записи DNS).

Эта команда запрашивает подтверждение. Необязательный параметр `--yes` позволяет отключить этот запрос.

В следующем примере показано, как удалить зону *contoso.local* из группы ресурсов *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Удаление всех ресурсов
 
Чтобы удалить все ресурсы, созданные при работе с этой статьей, сделайте следующее:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о службе DNS Azure см. в статье [Обзор Azure DNS](dns-overview.md).

Дополнительные сведения об управлении зонами DNS в службе DNS Azure см. в статье [Как управлять зонами DNS в службе DNS Azure с помощью Azure CLI 2.0](dns-operations-dnszones-cli.md).

Дополнительные сведения об управлении записями DNS в службе DNS Azure см. в статье [Управление записями и наборами записей DNS в Azure DNS с помощью Azure CLI 2.0](dns-operations-recordsets-cli.md).
