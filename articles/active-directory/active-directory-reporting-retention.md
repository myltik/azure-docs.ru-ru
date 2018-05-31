---
title: Политики хранения отчетов Azure Active Directory | Документация Майкрософт
description: Политики хранения данных отчета в Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9101b3877f8a011878baeed0d5c23d29fddaeaad
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055177"
---
# <a name="azure-active-directory-report-retention-policies"></a>Политики хранения отчетов Azure Active Directory


Эта статья содержит ответы на часто задаваемые вопросы о хранении данных для разных отчетов о действиях в Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Вопрос. Как начать сбор данных о действиях?

**Ответ.**

| Выпуск Azure AD | Начало сбора |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | При регистрации для подписки |
| Azure AD уровня "Бесплатный" | При первом открытии [колонки Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) или использовании [API-интерфейсов отчетности](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Вопрос. Когда данные о действиях становятся доступными на портале Azure?

**Ответ.**

- **Немедленно** — если вы уже работали с отчетами на портале Azure.
- **В течение 2 часов** — если вы не включили функцию отчетности на портале Azure.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Вопрос. Как начать сбор сигналов системы безопасности?  

**Ответ.** Процесс сбора сигналов системы безопасности начинается, когда вы соглашаетесь использовать центр защиты идентификации. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Вопрос. Как долго хранятся собранные данные?

**Ответ.**

**Отчеты о действиях**    

| Отчет                 | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Аудит каталога        | 7 дней        | 30 дней             | 30 дней             |
| Действия при входе       | Недоступно           | 30 дней             | 30 дней             |
| Использование Azure MFA        | 30 дней       | 30 дней             | 30 дней             |

**Сигналы системы безопасности**

| Отчет         | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| пользователи под угрозой;  | 7 дней        | 30 дней             | 90 дней             |
| Вход, представляющий риск | 7 дней        | 30 дней             | 90 дней             |

---
