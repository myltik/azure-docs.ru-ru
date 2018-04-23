---
title: Отчеты о доступе и использовании для Azure MFA | Документация Майкрософт
description: Эта статья содержит информацию о том, как использовать функцию отчетов службы Многофакторной идентификации Azure.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 3a29d1b17924b519167f362e6116f405e182bf75
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Отчеты в службе Многофакторной идентификации Azure

Служба "Многофакторная идентификация Azure" предоставляет несколько типов отчетов, которые могут быть полезны для вас и вашей организации. Просмотреть их можно на портале Azure. В следующей таблице перечислены доступные отчеты:

| Отчет | Расположение | ОПИСАНИЕ |
|:--- |:--- |:--- |
| Журнал заблокированных пользователей | "Azure AD > Сервер MFA > Блокировать или разблокировать пользователей" | История запросов на блокировку или разблокирование пользователей. |
| Предупреждения об использовании и о мошенничестве | "Azure AD > События входа" | Сведения об общем использовании, сводка пользователей, сведения о пользователях, а также история предупреждений о мошенничестве, отправленных в течение указанного диапазона дат. |
| Использование локальных компонентов | "Azure AD > Сервер MFA > Отчет о действиях" | Сведения об общем использовании MFA через расширение NPS, ADFS и сервер MFA. |
| Журнал обойденных пользователей | "Azure AD > Сервер MFA > Одноразовый обход проверки" | История запросов на обход многофакторной проверки подлинности для пользователя. |
| Состояние сервера | "Azure AD > Сервер MFA > Состояние сервера" | Сведения о состоянии серверов Многофакторной идентификации, с которыми связана ваша учетная запись. |

## <a name="view-reports"></a>Просмотр отчетов 

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левой части экрана выберите **Azure Active Directory** > **Сервер MFA**.
3. Выберите отчет, который нужно просмотреть.

   <center>![Облако](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Создание отчетов PowerShell

Определите пользователей, зарегистрированных для многофакторной проверки подлинности с помощью Powershell:

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Определите пользователей, не зарегистрированных для многофакторной проверки подлинности с помощью Powershell:

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Дополнительная информация

* [Для пользователей](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)
* [Выберите для себя решение "Многофакторная идентификация Microsoft Azure"](concept-mfa-whichversion.md)
