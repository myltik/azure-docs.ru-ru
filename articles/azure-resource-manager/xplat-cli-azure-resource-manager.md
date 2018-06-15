---
title: Управление ресурсами с помощью интерфейса командной строки Azure | Документация Майкрософт
description: Используйте интерфейс командной строки Azure для управления ресурсами и группами ресурсов в Azure.
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: 4111d1d990f3e9efb78fae0476b027d5bae013af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603252"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure

В этой статье вы узнаете, как управлять решениями с помощью Azure CLI и Azure Resource Manager. Если вы не знакомы с Resource Manager, то см. статью [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md). Эта статья посвящена задачам управления. Вы сможете выполнять следующие задачи:

1. Создание группы ресурсов
2. Добавление ресурса в группу ресурсов
3. Добавление тега к ресурсу
4. Запрос ресурсов на основе имен или значений тегов
5. Применение и удаление блокировки ресурса
6. Удаление группы ресурсов

В этой статье не показан процесс развертывания в подписке шаблона Resource Manager. Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Сведения об установке и локальном использовании Azure CLI см. в статье [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Установка Azure CLI 2.0).

## <a name="set-subscription"></a>Выбор подписки

При наличии нескольких подписок можно переключаться с одной подписки на другую. Для начала давайте просмотрим все подписки для учетной записи.

```azurecli-interactive
az account list
```

Отобразится список включенных и отключенных подписок.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Обратите внимание, что одна подписка отмечена как подписка по умолчанию. Эта подписка является вашим текущим контекстом для операций. Чтобы переключиться на другую подписку, укажите имя подписки с помощью команды **az account set**.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Чтобы отобразить текущий контекст подписки, используйте команду **az account show** без параметра:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем развертывать ресурсы в подписке, необходимо создать группу ресурсов, которая будет их содержать.

Чтобы создать группу ресурсов, используйте команду **az group create**. В команде используются параметр **name** для указания имени группы ресурсов и параметр **location** для указания ее расположения.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Выходные данные имеют следующий формат:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Чтобы получить сведения о группе ресурсов, используйте следующую команду:

```azurecli-interactive
az group show --name TestRG1
```

Для получения всех групп ресурсов в подписке используйте следующую команду:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Добавление ресурсов в группу ресурсов

Чтобы добавить ресурс в группу ресурсов, можно использовать команду **az resource create** или команду для конкретного типа создаваемого ресурса (например **az storage account create**). Возможно, проще использовать команду для конкретного типа ресурса, так как она включает в себя параметры для свойств, которые необходимы для нового ресурса. Чтобы использовать команду **az resource create**, необходимо знать все свойства и ввести их без запроса.

Однако добавление ресурса с помощью сценария может привести к путанице в будущем, потому что новый ресурс не существует в шаблоне Resource Manager. Шаблоны обеспечивают надежность развертывания и позволяют развернуть решение повторно.

Следующая команда создает учетную запись хранения. Вместо имени, использованного в примере, укажите уникальное имя для учетной записи хранения. Это имя должно содержать от 3 до 24 знаков и состоять только из цифр и букв нижнего регистра. При использовании имени из примера возникнет ошибка, так как это имя уже используется.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Чтобы получить сведения об этом ресурсе, используйте следующую команду:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Добавление тега

Теги позволяют организовать ресурсы в соответствии с различными свойствами. Например, в разных группах ресурсов могут находиться ресурсы, относящиеся к одному отделу. Чтобы пометить ресурсы как относящиеся к одной категории, к ним можно применить тег отдела и значение. Также можно пометить, в какой среде используется ресурс — рабочей или тестовой. В этом примере теги применяются только к одному ресурсу, но в вашей среде, скорее всего, имеет смысл применить теги ко всем ресурсам.

Следующая команда применяет к учетной записи хранения два тега:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Теги обновляются как один объект. Для добавления тега к ресурсу, который уже содержит теги, сначала получите сведения о существующих тегах. Добавьте новый тег в объект, содержащий существующие теги, а затем повторно примените к ресурсу все теги.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Поиск ресурсов

Используйте команду **az resource list**, чтобы получить сведения о ресурсах для разных условий поиска.

* Чтобы найти ресурс по имени, укажите параметр **name**:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Чтобы получить сведения о всех ресурсах в группе ресурсов, укажите параметр **resource-group**:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Для получения сведений о всех ресурсах с именем и значением тега укажите параметр **tag**:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Для всех ресурсов с определенным типом ресурса укажите параметр **resource-type**:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Получение идентификатора ресурса

Многие команды принимают в качестве параметра идентификатор ресурса. Чтобы получить идентификатор ресурса и хранилища в переменной, используйте следующую команду:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Блокировка ресурса

Чтобы гарантировать, что критически важный ресурс не будет случайно удален или изменен, примените к нему блокировку ресурса. Можно указать параметр **CanNotDelete** или **ReadOnly**.

Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только владельцу и администратору доступа пользователей.

Чтобы применить блокировку, используйте следующую команду:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Заблокированный ресурс в предыдущем примере невозможно удалить, пока не будет снята блокировка. Для снятия блокировки используйте следующий командлет:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Дополнительные сведения об установке блокировок см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Удаление ресурсов или группы ресурсов
Ресурс или группу ресурсов можно удалить. При удалении группы ресурсов также удаляются все ресурсы, входящие в эту группу.

* Чтобы удалить ресурс из группы ресурсов, используйте команду удаления для удаляемого типа ресурса. Команда удаляет ресурс, но не удаляет группу ресурсов.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Чтобы удалить группу ресурсов и все ее ресурсы, используйте команду **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

В обоих командах требуется подтвердить, что вы хотите удалить ресурс или группу ресурсов.

## <a name="next-steps"></a>Дополнительная информация
* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Сведения о развертывании шаблонов см. в статье [Развертывание приложения с использованием шаблона Azure Resource Manager](resource-group-template-deploy-cli.md).
* Существующие ресурсы можно переместить в новую группу ресурсов. Примеры см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).