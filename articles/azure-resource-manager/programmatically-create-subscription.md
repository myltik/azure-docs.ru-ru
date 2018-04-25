---
title: Программное создание подписок Azure Enterprise | Документация Майкрософт
description: Узнайте, как программно создавать дополнительные подписки Azure Enterprise или Azure "Enterprise — разработка и тестирование".
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/18/2018
ms.author: jlian
ms.openlocfilehash: 5ec67c0d51d24a7bb93a3f11bf3b73a223e8b583
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Программное создание подписок Azure Enterprise (предварительная версия)

Как пользователь Azure, подписавший [Соглашение Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), вы можете программно создавать подписки EA (MS-AZR-0017P) и EA "Разработка и тестирование"(MS-AZR-0148P). Чтобы предоставить другому пользователю или субъекту-службе разрешение на создание подписок, оплачиваемых через вашу учетную запись, предоставьте ему доступ к своей учетной записи регистрации с помощью [управления доступом на основе ролей (RBAC)](../active-directory/role-based-access-control-configure.md). 

> [!IMPORTANT]
> Подписки Azure, создаваемые с помощью этого API, регулируются соглашением, в соответствии с которым вы получили службы Microsoft Azure от корпорации Майкрософт или уполномоченного торгового посредника. Дополнительные сведения см. на странице [Юридическая информация Службы Microsoft Azure](https://azure.microsoft.com/support/legal/).

В этой статье вы узнаете:

> [!div class="checklist"]
> * как создавать подписки программно с помощью Azure Resource Manager;
> * как использовать RBAC для предоставления разрешений на создание подписок, оплачиваемых через вашу учетную запись EA.

Кроме того, ознакомьтесь с [примером кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Попросите администратора регистрации EA добавить вас в качестве владельца учетной записи

Сначала попросите администратора регистрации [добавить вас в качестве владельца учетной записи на портале EA](https://ea.azure.com/helpdocs/addNewAccount) (требуется вход). Следуйте инструкциям из приглашения, полученного по электронной почте, чтобы вручную создать начальную подписку.

> [!IMPORTANT]
> Необходимо подтвердить владение учетной записью и вручную создать начальную подписку EA, прежде чем переходить к следующему шагу. Просто добавить учетную запись в регистрацию не достаточно.

## <a name="find-accounts-you-have-access-to"></a>Поиск учетных записей, к которым у вас есть доступ

После того, как вы будете добавлены в регистрацию Azure EA в качестве владельца учетной записи, платформа Azure использует связь между регистрацией и учетной записью, чтобы определить цель для выставления счетов за подписки. Чтобы создать подписки, сначала узнайте, к каким учетным записям регистрации у вас имеется доступ. Если вы являетесь владельцем учетной записи EA и пытаетесь использовать этот API, платформа Azure проверяет наличие следующих условий:

- ваша учетная запись добавлена в регистрацию EA;
- у вас одна или несколько подписок EA или EA "Разработка и тестирование", то есть вы вручную выполнили регистрацию по крайней мере один раз;
- вы выполнили вход в *домашний каталог* владельца учетной записи, в котором по умолчанию создаются подписки.

Если два вышеуказанных условия выполнены, возвращается ресурс `enrollmentAccount` и можно приступить к созданию подписки под этой учетной записью. Все подписки, созданные с учетной записью, оплачиваются в рамках регистрации EA, к которой относится эта учетная запись.

# <a name="resttabrest"></a>[REST](#tab/rest)

Запрос на вывод списка всех учетных записей регистрации.

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Платформа Azure выводит список всех учетных записей регистрации, к которым у вас есть доступ.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Используйте [команду Get-EnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-EnrollmentAccount
```

Платформа Azure выводит список идентификаторов объектов и адресов электронной почты учетных записей.

```azurepowershell
ObjectId                               | PrincipalName
<enrollmentAccountId>   | MobileOnboardingEng@contoso.com
<enrollmentAccountId>   | MobileBackendEng@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive 
az billing enrollment-account list
```
Платформа Azure выводит список идентификаторов объектов и адресов электронной почты учетных записей.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

---

Используйте свойство `principalName`, чтобы указать учетную запись для оплаты подписок. Укажите `id` в качестве значения `enrollmentAccount`, используемого для создания подписки на следующем шаге.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Создание подписок с помощью определенной учетной записи регистрации 

В следующем примере создается запрос на создание подписки *Dev Team Subscription* и предложения подписки *MS-AZR-0017P* (обычная подписка EA). Используется учетная запись регистрации `<enrollmentAccountId>`, которая является учетной записью регистрации для MobileOnboardingEng@contoso.com. Этот пример также при необходимости добавляет двух пользователей в качестве владельцев (RBAC) для подписки.

# <a name="resttabrest"></a>[REST](#tab/rest)

Используйте `id` из `enrollmentAccount` в пути запроса для создания подписки.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Имя элемента  | Обязательно | type   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Нет       | Строка | Отображаемое имя подписки. Если не указан, то ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offerType`   | Yes      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Нет        | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |

В ответе возвращается объект `subscriptionOperation` для отслеживания. После завершения создания подписки объект `subscriptionOperation` вернет объект `subscriptionLink` с идентификатором подписки.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы использовать эту предварительную версию модуля, сначала установите ее с помощью командлета `Install-Module AzureRM.Subscription -AllowPrerelease`. Чтобы убедиться в том, что `-AllowPrerelease` работает, установите последнюю версию PowerShellGet из статьи [Получение модуля PowerShellGet](/powershell/gallery/psget/get_psget_module).

Используйте командлет [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview), указав имя `enrollmentAccount` в качестве параметра `EnrollmentAccountObjectId`, чтобы создать подписку. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountId> -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Имя элемента  | Обязательно | type   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Нет       | Строка | Отображаемое имя подписки. Если не указан, то ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Yes      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `OwnerObjectId`      | Нет        | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `OwnerSignInName`    | Нет        | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.|
| `OwnerApplicationId` | Нет        | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `OwnerObjectId`.| 

Полный список параметров приведен в разделе о [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Чтобы использовать эту предварительную версию расширения, сначала установите ее с помощью команды `az extension add --name subscription`.

Используйте команду [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create), указав имя `enrollmentAccount` в качестве параметра `enrollment_account_name`, чтобы создать подписку.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-name "<enrollmentAccountId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Имя элемента  | Обязательно | type   | ОПИСАНИЕ                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Нет       | Строка | Отображаемое имя подписки. Если не указан, то ему присваивается имя предложения, например "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Yes      | Строка | Предложение подписки. Доступны два варианта предложения EA: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (для использования в рабочей среде) и [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (для разработки и тестирования, должен быть [включен с помощью портала EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owner-object-id`      | Нет        | Строка | Идентификатор объекта любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании.  |
| `owner-upn`    | Нет        | Строка | Адрес электронной почты любого пользователя, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.|
| `owner-spn` | Нет        | Строка | Идентификатор приложения любого субъекта-службы, которого вы хотите добавить в качестве владельца RBAC для подписки при ее создании. Можно использовать этот параметр вместо `owner-object-id`.| 

Полный список параметров приведен в разделе о команде [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Делегирование доступа к учетной записи регистрации с помощью RBAC

Чтобы предоставить другому пользователю или субъекту-службе возможность создания подписок в рамках определенной учетной записи, [назначьте ему роль владельца RBAC на уровне учетной записи регистрации](../active-directory/role-based-access-control-manage-access-rest.md). Следующий пример дает пользователю в клиенте с `principalId` со значением `<userObjectId>` (для MobileOnboardingEng@contoso.com) роль владельца учетной записи регистрации. 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
После успешного назначения роли владельца на уровне учетной записи регистрации платформа Azure возвращает сведения о назначении роли.

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Используйте командлет [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md), чтобы предоставить другому пользователю с ролью владельца доступ к вашей учетной записи регистрации.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Используйте команду [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md), чтобы предоставить другому пользователю с ролью владельца доступ к вашей учетной записи регистрации.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

----

Когда пользователь становится владельцем RBAC учетной записи регистрации, он получат возможность программно создавать подписки в рамках этой учетной записи. Для подписки, созданной делегированным пользователем, администратором службы по-прежнему остается исходный владелец учетной записи. Однако делегированный пользователь также является ее владельцем по умолчанию. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Аудит пользователей, создающих подписки, с помощью журналов действий

Для отслеживания подписок, создаваемых с помощью данного API, используйте [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs). В настоящее время для отслеживания создания подписки невозможно использовать PowerShell, интерфейс командной строки или портал Azure.

1. В качестве администратора клиента Azure AD [повысьте права доступа](../active-directory/role-based-access-control-tenant-admin-access.md), а затем назначьте роль читателя пользователю-аудитору для области `/providers/microsoft.insights/eventtypes/management`.
1. В качестве пользователя-аудитора вызовите [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs) для просмотра действий создания подписок. Пример:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Для удобного вызова API из командной строки попробуйте использовать [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ограничения API создания подписок Azure Enterprise

- С помощью этого API могут быть созданы только подписки Azure Enterprise.
- Действует ограничение в 50 подписок на учетную запись. После этого подписки могут создаваться только с помощью центра учетных записей.
- В учетной записи должна быть по крайней мере одна подписка EA или подписка EA "Разработка и тестирование". Это означает, что владелец учетной записи по крайней мере один раз выполнил регистрацию вручную.
- Пользователи, которые не являются владельцами учетной записи, но были добавлены в учетную запись регистрации с помощью RBAC, не могут создавать подписки, используя центр учетных записей.
- Невозможно выбрать клиент для создания подписки. Подписка всегда создается в домашнем клиенте владельца учетной записи. Чтобы переместить подписку в другой клиент, ознакомьтесь с [изменением подписки клиента](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Дополнительная информация

* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Дополнительные сведения о Azure Resource Manager и его интерфейсах API см. в статье [Обзор Azure Resource Manager](resource-group-overview.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](management-groups-overview.md).
* Полное руководство по лучшим методикам управления подписками для крупных организаций приведено в разделе [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md).
