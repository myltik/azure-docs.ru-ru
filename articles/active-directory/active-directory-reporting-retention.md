---
title: "Политики хранения отчетов Azure Active Directory | Документация Майкрософт"
description: "Политики хранения данных отчета в Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 47594d1d435ee92d57150a01a99765bab0e87846
ms.openlocfilehash: 2ac62c2c04975167b243b49bf2c536900e77df73
ms.lasthandoff: 02/03/2017


---
# <a name="azure-active-directory-report-retention-policies"></a>Политики хранения отчетов Azure Active Directory
*Данная документация является частью [руководства по отчетам Azure Active Directory](active-directory-reporting-guide.md).*


Эта статья содержит ответы на часто задаваемые вопросы о хранении данных для разных отчетов о действиях в Azure Active Directory. 

Как начать сбор данных о действиях?

| Выпуск Azure AD | Начало сбора |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | При регистрации для подписки |
| Azure AD уровня "Бесплатный" | При первом открытии [колонки Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) или использовании [API-интерфейсов отчетности](https://aka.ms/aadreports)  |


Когда данные о действиях становятся доступными на портале Azure?

- **Немедленно** — если вы уже работали с отчетами на классическом портале Azure.
- **В течение 2 часов** — если вы не включили функцию отчетности на классическом портале Azure.

Как начать сбор сигналов системы безопасности?  
Процесс сбора сигналов системы безопасности начинается, когда вы соглашаетесь использовать центр защиты идентификации. 

Как долго хранятся собранные данные?

**Отчеты о действиях**    

| Отчет | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| Аудит каталога | 7 дней | 30 дней | 30 дней |
| Действия при входе |    7 дней | 30 дней | 30 дней |

**Сигналы системы безопасности**

| Отчет | Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| Вход, представляющий риск | 7 дней | 30 дней | 90 дней |



