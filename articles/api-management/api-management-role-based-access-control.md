---
title: "Как использовать управление доступом на основе ролей в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как использовать встроенные роли и создавать пользовательские роли в службе управления API Azure."
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b048a1db135d217b319541b92cf3c30b345d1a66
ms.openlocfilehash: cedab98452ad336748d805f0efbf50633badf8ab


---

# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Как использовать управление доступом на основе ролей в службе управления API Azure
Служба управления API Azure использует управление доступом на основе ролей (RBAC) для детализированного контроля доступа к службам и сущностям управления API (например, к интерфейсам API, политикам). В этой статье содержатся общие сведения о встроенных и пользовательских ролях в службе управления API. Если вы хотите больше узнать об управлении доступом на портале Azure, ознакомьтесь с разделом [Начало работы с управлением доступом на портале Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Встроенные роли
В настоящее время служба управления API предоставляет три встроенные роли, к которым скоро добавятся еще две. Эти роли можно назначать в различных областях, включая подписку, группу ресурсов и отдельный экземпляр управления API. Например, если пользователю назначается роль Azure API Management Service Reader на уровне группы ресурсов, то он будет иметь доступ на чтение ко всем экземплярам управления API в группе ресурсов. 

В таблице ниже содержатся краткие описания встроенных ролей. Эти роли можно назначать с помощью портала Azure или других инструментов, включая Azure [PowerShell](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-powershell), [интерфейс командной строки](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-azure-cli) Azure и [REST API](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-rest). Дополнительные сведения о том, как назначать встроенные роли, см. в разделе [Начало работы с управлением доступом на портале Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/).

| Роль          | Доступ на чтение<sup>[1]</sup> | Доступ на запись<sup>[2]</sup> | Создание, удаление и масштабирование служб, настройка VPN и личных доменов | Доступ к устаревшему порталу издателя | Описание
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Azure API Management Service Contributor | ✓ | ✓  | ✓  | ✓ | Суперпользователь. Имеет полный доступ CRUD к службам и сущностям управления API (например, к интерфейсам API, политикам). Имеет доступ к устаревшему порталу издателя. |
| Azure API Management Service Reader | ✓ | | || Имеет доступ на чтение к службам и сущностям управления API. |
| Azure API Management Service Operator | ✓ | | ✓ | | Может управлять службами управления API, но не может управлять сущностями.|
| Azure API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Может управлять сущностями управления API, но не может управлять службами.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Может управлять порталом разработчика. Имеет доступ на чтение к службам и сущностям.|

<sup>[1] Доступ на чтение к службам и сущностям управления API (например, к интерфейсам API, политикам).</sup>

<sup>[2] Доступ на запись к службам и сущностям управления API, за исключением следующих операций: 1) создание, удаление и масштабирование экземпляров; 2) настройка VPN; 3) настройка имени личного домена.</sup>

<sup>\*Роль Service Editor будет доступны после того, как мы полностью перенесем пользовательский интерфейс администратора с существующего портала издателя на портал Azure. Роль Content Manager будет доступна после того, как будет выполнен рефакторинг портала издателя для того, чтобы он содержал только функции, относящиеся к управлению порталом разработчика.</sup>  


## <a name="custom-roles"></a>Пользовательские роли
Если ни одна из встроенных ролей не удовлетворяет вашим потребностям, можно создать пользовательские роли, которые обеспечат более детализированное управление доступом к сущностям управления API. Например, можно создать пользовательскую роль, которая имеет доступ на чтение к службе управления API и доступ на запись к одному конкретному API. Чтобы узнать больше о пользовательских ролях, ознакомьтесь с разделом [Пользовательские роли в Azure RBAC](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles). 

При создании пользовательской роли проще всего начать с одной из встроенных ролей. Измените атрибуты и добавьте необходимые действия Actions, notActions или области AssignableScopes, а затем сохраните изменения как новую роль. В следующем примере на основе роли Azure API Managment Service Reader создается пользовательская роль Calculator API Editor. Пользовательская роль может быть назначена определенному API, тем самым предоставляя доступ только к этому API. 

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

## <a name="watch-a-video-overview"></a>Просмотр видеообзора

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
> 
> 

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше об управлении доступом на основе ролей в Azure.
  * [Начало работы с управлением доступом на портале Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Пользовательские роли в Azure RBAC](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)



<!--HONumber=Feb17_HO1-->


