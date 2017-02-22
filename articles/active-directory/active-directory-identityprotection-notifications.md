---
title: "Уведомления защиты идентификации Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как уведомления помогают в изучении проблем."
services: active-directory
keywords: "защита удостоверений Azure Active Directory, Cloud App Discovery, управление приложениями, безопасность, риск, уровень риска, уязвимость, политика безопасности"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ce3379d5b5e883c6601c40aca191e8b84e3ad8d3
ms.openlocfilehash: 0170b5d2435f6e856478ee9e55ae26c626288f75


---
# <a name="azure-active-directory-identity-protection-notifications"></a>Уведомления защиты идентификации Azure Active Directory
Служба защиты идентификации Azure AD отправляет по электронной почте два типа автоматических уведомлений, что упрощает управление рисками для пользователей и событиями риска:

* оповещение электронной почты о том, что пользователь скомпрометирован;
* сообщение электронной почты еженедельного дайджеста.

## <a name="user-compromised-alert-email"></a>оповещение электронной почты о том, что пользователь скомпрометирован;
Электронное оповещение о том, что пользователь скомпрометирован, создается, если служба защиты идентификации Azure AD определяет, что учетная запись скомпрометирована. В таком электронном сообщении содержится ссылка на отчет о пользователях, помеченных для события риска, на панели мониторинга защиты идентификации. Мы рекомендуем безотлагательно проверить уведомления о скомпрометированных учетных записях.

## <a name="weekly-digest-email"></a>сообщение электронной почты еженедельного дайджеста.
В электронном сообщении еженедельного дайджеста содержится сводка новых событий риска.<br>
Сюда входят:

* пользователи под угрозой;
* подозрительные действия;
* обнаруженные уязвимости;
* ссылки на связанные отчеты в службе защиты идентификации.

<br>
![Исправление](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br>

Можно отключить отправку еженедельных дайджестов по электронной почте.
<br><br>
![Риск для пользователя](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**Вот как можно открыть диалоговое окно настройки**.

1. В колонке **Защита идентификации Azure AD** щелкните **Параметры**.
   <br><br>
   ![Политика риска пользователя](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. В разделе **Общие** щелкните **Уведомления**.
   <br><br>
   ![Политика риска пользователя](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

## <a name="see-also"></a>Дополнительные материалы
* [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)



<!--HONumber=Dec16_HO4-->


