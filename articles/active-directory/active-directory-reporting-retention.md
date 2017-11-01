---
title: "Политики хранения отчетов Azure Active Directory | Документация Майкрософт"
description: "Политики хранения данных отчета в Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 933d251903f4ca74902e984e7a1581a75345ee7f
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Политики хранения отчетов Azure Active Directory


Эта статья содержит ответы на часто задаваемые вопросы о хранении данных для разных отчетов о действиях в Azure Active Directory. 

**Вопрос. Как начать сбор данных о действиях?**

**Ответ.**

| Выпуск Azure AD | Начало сбора |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | При регистрации для подписки |
| Azure AD уровня "Бесплатный" | При первом открытии [колонки Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) или использовании [API-интерфейсов отчетности](https://aka.ms/aadreports)  |

---
**Вопрос. Когда данные о действиях становятся доступными на портале Azure?**

**Ответ.**

- **Немедленно** — если вы уже работали с отчетами на классическом портале Azure.
- **В течение 2 часов** — если вы не включили функцию отчетности на классическом портале Azure.

---
**Вопрос. Как начать сбор сигналов системы безопасности?**  

**Ответ.** Процесс сбора сигналов системы безопасности начинается, когда вы соглашаетесь использовать центр защиты идентификации. 


---
**Вопрос. Как долго хранятся собранные данные?**

**Ответ.**

**Отчеты о действиях**    

| Отчет                 | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Аудит каталога        | 7 дней        | 30 дней             | 30 дней             |
| Действия при входе       | Недоступно           | 30 дней             | 30 дней             |
| Использование Azure MFA        | 90 дней       | 90 дней             | 90 дней             |

**Сигналы системы безопасности**

| Отчет         | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| пользователи под угрозой;  | 7 дней        | 30 дней             | 90 дней             |
| Вход, представляющий риск | 7 дней        | 30 дней             | 90 дней             |

---
