---
title: "Использование управляемого приложения Azure | Документация Майкрософт"
description: "В этой статье описывается, как создавать управляемое приложение Azure с помощью опубликованных файлов."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.contentlocale: ru-ru
ms.lasthandoff: 08/25/2017

---
# <a name="consume-an-internal-managed-application"></a>Использование внутреннего управляемого приложения

Можно использовать [управляемые приложения](managed-application-overview.md) Azure, предназначенные для членов вашей организации. Например, можно выбрать управляемые приложения отдела ИТ, которые обеспечивают соответствие стандартам организации. Эти управляемые приложения доступны через каталог услуг, а не Azure Marketplace.

Прежде чем продолжить чтение этой статьи, необходимо добавить в подписку управляемое приложение из каталога услуг (если в подписке нет такого приложения). Если кто-либо в вашей организации еще не создал управляемое приложение, ознакомьтесь с разделом [Создание и публикация управляемого приложения Azure](managed-application-publishing.md).

В настоящее время использовать управляемое приложение можно с помощью Azure CLI или портала Azure.

## <a name="create-the-managed-application-by-using-the-portal"></a>Создание управляемого приложения с помощью портала

Чтобы развернуть управляемое приложение с помощью портала, выполните следующие действия.

1. Перейдите на портал Azure. Найдите **управляемое приложение из каталога услуг**.

   ![Управляемое приложение каталога услуг](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. Выберите управляемое приложение, которое необходимо создать, из списка доступных решений. Нажмите кнопку **Создать**.

   ![Выбор управляемого приложения](./media/managed-application-consumption/select-offer.png)

1. Укажите значения параметров, необходимых для подготовки ресурсов. Выберите расположение **Западно-центральная часть США**. Нажмите кнопку **ОК**.

   ![Параметры управляемого приложения](./media/managed-application-consumption/input-parameters.png)

1. Шаблон проверяет введенные значения. Если проверка прошла успешно, нажмите кнопку **ОК**, чтобы запустить развертывание.

   ![Проверка управляемого приложения](./media/managed-application-consumption/validation.png)

После завершения развертывания соответствующие ресурсы, определенные в шаблоне, подготавливаются в указанной управляемой группе ресурсов.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Создание управляемого приложения с помощью Azure CLI

Создать управляемое приложение с использованием Azure CLI можно двумя способами:

* с помощью команды создания управляемых приложений;
* с помощью команды развертывания шаблона;

### <a name="use-the-template-deployment-command"></a>Использование команды развертывания шаблона

Разверните файл applianceMainTemplate.json, который создал поставщик.

Создайте две группы ресурсов: одну для создания ресурсов управляемого приложения (Microsoft.Solutions/appliances), а вторую — для хранения всех ресурсов, определенных в файле mainTemplate.json. Эта группа ресурсов управляется независимым поставщиком программного обеспечения.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Используйте `westcentralus` в качестве расположения группы ресурсов.
>

Затем используйте следующую команду, чтобы развернуть файл applianceMainTemplate.json в mainResourceGroup.

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

После выполнения предыдущего шаблона появится запрос на ввод значений параметров, определенных в шаблоне. Помимо параметров, необходимых для подготовки ресурсов в шаблоне, понадобятся два значения ключевых параметров:

- **managedResourceGroupId** — это идентификатор группы ресурсов, содержащей ресурсы, определенные в файле applianceMainTemplate.json. Он имеет формат `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. В предыдущем примере это идентификатор `managedResourceGroup`.
- **applianceDefinitionId** — это идентификатор определения ресурсов управляемого приложения. Данное значение предоставляет независимый поставщик программного обеспечения.

> [!NOTE]
> Издатель должен предоставить доступ к группе ресурсов, содержащей определение управляемого приложения. Ресурс определения создается в подписке издателя. Поэтому пользователю, группе пользователей или приложению в клиенте пользователя требуется доступ на чтение к этому ресурсу.

После успешного завершения развертывания в mainResourceGroup будет создано управляемое приложение. Ресурс storageAccount будет создан в managedResourceGroup.

### <a name="use-the-create-command"></a>Использование команды create

Вы можете использовать команду `az managedapp create` для создания управляемых приложений из определения управляемого приложения.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id** — это идентификатор ресурса определения управляемого приложения, созданного на предыдущем шаге. Чтобы получить этот идентификатор, выполните следующую команду.

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  Эта команда возвращает определение управляемого приложения. Требуется значение свойства идентификатора.

* **managed-rg-id** — это имя группы ресурсов, содержащей ресурсы, определенные в файле applianceMainTemplate.json. Это управляемая группа ресурсов. Ею управляет издатель. Если эта группа не существует, она создается автоматически.
* **resource-group** — это группа ресурсов, в которой создается ресурс управляемого приложения. Ресурс Microsoft.Solutions/appliance размещается в этой группе ресурсов.
* **parameters** — параметры, необходимые для ресурсов, определенных в файле applianceMainTemplate.json.

## <a name="known-issues"></a>Известные проблемы

В этом предварительном выпуске могут возникнуть следующие проблемы.

* Во время создания управляемого приложения появляется сообщение "500 — внутренняя ошибка сервера". Если эта проблема возникла, скорее всего, она лишь временная. Повторите операцию.
* Для управляемой группы ресурсов требуется новая группа ресурсов. В случае использования существующей группы ресурсов произойдет сбой развертывания.
* Группу ресурсов для ресурса Microsoft.Solutions/appliances нужно создать в расположении **westcentralus**.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в разделе [Обзор управляемых приложений Azure](managed-application-overview.md).
* Сведения о публикации управляемых приложений в каталоге услуг см. в разделе [Создание и публикация управляемого приложения Azure](managed-application-publishing.md).
* Дополнительные сведения о публикации управляемых приложений в Azure Marketplace см. в статье [Управляемые приложения Azure в Marketplace](managed-application-author-marketplace.md).
* Сведения об использовании управляемого приложения из Marketplace см. в статье [Использование управляемых приложений Azure в Marketplace](managed-application-consume-marketplace.md).

