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
ms.date: 11/01/2017
ms.author: tomfitz
ms.openlocfilehash: d18f5f9e75cd815b7f9622498f16624cf375d6bd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Использование тегов для организации ресурсов в Azure

[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы просмотреть существующие теги для *группы ресурсов*, используйте этот командлет:

```azurecli
az group show -n examplegroup --query tags
```

Этот скрипт вернет ответ в следующем формате:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Чтобы просмотреть существующие теги для *ресурса с указанным именем, типом и группой ресурсов*, используйте следующую команду:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

При циклическом переборе коллекции ресурсов может потребоваться отобразить ресурс с помощью идентификатора ресурса. Полный пример показан далее в этой статье. Чтобы просмотреть существующие теги для *ресурса с указанным идентификатором ресурса*, используйте следующую команду:

```azurecli
az resource show --id <resource-id> --query tags
```

Чтобы получить группы ресурсов с определенным тегом, используйте команду `az group list`:

```azurecli
az group list --tag Dept=IT
```

Чтобы получить все ресурсы с определенным тегом и значением, используйте команду `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов.

Чтобы добавить теги в *группу ресурсов без тегов*, используйте этот командлет:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Чтобы добавить теги в *ресурс без тегов*, используйте этот командлет:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить теги в ресурс, который уже содержит теги, извлеките существующие теги, переформатируйте это значение и еще раз добавьте существующие и новые теги: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе, *не сохраняя существующие теги ресурсов*, используйте следующий сценарий.

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе и *сохранить существующие теги ресурсов*, используйте следующий сценарий:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Шаблоны

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Microsoft Azure

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Интерфейс REST API

Портал Azure и PowerShell используют [интерфейс REST API диспетчера ресурсов Resource Manager](https://docs.microsoft.com/rest/api/resources/). Если вам нужно интегрировать теги в другую среду, их можно получить с помощью метода **GET** по идентификатору ресурса и обновить набор тегов с помощью вызова метода **PATCH**.

## <a name="tags-and-billing"></a>Теги и выставление счетов

С помощью тегов можно группировать данные о выставлении счетов. Например, если вы используете несколько виртуальных машин для различных организаций, то с помощью тегов можно сгруппировать сведения об использовании по месту возникновения затрат. Кроме того, теги можно использовать для классификации затрат по среде выполнения (например, сведения о выставленных счетах за виртуальные машины, запущенные в рабочей среде).

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](../billing/billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать на [портале учетных записей Azure](https://account.windowsazure.com/) или на [портале Azure Enterprise Portal](https://ea.azure.com). Дополнительные сведения о программном доступе к данным для выставления счетов см. в статье [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Подробнее об операциях REST API см. в [справочнике по REST API для выставления счетов Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

В скачанном CSV-файле со сведениями об использовании служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Tags**. Дополнительные сведения см. в статье [Расшифровка счета за использование Microsoft Azure](../billing/billing-understand-your-bill.md).

![См. сведения об использовании тегов при выставлении счетов.](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Дальнейшие действия

* В подписку можно добавить ограничения и соглашения, используя настраиваемые политики. Некоторые политики могут требовать, чтобы для всех ресурсов было задано значение определенного тега. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"](../azure-policy/azure-policy-introduction.md).
* Общие сведения об использовании Azure PowerShell для развертывания ресурсов см. в статье [Управление ресурсами с помощью Azure PowerShell и Resource Manager](powershell-azure-resource-manager.md).
* Общие сведения об использовании Azure CLI для развертывания ресурсов см. в статье [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](xplat-cli-azure-resource-manager.md).
* Общие сведения об использовании портала см. в статье [Управление ресурсами Azure через портал](resource-group-portal.md).  
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md).
