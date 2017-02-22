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
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 8a7d1bd1db8abdac5b741d79b10c9b363afecd24
ms.openlocfilehash: 9f8d9c845c391ec24aa3b265aa3078840fe3240a


---
# <a name="azure-active-directory-report-retention-policies"></a>Политики хранения отчетов Azure Active Directory
*Данная документация является частью [руководства по отчетам Azure Active Directory](active-directory-reporting-guide.md).*


Эта статья содержит ответы на часто задаваемые вопросы о хранении данных для разных отчетов о действиях в Azure Active Directory. 

Как начать сбор данных о действиях?

| Выпуск Azure AD | Начало сбора |
| :--              | :--   |
|Уровни "Премиум" и "Премиум 2" | При регистрации для использования лицензии |
| Free | При первом открытии [колонки Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) или использовании [API-интерфейсов отчетности](https://aka.ms/aadreports)  |


Когда данные о действиях становятся доступными на портале Azure?

- **Немедленно** — если вы уже работали с отчетами на классическом портале Azure.
- **В течение 2 часов** — если вы не включили функцию отчетности на классическом портале Azure.

Как начать сбор сигналов системы безопасности?  
Процесс сбора сигналов системы безопасности начинается, когда вы соглашаетесь использовать центр защиты идентификации. 

Как долго хранятся собранные данные?

**Отчеты о действиях**    

| Отчет | Azure AD уровня "Бесплатный" | Azure AD уровня "Премиум 1" | Azure AD уровня "Премиум 2" |
| :--    | :--           | :--                | :--                |
| Аудит каталога | 7 дней | 30 дней | 30 дней |
| Действия при входе |    7 дней | 30 дней | 30 дней |

**Сигналы системы безопасности**

| Отчет | Azure AD уровня "Бесплатный" | Azure AD уровня "Премиум 1" | Azure AD уровня "Премиум 2" |
| :--    | :--           | :--                | :--                |
| Вход, представляющий риск | 7 дней | 30 дней | 90 дней |





<!--HONumber=Dec16_HO4-->


