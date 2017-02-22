---
title: "Определения и примеры для API отчетов об использовании Azure AD B2C | Документация Майкрософт"
description: "Руководство по получению отчетов по пользователям, операциям аутентификации и MFA для клиента B2C (с примером)."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Доступ к отчетам об использовании в Azure AD B2C с помощью API отчетов

Azure Active Directory B2C предоставляет аутентификацию на основе имени для входа и MFA для всех пользователей вашего семейства приложений у поставщиков удостоверений.  Зная количество пользователей, зарегистрированных в клиенте, какие поставщики использовались для регистрации и сколько типов аутентификации используется, ответьте на следующие вопросы.
* Сколько пользователей для каждого типа поставщика удостоверений (например, учетной записи Майкрософт, LinkedIn) зарегистрировано за последние 10 дней?
* Сколько успешных операций Многофакторной идентификации было выполнено за последний месяц?
* Сколько успешных операций аутентификации на основе имени для входа было выполнено за последний месяц? В день? На приложение?
* Как можно приблизительно оценить ожидаемые ежемесячные расходы на работу моего клиента B2C?

Эта статья посвящена отчетам, наиболее тесно связанным с выставлением счетов, которое основано на количестве пользователей, оплачиваемых операций аутентификации на основе имени для входа и операций Многофакторной идентификации.


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Предварительные требования для доступа к API отчетов Azure AD
Перед началом работы выполните [предварительные требования для доступа к интерфейсам API отчетов Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).  Создайте приложение, получите его секрет и предоставьте ему права доступа к отчетам своего клиента Azure AD B2C. В статье также приведены примеры *сценария Bash* и *сценария Python*.

## <a name="powershell-script"></a>Сценарий PowerShell
В этом сценарии показаны четыре отчета об использовании, в которых применяются параметр **TimeStamp** и фильтр по **-ApplicationId**.

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Определения отчетов об использовании
**tenantUserCount** считает количество пользователей в клиенте по типу поставщика удостоверений в день за последние 30 дней. (При необходимости фильтр TimeStamp предоставляет количество пользователей с указанной даты до текущей даты). Данный отчет содержит:
 * TotalUserCount — число всех объектов-пользователей;
 * OtherUserCount — число пользователей каталога AAD (не являющихся пользователями B2C);
 * LocalUserCount — число учетных записей пользователей B2C с локальными учетными данными в клиенте B2C;

**AlternateIdUserCount** — число пользователей B2C, зарегистрированных с помощью внешних поставщиков удостоверений (например, Facebook, учетная запись Майкрософт или другие клиенты AAD, в т. ч. OrgId).

**b2cAuthenticationCountSummary** — сводка по количеству платных ежедневных операций аутентификации за последние 30 дней по дням и типу последовательности аутентификации.

**b2cAuthenticationCount** — количество операций аутентификации за период времени. По умолчанию — за последние 30 дней.  (Необязательно. Начальные и завершающие элементы TimeStamp определяют конкретный период подсчета.) Выходные данные содержат StartTimeStamp (самая ранняя дата действия этого клиента) и EndTimeStamp (дата последнего обновления).

**b2cMfaRequestCountSummary** — сводка по количеству ежедневных операций Многофакторной идентификации по дням и типу Многофакторной идентификации (с помощью SMS или голосовой связи).


## <a name="limitations"></a>Ограничения
* Данные о количестве пользователей обновляются каждые 24–48 часов.  Данные об операциях аутентификации обновляются несколько раз в день.
* При использовании фильтра по ApplicationId возможно отображение пустого отчета из-за одной из следующих причин:
 * Идентификатор приложения не существует в клиенте. Убедитесь, что он указан правильно.
 * Идентификатор приложения существует, но не были найдены данные за отчетный период. Проверьте параметры даты и времени.


## <a name="next-steps"></a>Дальнейшие действия
### <a name="estimating-your-azure-ad-monthly-bill"></a>Вы можете оценить ежемесячный счет Azure AD.
Используя также [последние доступные цены на Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/), вы можете оценить ежедневное, еженедельное и ежемесячное потребление ресурсов Azure.  Оценка особенно полезна при планировании изменений в поведении клиента, которые могут повлиять на общую стоимость.  Фактические затраты можно просмотреть в [связанной подписке Azure](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing).

### <a name="options-for-other-output-formats"></a>Параметры для других форматов вывода
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    



<!--HONumber=Feb17_HO2-->


