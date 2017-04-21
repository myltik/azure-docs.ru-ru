---
title: "Добавление тегов к ресурсам Azure для их логической организации | Документация Майкрософт"
description: "Здесь описано, как применить теги, чтобы организовать ресурсы Azure для выставления счетов и управления."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 2f56314769d90a1f0f9ebb5ece9c8e54b23b8936
ms.lasthandoff: 02/21/2017


---
# <a name="use-tags-to-organize-your-azure-resources"></a>Использование тегов для организации ресурсов в Azure
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> Теги можно применять только к ресурсам, которые поддерживают операции диспетчера ресурсов. Если вы создали виртуальную машину, виртуальную сеть или хранилище при помощи модели классического развертывания (например, через классический портал), то к таким ресурсам нельзя применить теги. Для поддержки тегов эти ресурсы необходимо развернуть повторно с помощью Resource Manager. Все остальные ресурсы поддерживают теги.
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>Обеспечение согласованности тегов с помощью политик

Политики ресурсов позволяют создавать стандартные правила для организации. Можно создать политики, обеспечивающие добавление к ресурсам тегов с соответствующими значениями. Дополнительные сведения см. в разделе [Применение политик ресурсов для тегов](resource-manager-policy-tags.md).

## <a name="templates"></a>Шаблоны

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Microsoft Azure
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli-20"></a>Azure CLI 2.0

С помощью Azure CLI 2.0 можно добавить теги для ресурсов и группы ресурсов и запрашивать ресурсы по значениям тегов.

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Если существующие теги нужно сохранить, необходимо использовать другой подход. Ниже приведены командлеты для разных ситуаций.

* Добавление тегов к группе ресурсов, у которой нет тегов:

  ```azurecli
  az group update -n TagTestGroup --set tags.Environment=Test tags.Dept=IT
  ```

* Добавление тегов к ресурсу, у которого нет тегов:

  ```azurecli
  az resource tag --tags Dept=IT Environment=Test -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
  ``` 

Для добавления тега к ресурсу, который уже содержит теги, сначала получите сведения о существующих тегах. 

```azurecli
az resource show --query tags --output list -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
```

Она вернет ответ в следующем формате:

```
Dept        : Finance
Environment : Test
```

Повторно примените существующие теги к ресурсу и добавьте новые теги.

```azurecli
az resource tag --tags Dept=Finance Environment=Test CostCenter=IT -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
``` 

Чтобы получить группы ресурсов с определенным тегом, используйте командлет `az group list`.

```azurecli
az group list --tag Dept=IT
```

Чтобы получить все ресурсы с определенным тегом и значением, используйте командлет `az resource list`.

```azurecli
az resource list --tag Dept=Finance
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>Интерфейс REST API
Как на портале, так и в PowerShell «за кулисами» используется [интерфейс REST API диспетчера ресурсов](https://docs.microsoft.com/rest/api/resources/) . Если требуется интегрировать теги в другую среду, их можно получить с помощью метода GET по идентификатору ресурса и обновить набор тегов с помощью вызова метода PATCH.

## <a name="tags-and-billing"></a>Теги и выставление счетов
Теги позволяют сгруппировать данные о выставлении счетов. Например, если вы используете несколько виртуальных машин для различных организаций, то с помощью тегов можно сгруппировать сведения об использовании по месту возникновения затрат. Теги также можно использовать для упорядочивания затрат по среде выполнения, например сведения об использовании выставления счетов для виртуальных машин, запущенных в рабочей среде.


Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](../billing/billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать на [портале учетных записей Azure](https://account.windowsazure.com/) или на [портале EA](https://ea.azure.com). Дополнительные сведения о программном доступе к данным для выставления счетов см. в статье [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Подробнее об операциях REST API см. в [справочнике по REST API для выставления счетов Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).


В скачанном CSV-файле со сведениями об использовании служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Tags**. Дополнительные сведения см. в статье [Расшифровка счета за использование Microsoft Azure](../billing/billing-understand-your-bill.md).

![См. сведения об использовании тегов при выставлении счетов.](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Дальнейшие действия
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. При использовании определяемой политики может потребоваться, чтобы для всех ресурсов было задано значение тега. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).
* Общие сведения об использовании Azure PowerShell для развертывания ресурсов см. в статье [Использование Azure PowerShell с Azure Resource Manager](powershell-azure-resource-manager.md).
* Основные сведения об использовании Azure CLI для развертывания ресурсов см. в статье [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](xplat-cli-azure-resource-manager.md).
* Общие сведения об использовании портала см. в статье [Управление ресурсами Azure через портал](resource-group-portal.md).  
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).


