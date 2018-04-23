---
title: Как использовать управление доступом на основе ролей в службе управления API Azure | Документация Майкрософт
description: Узнайте, как использовать встроенные роли и создавать пользовательские роли в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: bf01cf4fe5d7c7219fa7c53e54db60cd4bafb928
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Как использовать управление доступом на основе ролей в службе управления API Azure
Служба управления API Azure использует управление доступом на основе ролей (RBAC) для детализированного контроля доступа к службам и сущностям управления API (например, к интерфейсам API и политикам). В этой статье содержатся общие сведения о встроенных и пользовательских ролях в службе управления API. Дополнительные сведения об управлении доступом на портале Azure, см. в руководстве по [началу работы с управлением доступом на портале Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Встроенные роли
Сейчас служба управления API предоставляет три встроенные роли, к которым скоро будут добавлены еще две. Эти роли можно назначать в разных областях, включая подписку, группу ресурсов и отдельный экземпляр службы управления API. Например, если назначить пользователю роль Azure API Management Service Reader на уровне группы ресурсов, у такого пользователя будет доступ на чтение ко всем экземплярам службы управления API в группе ресурсов. 

В таблице ниже содержатся краткие описания встроенных ролей. Эти роли можно назначать с помощью портала Azure или других инструментов, включая Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) и [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Дополнительные сведения о том, как назначать встроенные роли, см. в разделе [Начало работы с управлением доступом на портале Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Роль          | Доступ на чтение<sup>[1]</sup> | Доступ на запись<sup>[2]</sup> | Создание, удаление и масштабирование служб, настройка VPN и личных доменов | Доступ к устаревшему порталу издателя | ОПИСАНИЕ
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Azure API Management Service Contributor | ✓ | ✓ | ✓ | ✓ | Суперпользователь. Имеет полный доступ CRUD к службам и сущностям управления API (например, к интерфейсам API и политикам). Имеет доступ к устаревшему порталу издателя. |
| Azure API Management Service Reader | ✓ | | || Имеет доступ на чтение к службам и сущностям управления API. |
| Azure API Management Service Operator | ✓ | | ✓ | | Может управлять службами управления API, но не может управлять сущностями.|
| Azure API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Может управлять сущностями управления API, но не может управлять службами.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Может управлять порталом разработчика. Имеет доступ на чтение к службам и сущностям.|

<sup>[1] Полный доступ к службам и сущностям управления API (например, к интерфейсам API и политикам).</sup>

<sup>[2] Доступ на запись к службам и сущностям управления API, за исключением следующих операций: создание, удаление и масштабирование экземпляров; настройка VPN; настройка пользовательского домена.</sup>

<sup>\* Роль Service Editor будет доступна, когда мы полностью перенесем пользовательский интерфейс администратора с существующего портала издателя на портал Azure. Роль Content Manager будет доступна, когда будет выполнен рефакторинг портала издателя для того, чтобы он содержал только функции, относящиеся к управлению порталом разработчика.</sup>  

## <a name="custom-roles"></a>Пользовательские роли
Если ни одна из встроенных ролей не удовлетворяет вашим потребностям, можно создать пользовательские роли, которые обеспечат более детализированное управление доступом к сущностям управления API. Например, можно создать пользовательскую роль, которая имеет доступ на чтение к службе управления API и доступ на запись к одному конкретному API. Дополнительные сведения о пользовательских ролях см. в описании [пользовательских ролей в Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

При создании пользовательской роли проще всего начать с одной из встроенных ролей. Измените атрибуты и добавьте необходимые действия **Actions**, **notActions** или области **AssignableScopes**, а затем сохраните изменения как новую роль. В следующем примере на основе роли Azure API Managment Service Reader создается пользовательская роль Calculator API Editor. Пользовательскую роль можно назначить конкретному API. Следовательно, эта роль получит доступ только к этому API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

В статье [Операции поставщиков ресурсов Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) содержится список разрешений, которые могут быть предоставлены на уровне управления API.

## <a name="video"></a>Видео


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать больше об управлении доступом на основе ролей в Azure, см. следующие статьи:
  * [Начало работы с управлением доступом на портале Azure](../role-based-access-control/overview.md)
  * [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md)
  * [Пользовательские роли в Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Операции поставщиков ресурсов Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)

