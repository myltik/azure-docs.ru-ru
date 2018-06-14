---
title: Поставщики ресурсов Azure и типы ресурсов | Документация Майкрософт
description: Описывает поставщиков ресурсов, которые поддерживают диспетчер ресурсов, их схемы и доступные версии API, а также регионы, допускающие размещение ресурсов.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 811bb40816339dbe7097e429722625a3ae5c95c0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358206"
---
# <a name="resource-providers-and-types"></a>Поставщики и типы ресурсов

При развертывании ресурсов часто бывает необходимо получить сведения о типах и поставщиках ресурсов. В этой статье раскрываются следующие темы:

* просмотр всех поставщиков ресурсов в Azure;
* проверка состояния регистрации поставщика ресурсов;
* регистрация поставщика ресурсов;
* просмотр типов ресурсов для поставщика ресурсов;
* просмотр допустимых расположений для типа ресурса;
* просмотр допустимых версий API для типа ресурса.

Вы можете выполнить эти шаги с помощью портала, PowerShell или Azure CLI.

## <a name="powershell"></a>PowerShell

Чтобы просмотреть всех поставщиков ресурсов в Azure, а также состояние регистрации для подписки, используйте команду ниже:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Она возвращает результаты, подобные следующим:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Регистрация поставщика ресурсов настраивает подписку для работы с поставщиком ресурсов. Областью регистрации всегда является подписка. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее, некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на выполнение операции `/register/action` для поставщика ресурсов. Эта операция включается в роли участника и владельца.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Она возвращает результаты, подобные следующим:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Невозможно отменить регистрацию поставщика ресурсов, если в подписке еще есть типы ресурсов из этого поставщика ресурсов.

Чтобы просмотреть сведения для конкретного поставщика ресурсов, используйте команду ниже:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Она возвращает результаты, подобные следующим:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Чтобы просмотреть типы ресурсов для поставщика ресурсов, используйте команду ниже:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Возвращаемые данные:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API. 

Чтобы получить список доступных версий API для типа ресурса, используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Возвращаемые данные:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс. 

Чтобы получить список поддерживаемых расположений для типа ресурса, используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Возвращаемые данные:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure
Чтобы просмотреть всех поставщиков ресурсов в Azure, а также состояние регистрации для подписки, используйте команду ниже:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Она возвращает результаты, подобные следующим:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Регистрация поставщика ресурсов настраивает подписку для работы с поставщиком ресурсов. Областью регистрации всегда является подписка. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее, некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на выполнение операции `/register/action` для поставщика ресурсов. Эта операция включается в роли участника и владельца.

```azurecli
az provider register --namespace Microsoft.Batch
```

Оно возвращает сообщение о выполнении регистрации.

Невозможно отменить регистрацию поставщика ресурсов, если в подписке еще есть типы ресурсов из этого поставщика ресурсов.

Чтобы просмотреть сведения для конкретного поставщика ресурсов, используйте команду ниже:

```azurecli
az provider show --namespace Microsoft.Batch
```

Она возвращает результаты, подобные следующим:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Чтобы просмотреть типы ресурсов для поставщика ресурсов, используйте команду ниже:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Возвращаемые данные:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API. 

Чтобы получить список доступных версий API для типа ресурса, используйте следующую команду:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Возвращаемые данные:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс. 

Чтобы получить список поддерживаемых расположений для типа ресурса, используйте следующую команду:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Возвращаемые данные:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Microsoft Azure

Чтобы просмотреть всех поставщиков ресурсов в Azure, а также состояние регистрации для подписки, выберите **Подписки**.

![выбор подписок](./media/resource-manager-supported-services/select-subscriptions.png)

Выберите подписку, которую нужно просмотреть.

![указание подписки](./media/resource-manager-supported-services/subscription.png)

Выберите **Поставщики ресурсов**, а затем просмотрите список доступных поставщиков ресурсов.

![отображение поставщиков ресурсов](./media/resource-manager-supported-services/show-resource-providers.png)

Регистрация поставщика ресурсов настраивает подписку для работы с поставщиком ресурсов. Областью регистрации всегда является подписка. По умолчанию многие поставщики ресурсов регистрируются автоматически. Тем не менее, некоторые поставщики ресурсов может потребоваться зарегистрировать вручную. Чтобы зарегистрировать поставщик ресурсов, необходимо иметь разрешение на выполнение операции `/register/action` для поставщика ресурсов. Эта операция включается в роли участника и владельца. Чтобы зарегистрировать поставщика ресурсов, выберите **Регистрация**.

![регистрация поставщика ресурсов](./media/resource-manager-supported-services/register-provider.png)

Невозможно отменить регистрацию поставщика ресурсов, если в подписке еще есть типы ресурсов из этого поставщика ресурсов.

Чтобы просмотреть сведения о конкретном поставщике ресурсов, выберите **Все службы**.

![Пункт "Все службы"](./media/resource-manager-supported-services/more-services.png)

Найдите **Обозреватель ресурсов** и выберите его из списка доступных вариантов.

![выбор обозревателя ресурсов](./media/resource-manager-supported-services/select-resource-explorer.png)

Выберите **Поставщики**.

![выбор поставщиков](./media/resource-manager-supported-services/select-providers.png)

Выберите поставщика ресурсов и тип ресурса, который вы хотите просмотреть.

![Выбор типа ресурсов](./media/resource-manager-supported-services/select-resource-type.png)

Диспетчер ресурсов поддерживается во всех регионах, однако развертываемые вами ресурсы могут поддерживаться не во всех регионах. Кроме того, на вашу подписку могут налагаться ограничения, которые препятствуют использованию определенных регионов, поддерживающих ресурс. Обозреватель ресурсов отображает допустимые расположения для типа ресурса.

![Отображение расположений](./media/resource-manager-supported-services/show-locations.png)

Версия API соответствует версии операций API REST, выполняемых поставщиком ресурсов. При появлении в поставщике ресурсов новых возможностей выпускается новая версия REST API. Обозреватель ресурсов отображает допустимые версии API для типа ресурса.

![Отображение версий API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Дополнительная информация
* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Сведения о развертывании ресурсов см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).
* Чтобы просмотреть операции для поставщика ресурсов, ознакомьтесь с [Azure REST API](/rest/api/).

