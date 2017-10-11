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
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 079d16bbf75cd2b3b94269d684e1ae1a0e6aa967
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
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
![Исправления](./media/active-directory-identityprotection-notifications/400.png "исправления")
<br>

Можно отключить отправку еженедельных дайджестов по электронной почте.
<br><br>
![Рисков для пользователя](./media/active-directory-identityprotection-notifications/62.png "рисков для пользователя")
<br>

**Вот как можно открыть диалоговое окно настройки**.

1. В колонке **Защита идентификации Azure AD** щелкните **Параметры**.
   <br><br>
   ![Политика пользователя риск](./media/active-directory-identityprotection-notifications/401.png "риск политика пользователя")
   <br>
2. В разделе **Общие** щелкните **Уведомления**.
   <br><br>
   ![Политика пользователя риск](./media/active-directory-identityprotection-notifications/405.png "риск политика пользователя")
   <br>

## <a name="see-also"></a>См. также
* [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)
