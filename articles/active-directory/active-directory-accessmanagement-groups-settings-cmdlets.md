---
title: Настройка параметров группы с помощью PowerShell в Azure Active Directory | Документация Майкрософт
description: Управление параметрами групп с помощью командлетов Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 02/20/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 5da5bd920becdc86e133cb1b4e67fb3125c2638e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260538"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Настройка параметров групп с помощью командлетов Azure Active Directory
Эта статья содержит инструкции по использованию командлетов PowerShell в Azure Active Directory (Azure AD) для создания и изменения групп. Это содержимое относится только к группам Office 365, также известным как единые группы. 

> [!IMPORTANT]
> Для доступа к некоторым параметрам требуется лицензия Azure Active Directory Premium P1. Дополнительные сведения см. [здесь](#template-settings).

Чтобы узнать, как запретить пользователям без прав администратора создавать группы *безопасности*, установите `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False`, как описано в статье [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Параметры групп Office 365 настраиваются с помощью объектов Settings и SettingsTemplate. Изначально в каталоге не отображаются все объекты параметров, так как он настроен с параметрами по умолчанию. Чтобы изменить параметры по умолчанию, необходимо с помощью шаблона параметров создать объект параметров. Шаблоны параметров определены корпорацией Майкрософт. Поддерживается несколько разных шаблонов параметров. Чтобы настроить параметры группы Office 365 для каталога, используйте шаблон Group.Unified. Для настройки параметров отдельной группы Office 365 используйте шаблон Group.Unified.Guest. Этот шаблон используется для управления гостевым доступом к группе Office 365. 

Командлеты являются частью модуля PowerShell версии 2 для Azure Active Directory. Дополнительные сведения о скачивании и установке модуля на компьютер см. в статье [PowerShell версии 2 для Azure Active Directory](https://docs.microsoft.com/powershell/azuread/). Вы можете установить выпуск версии 2 модуля [из коллекции PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Получение определенного значения параметра
Если вам известно имя параметра, которое необходимо получить, можно использовать приведенный ниже командлет. В этом примере показано получение значения для параметра с именем UsageGuidelinesUrl. Дополнительные сведения о параметрах каталога и их именах приводятся далее в этой статье.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Создание параметров на уровне каталога
Далее приведены действия, необходимые для создания параметров на уровне каталога, применимые ко всем группам Office 365 в каталоге.

1. В командлетах DirectorySettings необходимо указать идентификатор объекта SettingsTemplate, который вы хотите использовать. Если он вам неизвестен, выполните приведенный ниже командлет, чтобы отобразить все шаблоны параметров.
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Этот командлет отображает все шаблоны, которые доступны.
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Чтобы добавить URL-адрес правил использования, сначала необходимо получить объект SettingsTemplate, который определяет значение URL-адреса правил использования; это — шаблон Group.Unified:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Затем создайте новый объект параметров на основе этого шаблона:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Затем обновите значение правил использования:
  
  ```
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"

  ```  
5. И, наконец, примените параметры:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

После успешного выполнения командлет возвращает идентификатор нового объекта Settings.
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Параметры шаблона
Ниже приведены параметры, определенные в объекте SettingsTemplate шаблона Group.Unified. Если не указано иное, для доступа к этим параметрам требуется лицензия Azure Active Directory Premium P1. 

| **Параметр** | **Описание** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Тип: логический<li>значение по умолчанию: True |Флаг, указывающий, разрешено ли пользователям без прав администратора создание группы Office 365 в каталоге. Для этого параметра не требуется лицензия Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Тип: строка<li>Default: “” |Идентификатор GUID группы безопасности, участникам которой разрешено создавать группы Office 365, даже когда EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Тип: строка<li>Default: “” |Ссылка на правила использования группы. |
|  <ul><li>ClassificationDescriptions<li>Тип: строка<li>Default: “” | Разделенный запятыми список описаний классификаций. |
|  <ul><li>DefaultClassification<li>Тип: строка<li>Default: “” | Классификация, которая должна использоваться по умолчанию для группы, если не была указана иная классификация.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Тип: строка<li>Default: “” | Строка с максимальной длиной 64 символа, которая определяет соглашение об именовании, настроенное для групп Office 365. Дополнительные сведения см. в статье [Enforce a naming policy for Office 365 groups in Azure Active Directory (preview)](groups-naming-policy.md) (Принудительное применение политики именования для групп Office 365 (предварительная версия)). |
| <ul><li>CustomBlockedWordsList<li>Тип: строка<li>Default: “” | Строка фраз с разделителями-запятыми, которые не разрешено использовать в именах или псевдонимах групп. Дополнительные сведения см. в статье [Enforce a naming policy for Office 365 groups in Azure Active Directory (preview)](groups-naming-policy.md) (Принудительное применение политики именования для групп Office 365 (предварительная версия)). |
| <ul><li>EnableMSStandardBlockedWords<li>Тип: логический<li>Значение по умолчанию: False. | Не используйте.
|  <ul><li>AllowGuestsToBeGroupOwner<li>Тип: логический<li>значение по умолчанию: False | Логическое значение, указывающее, может ли гостевой пользователь быть владельцем группы. |
|  <ul><li>AllowGuestsToAccessGroups<li>Тип: логический<li>значение по умолчанию: True | Логическое значение, указывающее, может ли гостевой пользователь иметь доступ к содержимому групп Office 365.  Для этого параметра не требуется лицензия Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Тип: строка<li>Default: “” | URL-адрес ссылки на правила использования гостя. |
|  <ul><li>AllowToAddGuests<li>Тип: логический<li>значение по умолчанию: True | Логическое значение, указывающее, разрешено ли добавлять гостей в этот каталог.|
|  <ul><li>ClassificationList<li>Тип: строка<li>Default: “” |Разделенный запятыми список допустимых значений классификации, которые можно применять к группам Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Чтение параметров на уровне каталога
Далее приведены действия, необходимые для чтения параметров на уровне каталога, применимые ко всем группам Office в каталоге.

1. Чтение всех существующих параметров каталога:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Этот командлет возвращает список всех параметров каталога:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Чтение всех параметров определенной группы:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Чтение всех значений параметров каталога из объекта Settings конкретного каталога по его идентификатору GUID.
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Этот командлет возвращает имена и значения в объекте Settings для этой конкретной группы:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  CustomBlockedWordsList        
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            https://guideline.example.com
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Обновление параметров определенной группы

1. Найдите шаблон параметров Groups.Unified.Guest.
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Получите объект SettingTemplate для шаблона Groups.Unified.Guest.
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Создайте объект Settings из шаблона.
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Установите требуемое значение для параметра.
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Создайте параметр для требуемой группы в каталоге.
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Обновление параметров на уровне каталога

Далее приведены действия, необходимые для обновления параметров на уровне каталога, применимые ко всем группам Office 365 в каталоге. В этих примерах предполагается, что в каталоге уже существует объект Settings.

1. Найдите существующий объект Settings.
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Обновление значения:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Обновление параметра:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Удаление параметров на уровне каталога
Далее приведено действие, необходимое для удаления параметров на уровне каталога, применимое ко всем группам Office в каталоге.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Справочник по синтаксису командлетов
Дополнительную документацию по PowerShell Azure Active Directory см. в разделе [Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0) (Командлеты Azure Active Directory).

## <a name="additional-reading"></a>Дополнительные материалы

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
