---
title: Уведомления защиты идентификации Azure Active Directory | Документация Майкрософт
description: Узнайте, как уведомления помогают в изучении проблем.
services: active-directory
keywords: защита удостоверений Azure Active Directory, Cloud App Discovery, управление приложениями, безопасность, риск, уровень риска, уязвимость, политика безопасности
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e72cfab8886f28982e5451362704d6a7a9fe59e4
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713819"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Уведомления защиты идентификации Azure Active Directory

Служба защиты идентификации Azure AD отправляет по электронной почте два типа автоматических уведомлений, что упрощает управление рисками для пользователей и событиями риска:

- сообщение электронной почты об обнаружении пользователей в группе риска;
- сообщение электронной почты еженедельного дайджеста.

В этой статье рассматриваются оба уведомления по электронной почте.


## <a name="users-at-risk-detected-email"></a>Сообщение электронной почты об обнаружении пользователей в группе риска

При обнаружении учетной записи в группе риска служба "Защита идентификации Azure Active Directory" создает уведомление по электронной почте с темой **Users at risk detected** (Обнаружены пользователи в группе риска). В таком электронном сообщении содержится ссылка на отчет **[Пользователи, находящиеся в группе риска](active-directory-reporting-security-user-at-risk.md)**. Мы советуем немедленно проверить пользователей в группе риска.

![сообщение электронной почты об обнаружении пользователей в группе риска;](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Параметр Configuration

Как администратор вы можете настроить:

- **Уровень риска, при котором формируется это письмо.** По умолчанию установлено значение High (Высокий).
- **Получателей.** По умолчанию это письмо отправляется всем глобальным администраторам. Глобальные администраторы также могут добавить в список получателей других глобальных администраторов, администраторов безопасности и читателей безопасности.  


Чтобы открыть связанное диалоговое окно, на странице **Защита идентификации** в разделе **Параметры** щелкните **Оповещения**.

![сообщение электронной почты об обнаружении пользователей в группе риска;](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>сообщение электронной почты еженедельного дайджеста.

В электронном сообщении еженедельного дайджеста содержится сводка новых событий риска.  
Сюда входят:

- пользователи под угрозой;

- подозрительные действия;

- обнаруженные уязвимости;

- ссылки на связанные отчеты в службе защиты идентификации.

    ![Исправление](./media/active-directory-identityprotection-notifications/400.png "Исправление")

### <a name="configuration"></a>Параметр Configuration

Как администратор вы можете отключить отправку еженедельных дайджестов по электронной почте.

![Риски для пользователя](./media/active-directory-identityprotection-notifications/62.png "Риски для пользователя")

Чтобы открыть связанное диалоговое окно, на странице **Защита идентификации** в разделе **Параметры** щелкните **Еженедельный дайджест**.

![сообщение электронной почты об обнаружении пользователей в группе риска;](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>См. также

- [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)
