---
title: Запрос к подпискам службы "Сетка событий Azure"
description: Процесс получения списка подписок для Сетки событий Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: 2b46cde4a352e647ee97669f116a6c1926879fa0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302421"
---
# <a name="query-event-grid-subscriptions"></a>Запрос к подпискам службы "Сетка событий Azure" 

В этой статье описано, как получить список подписок Сетки событий для подписки Azure. При создании запросов к существующим подпискам Сетки событий важно понимать различия между типами подписок. Параметры запросов будут зависеть от требуемого типа подписки.

## <a name="resource-groups-and-azure-subscriptions"></a>Группы ресурсов и подписки Azure

Подписки и группы ресурсов Azure не являются ресурсами Azure. Это означает, что подписки Сетки событий на группы ресурсов или подписки Azure будут иметь другие свойства, отличные от свойств для подписок Сетки событий на ресурсы Azure. Подписки Сетки событий на группы ресурсов или подписки Azure считаются глобальными.

Чтобы получить подписки Сетки событий для группы ресурсов или подписки Azure, не нужно указывать никаких параметров. Достаточно выбрать подписку Azure, к которой вы направляете запрос. Следующие примеры не возвращают подписки Сетки событий для настраиваемых разделов и (или) ресурсов Azure.

Для интерфейса командной строки Azure:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Set-AzureRmContext -Subscription "My Azure Subscription"
Get-AzureRmEventGridSubscription
```

Чтобы получить подписки Сетки событий для подписки Azure, укажите тип раздела **Microsoft.Resources.Subscriptions**.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Чтобы получить подписки Сетки событий для всех групп ресурсов в подписке Azure, укажите тип раздела **Microsoft.Resources.ResourceGroups**.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Чтобы получить подписки Сетки событий для определенной группы ресурсов, укажите имя этой группы ресурсов в качестве параметра.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Настраиваемые разделы и ресурсы Azure

Настраиваемые разделы Сетки событий являются ресурсами Azure. Это означает, что можно использовать одинаковые запросы, чтобы получить подписки Сетки событий сетки для пользовательских разделов и других ресурсов, например для учетной записи хранилища больших двоичных объектов. Чтобы получить подписки Сетки событий для настраиваемых разделов, необходимо указать параметры, однозначно определяющие ресурс или расположение ресурса. Вы не можете с помощью одного запроса получить подписки Сетки событий для всех ресурсов в подписке Azure.

Чтобы получить подписки Сетки событий для настраиваемых разделов и других ресурсов в определенном расположении, укажите имя этого расположения.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

Чтобы получить подписки на настраиваемые разделы в определенном расположении, укажите это расположение и тип раздела **Microsoft.EventGrid.Topics**.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Чтобы получить подписки на учетные записи хранения в определенном расположении, укажите это расположение и тип раздела **Microsoft.Storage.StorageAccounts**.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Чтобы получить подписки Сетки событий на пользовательский раздел, укажите имя этого раздела и имя соответствующей группы ресурсов.

Для интерфейса командной строки Azure:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Для PowerShell используйте команду:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Чтобы получить подписки Сетки событий для определенного ресурса, укажите идентификатор этого ресурса.

Для интерфейса командной строки Azure:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Для PowerShell используйте команду:

```azurepowershell-interactive
$resourceid = (Get-AzureRmResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzureRmEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
