---
title: "Отчеты о доступе и использовании для Azure MFA | Документация Майкрософт"
description: "Эта статья содержит информацию о том, как использовать функцию отчетов службы Многофакторной идентификации Azure."
services: multi-factor-authentication
documentationcenter: 
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
ms.openlocfilehash: 696f4ae3cb479a208e73e53a9a9a437caeabd294
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Отчеты в службе Многофакторной идентификации Azure

Azure Multi-factor Authentication предоставляет несколько отчетов, которые могут использоваться вами и вашей организации доступны через портал Azure. В следующей таблице перечислены доступные отчеты:

| Отчет | Расположение | ОПИСАНИЕ |
|:--- |:--- |:--- |
| Журнал заблокированных пользователей | Azure AD > многофакторной проверки Подлинности сервера > Блокировка и разблокировка пользователей | Показывает журнал запросов на блокировку или разблокирование пользователей. |
| Предупреждения об использовании и о мошенничестве | Azure AD > входа в систему | Сведения об общем использовании, Сводка пользователей и сведения о пользователе; а также журнал предупреждений о мошенничестве, отправленных в течение указанного диапазона дат. |
| Использование для локальных компонентов | Azure AD > многофакторной проверки Подлинности сервера > отчет о действиях | Предоставляет сведения о общее использование многофакторной проверки Подлинности через модуль NPS ADFS и сервер многофакторной проверки Подлинности. |
| Журнал обойденных пользователей | Azure AD > многофакторной проверки Подлинности сервера > одноразовый обход проверки | Содержит журнал запросов на обход проверки многофакторной проверки подлинности для пользователя. |
| Состояние сервера | Azure AD > многофакторной проверки Подлинности сервера > состояние сервера | Отображает состояние серверов Multi-factor Authentication, связанный с вашей учетной записью. |

## <a name="view-reports"></a>Просмотр отчетов 

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левой части экрана выберите **Azure Active Directory** > **многофакторной проверки Подлинности сервера**.
3. Выберите отчет, который нужно просмотреть.

   <center>![Облако](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell отчетов

Определите пользователей, зарегистрированных с многофакторной проверки Подлинности с помощью PowerShell, который соответствует.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Определите пользователей, не имеющих зарегистрированные многофакторной проверки подлинности с помощью PowerShell, который соответствует.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Дальнейшие действия

* [Для пользователей](end-user/multi-factor-authentication-end-user.md)
* [Место развертывания](multi-factor-authentication-get-started.md)
