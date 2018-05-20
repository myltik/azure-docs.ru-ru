---
title: Создание групп управления для упорядочения ресурсов Azure | Документация Майкрософт
description: Узнайте, как создавать группы управления Azure для управления множеством ресурсов.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 1592e47509f2537bef9cbcefd3cf49618561edcc
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Создание групп управления для упорядочения ресурсов и управления ими
Группы управления — это контейнеры, которые помогают управлять доступом, политикой и соответствием требованиям в нескольких подписках. Создание таких контейнеров позволяет построить эффективную и экономную иерархию, которую можно использовать с [политикой Azure](../azure-policy/azure-policy-introduction.md) и [элементами управления доступом на основе ролей Azure](../role-based-access-control/overview.md). Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](management-groups-overview.md). 

Функция групп управления предоставляется в общедоступной предварительной версии. Чтобы начать использование групп управления, войдите на [портал Azure](https://portal.azure.com) или воспользуйтесь [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) либо [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) для создания групп управления.   

Создание первой группы управления в каталоге может занять до 15 минут. Существуют процессы, выполняемые в первый раз при настройке службы групп управления в Azure для вашего каталога. По завершении процесса вы получите уведомление.  

## <a name="how-to-create-a-management-group"></a>Как создать группу управления
Группу управления можно создать с помощью портала, PowerShell или Azure CLI.

### <a name="create-in-portal"></a>Создание на портале

1. Войдите на [портал Azure](http://portal.azure.com).
2. Выберите **Все службы** > **Группы управления**.
3. На главной странице выберите **New Management group** (Создать группу управления). 

    ![Главная группа](media/management-groups/main.png) 
4.  Заполните поле идентификатора группы управления. 
    - **Идентификатор группы управления** — уникальный идентификатор каталога, используемый для отправки команд в этой группе управления. Идентификатор не редактируется после ее создания, так как он используется во всей системе Azure для идентификации этой группы. 
    - Поле отображаемого имени — это имя, которое отображается во всех разделах портала Azure. Отдельное отображаемое имя можно указать в необязательном поле при создании группы управления и изменить в любой момент.  

    ![Создание](media/management-groups/create_context_menu.png)  
5.  Нажмите кнопку **Сохранить**.


### <a name="create-in-powershell"></a>Создание в PowerShell
В PowerShell используются командлеты Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** — уникальный создаваемый идентификатор. Этот идентификатор используется в других командах для ссылки на эту группу, и впоследствии его нельзя изменить.

Если нужно, чтобы для группы управления на портале отображалось другое имя, следует добавить параметр **DisplayName** со строкой. Например, если нужно создать группу управления с именем (GroupName) Contoso и отображаемым именем (DisplayName) "Группа Contoso", необходимо использовать следующий командлет: 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Используйте параметр **ParentId**, чтобы создать эту группу управления под другим управлением.  

### <a name="create-in-azure-cli"></a>Создание в Azure CLI
В Azure CLI используется команда az account management-group create. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>Дополнительная информация 
Дополнительные сведения о группах управления: 
- [Упорядочение ресурсов с помощью групп управления Azure](management-groups-overview.md)
- [Изменение, удаление групп управления и управление ими](management-groups-manage.md)
- [Установка модуля Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Просмотр спецификации REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Установка расширения Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
