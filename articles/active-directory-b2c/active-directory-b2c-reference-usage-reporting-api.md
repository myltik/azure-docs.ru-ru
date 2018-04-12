---
title: Azure Active Directory B2C. Примеры и определения для API отчетов об использовании | Документация Майкрософт
description: Руководство по получению отчетов по пользователям, операциям аутентификации и Многофакторной идентификации для клиента Azure AD B2C (с примерами).
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 07029181423927f0796cb85e728df416d01466e8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Доступ к отчетам об использовании в Azure AD B2C с помощью API отчетов

Azure Active Directory B2C (Azure AD B2C) предоставляет проверку подлинности на основе имени для входа и Многофакторной идентификации Azure. Аутентификация предоставляется для конечных пользователей всего семейства приложений у поставщиков удостоверений. Зная количество пользователей, зарегистрированных в клиенте, какие поставщики использовались для регистрации и сколько типов аутентификации используется, можно ответить на следующие вопросы:
* Сколько пользователей для каждого типа поставщика удостоверений (например, учетной записи Майкрософт или LinkedIn) зарегистрировано за последние 10 дней?
* Сколько успешных операций проверки подлинности с помощью Многофакторной идентификации было выполнено за последний месяц?
* Сколько успешных операций аутентификации на основе имени для входа было выполнено за последний месяц? В день? На приложение?
* Как можно оценить ожидаемые ежемесячные расходы на работу моего клиента Azure AD B2C?

Эта статья посвящена отчетам, связанным с выставлением счетов, которое основано на количестве пользователей, оплачиваемых операций аутентификации на основе имени для входа и операций Многофакторной идентификации.


## <a name="prerequisites"></a>предварительным требованиям
Прежде чем начать работу, выполните [предварительные требования для доступа к интерфейсам API отчетов Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Создайте приложение, получите его секрет и предоставьте ему права доступа к отчетам своего клиента Azure AD B2C. В статье также приведены примеры *сценария Bash* и *сценария Python*. 

## <a name="powershell-script"></a>Сценарий PowerShell
В этом сценарии показано создание четырех отчетов об использовании, в которых применяются параметр `TimeStamp` и фильтр по `ApplicationId`.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
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
* **tenantUserCount**: количество пользователей в клиенте по типу поставщика удостоверений в день за последние 30 дней. (При необходимости фильтр `TimeStamp` предоставляет количество пользователей с указанной даты до текущей даты). Данный отчет содержит:
  * **TotalUserCount**: число всех объектов-пользователей;
  * **OtherUserCount**: количество пользователей Azure Active Directory (не пользователей Azure AD B2C);
  * **LocalUserCount**: число учетных записей пользователей Azure AD B2C с локальными учетными данными в клиенте Azure AD B2C.

* **AlternateIdUserCount**: число пользователей Azure AD B2C, зарегистрированных с помощью внешних поставщиков удостоверений (например Facebook, учетной записи Майкрософт или другого клиента Azure Active Directory, который также называют `OrgId`).

* **b2cAuthenticationCountSummary**: сводка по количеству платных ежедневных операций аутентификации за последние 30 дней по дням и типу потока аутентификации.

* **b2cAuthenticationCount**: количество операций аутентификации за период времени. По умолчанию — за последние 30 дней.  (Необязательно. Начальные и завершающие параметры `TimeStamp` определяют конкретный период времени.) Выходные данные содержат `StartTimeStamp` (самую раннюю дату действия этого клиента) и `EndTimeStamp` (дату последнего обновления).

* **b2cMfaRequestCountSummary**: сводка по количеству ежедневных операций Многофакторной идентификации по дням и типу (с помощью SMS или голосовой связи).


## <a name="limitations"></a>Ограничения
Данные о количестве пользователей обновляются каждые 24–48 часов. Данные об операциях аутентификации обновляются несколько раз в день. При использовании фильтра по `ApplicationId` возможно отображение пустого отчета из-за одной из следующих причин:
  * Идентификатор приложения не существует в клиенте. Убедитесь, что он указан правильно.
  * Идентификатор приложения существует, но не были найдены данные за отчетный период. Проверьте параметры даты и времени.


## <a name="next-steps"></a>Дополнительная информация
### <a name="monthly-bill-estimates-for-azure-ad"></a>Оценка ежемесячных счетов Azure AD
Используя также [последние доступные цены на Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/), вы можете оценить ежедневное, еженедельное и ежемесячное потребление ресурсов Azure.  Оценка особенно полезна при планировании изменений в поведении клиента, которые могут повлиять на общую стоимость. Фактические затраты можно просмотреть в [связанной подписке Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Параметры для других форматов вывода
В следующем фрагменте кода показаны примеры отправки выходных данных в JSON, список значений имени и XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
