---
title: Создание управляемого приложения Azure с помощью Azure CLI | Документация Майкрософт
description: Показано, как создать управляемое приложение Azure, предназначенное для членов вашей организации.
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: 46ea192220ced18b25d60030527d1f76fb37962a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Создание и развертывание управляемого приложения Azure с помощью Azure CLI

Эта статья содержит общие сведения о роботе с управляемыми приложениями. Вы добавили определение управляемого приложения во внутренний каталог для пользователей в своей организации. Затем вы развернули управляемое приложение в своей подписке. Чтобы упростить вступление, мы уже создали файлы для управляемого приложения. Один файл определяет инфраструктуру решения, а второй — пользовательский интерфейс для развертывания управляемого приложения на портале. Эти файлы доступны на GitHub. Сведения о создании этих файлов см. в руководстве [Публикация управляемого приложения для внутреннего использования](publish-service-catalog-app.md).

По окончании работы у вас будет три группы ресурсов, содержащие разные части управляемого приложения.

| Группа ресурсов | Содержит | ОПИСАНИЕ |
| -------------- | -------- | ----------- |
| appDefinitionGroup | Определение управляемого приложения. | Эту группу ресурсов и определение управляемого приложения создает издатель. Любой пользователь с доступом к определению управляемого приложения может его развернуть. |
| applicationGroup | Экземпляр управляемого приложения. | Эту группу ресурсов и экземпляр управляемого приложения создает потребитель. Потребитель может обновить управляемое приложение с помощью этого экземпляра. |
| infrastructureGroup | Ресурсы для управляемого приложения. | Эта группа ресурсов создается автоматически при создании управляемого приложения. Издатель имеет доступ к этой группе ресурсов для управления приложением. Потребитель имеет ограниченный доступ к группе ресурсов. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Создание группы ресурсов для определения

Ваше определение управляемого приложения существует в группе ресурсов. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Создание определения управляемого приложения

При определении управляемого приложения необходимо выбрать пользователя, группу или приложение, которые управляют ресурсами от имени потребителя. Этому удостоверению предоставляются разрешения в управляемой группе ресурсов в соответствии с назначенной ролью. Обычно вы создаете группу Azure Active Directory для управления ресурсами. Однако для этой статьи используйте собственное удостоверение.

Чтобы получить идентификатор объекта своего удостоверения, укажите свое имя участника-пользователя в следующей команде:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Далее необходимо получить идентификатор определения встроенной роли RBAC, к которой вы хотите предоставить доступ пользователю. Следующая команда позволяет получить идентификатор определения роли "Владелец".

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Теперь создайте ресурс определения управляемого приложения. Управляемое приложение содержит только учетную запись хранения.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

После выполнения команды в вашей группе ресурсов будет определение управляемого приложения. 

Далее приведены некоторые параметры, которые использовались в предыдущем примере:

* **resource-group**. Имя группы ресурсов, в которой создается определение управляемого приложения.
* **lock-level**. Тип блокировки управляемой группы ресурсов. Этот параметр предотвращает выполнение нежелательных операций в группе ресурсов. Сейчас поддерживается только тип блокировки ReadOnly. Если указать этот тип, пользователь будет иметь только доступ на чтение к ресурсам в управляемой группе ресурсов. Удостоверения издателей, которым предоставлен доступ к группе управляемых ресурсов, исключаются из блокировки.
* **authorizations**. Содержит идентификатор субъекта и идентификатор определения роли, используемые для предоставления разрешения для управляемой группы ресурсов. Это свойство указывается в формате `<principalId>:<roleDefinitionId>`. Для него можно указать несколько значений. Если нужно указать несколько значений, то их необходимо указать в следующем формате: `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Разные значения разделяются пробелами.
* **package-file-uri.** Расположение пакета ZIP, содержащего необходимые файлы. Пакет содержит как минимум файлы **mainTemplate.json** и **createUiDefinition.json**. **mainTemplate.json.** Определяет ресурсы Azure, подготовленные как часть управляемого приложения. Этот шаблон ничем не отличается от обычного шаблона Resource Manager. **createUiDefinition.json.** Создает пользовательский интерфейс для пользователей, создающих управляемое приложение на портале Azure.

## <a name="create-resource-group-for-managed-application"></a>Создание группы ресурсов для управляемого приложения

Теперь вы готовы к развертыванию управляемого приложения. 

Для хранения развернутого управляемого приложения необходимо иметь группу ресурсов. В качестве расположения используйте **westcentralus**.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Развертывание управляемого приложения

Чтобы развернуть приложение, выполните следующие команды:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Далее приведены некоторые параметры, которые использовались в предыдущем примере:

* **managedapp-definition-id.** Идентификатор определения, созданного ранее в этой статье.
* **managed-rg-id.** Идентификатор группы ресурсов для ресурсов, связанных с управляемым приложением. Эту группу ресурсов можно создать, выполнив команду. Она **не должна существовать до выполнения команды**. Этой группой ресурсов управляет издатель. 
* **resource-group** — это группа ресурсов, в которой создается ресурс управляемого приложения.
* **parameters.** Параметры, необходимые для ресурсов, связанных с управляемым приложением.

После успешного завершения развертывания в applicationGroup будет создано управляемое приложение. Ресурс storageAccount будет создан в infrastructureGroup.

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в разделе [Обзор управляемых приложений Azure](overview.md).
* Примеры файлов см. в репозитории с [управляемыми приложениями](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Чтобы узнать, как создать файл определения пользовательского интерфейса для управляемого приложения, изучите статью [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
