---
title: "Уведомления защиты идентификации Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как уведомления помогают в изучении проблем."
services: active-directory
keywords: "защита удостоверений Azure Active Directory, Cloud App Discovery, управление приложениями, безопасность, риск, уровень риска, уязвимость, политика безопасности"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Уведомления защиты идентификации Azure Active Directory

Служба защиты идентификации Azure AD отправляет по электронной почте два типа автоматических уведомлений, что упрощает управление рисками для пользователей и событиями риска:

- Пользователи риску обнаружил электронной почты
- сообщение электронной почты еженедельного дайджеста.

В этой статье предоставляется обзор оба уведомления по электронной почте.


## <a name="users-at-risk-detected-email"></a>Пользователи риску обнаружил электронной почты

В ответ на обнаруженных учетной записи под угрозой, Azure AD Identity Protection приводит к возникновению ошибки оповещение по электронной почте с **пользователей риску обнаружил** в строке темы. Сообщение электронной почты ссылка на  **[пользователей с отметкой риск](active-directory-reporting-security-user-at-risk.md)**  отчета. Рекомендуется следует немедленно проверить пользователях под угрозой.

![Пользователи риску обнаружил электронной почты](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Параметр Configuration

Как администратор можно задать:

- **Уровень риска, которое вызывает создание этого электронного сообщения** -по умолчанию имеет значение уровень риска риск «Высокие».
- **Это сообщение электронной почты получателей** -по умолчанию получателей включают всем глобальным администраторам. Глобальные администраторы также можно добавить другие средства чтения безопасности глобальных администраторов, администраторов безопасности получателями.  


Чтобы открыть диалоговое окно связанные, нажмите кнопку **оповещения** в **параметры** раздел **защиты идентификации** страницы.

![Пользователи риску обнаружил электронной почты](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>сообщение электронной почты еженедельного дайджеста.

В электронном сообщении еженедельного дайджеста содержится сводка новых событий риска.  
Сюда входят:

- пользователи под угрозой;

- подозрительные действия;

- обнаруженные уязвимости;

- ссылки на связанные отчеты в службе защиты идентификации.

    ![Исправление](./media/active-directory-identityprotection-notifications/400.png "Исправление")

### <a name="configuration"></a>Параметр Configuration

Как администратор можно переключиться отправки еженедельная хэш-кода по электронной почте.

![Риски для пользователя](./media/active-directory-identityprotection-notifications/62.png "Риски для пользователя")

Чтобы открыть диалоговое окно связанные, нажмите кнопку **еженедельный дайджест** в **параметры** раздел **защиты идентификации** страницы.

![Пользователи риску обнаружил электронной почты](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>См. также

- [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)
