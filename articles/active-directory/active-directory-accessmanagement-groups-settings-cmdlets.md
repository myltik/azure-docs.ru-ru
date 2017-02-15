---
title: "Настройка параметров группы с помощью командлетов Azure Active Directory | Документация Майкрософт"
description: "Управление параметрами групп с помощью командлетов Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c18ef38661e31e16114b88bdfad36320776ef12b


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Настройка параметров групп с помощью командлетов Azure Active Directory
Можно настроить следующие параметры для объединенных групп в каталоге.

1. Классификации: разделенный запятыми список классификаций, которые пользователи могут присваивать группе. Например, Classified (Конфиденциально), Secret (Секретно) и Top Secret (Совершенно секретно).
2. URL-адрес правил использования: URL-адрес, указывающий пользователям на условия использования объединенных групп, определенные вашей организацией. Этот URL-адрес будет отображаться в пользовательском интерфейсе, в котором пользователи используют группы.
3. Создание групп включено: никто из пользователей, некоторые из них или все пользователи могут создавать объединенные группы. Когда этот параметр включен, все пользователи могут создавать группы. Когда этот параметр отключен, никто из пользователей не может создавать группы. Когда параметр отключен, можно также указать группу безопасности, члены которой все же смогут создавать группы.

Эти параметры настраиваются с помощью объектов Settings и SettingsTemplate. Изначально в каталоге не отображаются объекты Settings. Это означает, что каталог настроен с параметрами по умолчанию. Чтобы изменить параметры по умолчанию, необходимо с помощью шаблона параметров создать объект параметров. Шаблоны параметров определены корпорацией Майкрософт.

С [сайта Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)можно скачать модуль, содержащий командлеты, которые используются для этих операций.

## <a name="create-settings-at-the-directory-level"></a>Создание параметров на уровне каталога
Далее приведены действия, необходимые для создания параметров на уровне каталога, применимые ко всем группам Office в каталоге.

1. Если вы не знаете, какой объект SettingTemplate использовать, этот командлет возвращает список шаблонов параметров:

    `Get-MsolAllSettingTemplate`

    ![Список шаблонов параметров](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. Чтобы добавить URL-адрес правил использования, сначала необходимо получить объект SettingsTemplate, который определяет значение URL-адреса правил использования; это — шаблон Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. Затем создайте новый объект параметров на основе этого шаблона:

    `$setting = $template.CreateSettingsObject()`
4. Затем обновите значение правил использования:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. И, наконец, примените параметры:

    `New-MsolSettings –SettingsObject $setting`

    ![Добавление URL-адреса правил использования](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Ниже приведены параметры, определенные в объекте SettingsTemplate шаблона Group.Unified.

| **Параметр** | **Описание** |
| --- | --- |
|  <ul><li>ClassificationList<li>Тип: строка<li>Default: “” |Разделенный запятыми список допустимых значений классификации, которые можно применять к объединенным группам. |
|  <ul><li>EnableGroupCreation<li>Тип: логический<li> значение по умолчанию: True |Флаг, указывающий, разрешено ли создание объединенных групп в каталоге. |
|  <ul><li>GroupCreationAllowedGroupId<li>Тип: строка<li>Default: “” |Идентификатор GUID группы безопасности, которой разрешено создавать объединенные группы, даже когда EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Тип: строка<li>Default: “” |Ссылка на правила использования группы. |

## <a name="read-settings-at-the-directory-level"></a>Чтение параметров на уровне каталога
Далее приведены действия, необходимые для чтения параметров на уровне каталога, применимые ко всем группам Office в каталоге.

1. Чтение всех существующих параметров каталога:

    `Get-MsolAllSettings`
2. Чтение всех параметров определенной группы:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. Чтение определенных параметров каталога, использующих идентификатор GUID SettingId:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![Идентификатор GUID параметров](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Обновление параметров на уровне каталога
Далее приведены действия, необходимые для обновления параметров на уровне каталога, применимые ко всем группам Office в каталоге.

1. Получение существующего объекта Settings:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. Получение значения, которое требуется обновить:

    `$value = $Setting.GetSettingsValue()`
3. Обновление значения:

    `$value["AllowToAddGuests"] = "false"`
4. Обновление параметра:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Удаление параметров на уровне каталога
Далее приведено действие, необходимое для удаления параметров на уровне каталога, применимое ко всем группам Office в каталоге.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Справочник по синтаксису командлетов
Дополнительную документацию по PowerShell Azure Active Directory см. в разделе [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260) (Командлеты Azure Active Directory).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Ссылка на объект SettingsTemplate (объект SettingsTemplate шаблона Group.Unified)
* "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "Логический флаг, указывающий, включена ли функция создания объединенных групп."
* "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "Идентификатор GUID группы безопасности, внесенной в список расширений и имеющей право на создание объединенных групп."
* "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "Разделенный запятыми список допустимых значений классификации, которые можно применять к объединенным группам."
* "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "Ссылка на правила использования группы."

| name | type | defaultValue | Описание |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"System.Boolean" |True |"Логический флаг, указывающий, включена ли функция создания объединенных групп." |
| "GroupCreationAllowedGroupId" |"System.Guid" |"" |"Идентификатор GUID группы безопасности, внесенной в список расширений и имеющей право на создание объединенных групп." |
| "ClassificationList" |"System.String" |"" |"Разделенный запятыми список допустимых значений классификации, которые можно применять к объединенным группам." |
| "UsageGuidelinesUrl" |"System.String" |"" |"Ссылка на правила использования группы." |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную документацию по PowerShell Azure Active Directory см. в разделе [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260) (Командлеты Azure Active Directory).

Дополнительные инструкции от руководителя программы Майкрософт Роба де Йонга (Rob de Jong) см. в [групповом блоге Роба](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Dec16_HO4-->


