---
title: Примеры PowerShell для управления группами в Azure Active Directory | Документация Майкрософт
description: На этой странице представлены примеры командлетов PowerShell, которые помогут вам управлять группами в Azure Active Directory.
keywords: Azure AD, Azure Active Directory, PowerShell, группы, управление группами
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: rodejo
ms.openlocfilehash: 312efd6233546ae32e498907e04fbf8aea73f7b7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Командлеты Azure Active Directory версии 2 для управления группами
> [!div class="op_single_selector"]
> * [портал Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

В этой статье приведены примеры управления группами в Azure Active Directory (Azure AD) с помощью PowerShell.  Кроме того, в ней содержатся сведения о том, как выполнить настройки модуля Azure AD PowerShell. Во-первых, необходимо [скачать модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Установка модуля Azure AD PowerShell
Чтобы установить модуль Azure AD PowerShell, выполните следующие команды:

    PS C:\Windows\system32> install-module azuread

Чтобы проверить, установлен ли модуль, воспользуйтесь следующей командой:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Теперь можно начать использование командлетов в модуле. Полное описание командлетов в модуле Azure AD см. в электронной справочной документации по [PowerShell версии 2 для Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Подключение к каталогу
Прежде чем приступить к управлению группами с помощью командлетов Azure AD PowerShell, необходимо подключить сеанс PowerShell к каталогу, которым вы хотите управлять. Используйте следующую команду:

    PS C:\Windows\system32> Connect-AzureAD

Командлет запросит ввести учетные данные, которые вы хотите использовать для доступа к каталогу. В этом примере для доступа к демонстрационному каталогу используется karen@drumkit.onmicrosoft.com . Командлет возвращает подтверждение, показывающее, что сеанс был успешно подключен к каталогу:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Теперь можно начать использование командлетов Azure AD для управления группами в каталоге.

## <a name="retrieve-groups"></a>Получение сведений о группах
Чтобы получить сведения о существующих группах из каталога, используйте командлет Get-AzureADGroups. 

Чтобы получить сведения о всех группах в каталоге, используйте командлет без параметров:

    PS C:\Windows\system32> get-azureadgroup

Командлет возвращает сведения о всех группах в подключенном каталоге.

Для получения сведений о конкретной группе, для которой указывается идентификатор объекта, можно использовать параметр -objectID:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Командлет возвращает сведения о группе, идентификатор objectID которой совпадает со значением введенного параметра:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Для поиска конкретной группы можно использовать параметр -filter. Этот параметр принимает предложение фильтра ODATA и возвращает сведения о всех группах, соответствующих фильтру, как показано в следующем примере:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Командлеты Azure AD PowerShell используют стандарт запросов OData. Дополнительные сведения см. в разделе **$filter** статьи [OData system query options using the OData endpoint](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter) (Параметры системных запросов OData с использованием конечной точки OData).

## <a name="create-groups"></a>Создание групп
Чтобы создать новую группу в каталоге, используйте командлет New-AzureADGroup. Этот командлет создает новую группу безопасности с именем Marketing:

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Обновление групп
Чтобы обновить существующую группу, используйте командлет Set-AzureADGroup. В этом примере изменяется свойство DisplayName группы Intune Administrators. Сначала с помощью командлета Get-AzureADGroup мы находим группу и фильтруем, используя атрибут DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Затем мы меняем свойство Description на новое значение — Intune Device Administrators:

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Если теперь снова выполнить поиск группы, то мы увидим, что свойство Description обновилось и отражает новое значение:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="delete-groups"></a>Удаление групп
Для удаления групп из каталога используйте командлет Remove-AzureADGroup, как показано ниже:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Управление членством в группах 
### <a name="add-members"></a>Добавление членов
Чтобы добавить в группу новых членов, используйте командлет Add-AzureADGroupMember. Эта команда добавляет члена в группу Intune Administrators, которую мы использовали в предыдущем примере:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Параметр -ObjectId — это идентификатор объекта группы, в которую требуется добавить члена, а -RefObjectId — это идентификатор объекта пользователя, которого требуется добавить в качестве члена группы.

### <a name="get-members"></a>Получение сведений о членах
Чтобы получить сведения о существующих членах группы, используйте командлет Get-AzureADGroupMember, как показано в этом примере:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Удаление членов
Чтобы удалить члена, ранее добавленного в группу, используйте командлет Remove-AzureADGroupMember, как показано здесь:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Проверка членства
Чтобы проверить, является ли пользователь членом какой-либо группы, используйте командлет Select-AzureADGroupIdsUserIsMemberOf. В качестве параметров этот командлет принимает идентификатор объекта пользователя, для которого выполняется проверка членства в группах, и список групп, в которых проверяется членство. Список групп необходимо указать в форме сложной переменной типа Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck, поэтому сначала необходимо создать переменную с этим типом:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Затем необходимо указать значения идентификаторов группы для регистрации атрибута GroupIds этой сложной переменной:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Теперь, если требуется проверить членство пользователя с идентификатором объекта 72cd4bbd-2594-40a2-935c-016f3cfeeeea в группах в $g, используйте следующую команду:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Возвращаемое значение — это список групп, членом которых является данный пользователь. Этот метод также можно применять для проверки членства в контактах, группах или субъектах-службах для определенного списка групп. Для этого используйте командлеты Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf и Select-AzureADGroupIdsServicePrincipalIsMemberOf.

## <a name="disable-group-creation-by-your-users"></a>Отключение создания групп пользователями
Вы можете запретить пользователям без прав администратора создавать группы безопасности. По умолчанию в службе каталогов MS Online (MSODS) пользователям без прав администратора разрешается создавать группы, независимо от того, включено ли при этом самостоятельное управление группами (SSGM). Параметр SSGM позволяет управлять поведением только на панели доступа "Мои приложения". 

Чтобы отключить создание групп для пользователей без прав администратора, сделайте следующее:

1. Убедитесь, что пользователи без прав администратора могут создавать группы.
   
  ````
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ````
  
2. Если этот код вернет `UsersPermissionToCreateGroupsEnabled : True`, то пользователи без прав администратора могут создавать группы. Чтобы отключить эту функцию, используйте следующий код.
  
  ```` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ````
  
## <a name="manage-owners-of-groups"></a>Управление владельцами групп
Чтобы добавить в группу новых владельцев, воспользуйтесь командлетом Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Параметр -ObjectId — это идентификатор объекта группы, в которую требуется добавить владельца, а -RefObjectId — это идентификатор объекта пользователя, которого требуется добавить в качестве владельца группы.

Для получения сведений о владельцах группы используйте командлет Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Командлет возвращает список владельцев для указанной группы:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Для удаления владельца из группы используйте командлет Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Зарезервированные псевдонимы 
После создания группы определенные конечные точки позволяют пользователю указать mailNickname или псевдоним, который можно использовать как часть адреса электронной почты группы. Группы с приведенными ниже привилегированными псевдонимами электронной почты может создавать только глобальный администратор Azure AD. 
  
* abuse 
* admin 
* administrator 
* hostmaster 
* majordomo 
* postmaster 
* root 
* secure 
* security 
* ssl-admin 
* webmaster 

## <a name="next-steps"></a>Дополнительная информация
Дополнительную документацию по PowerShell Azure Active Directory см. в разделе [Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0) (Командлеты Azure Active Directory).

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
