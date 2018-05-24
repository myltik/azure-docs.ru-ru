---
title: Azure Active Directory B2C. Примеры и определения для журналов аудита | Документы Майкрософт
description: Руководство и примеры по обращению к журналам аудита Azure AD B2C
services: active-directory-b2c
author: sromeroz
manager: sasubram
ms.author: sezambra
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.openlocfilehash: 8cc48853b0677230c4e19df4f0ecd93ce88b119d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206451"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Обращение к журналам аудита Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) создает журналы аудита, содержащие сведения о действиях для ресурсов B2C, выданных токенах и доступе с правами администратора. Эта статья содержит краткий обзор данных, предоставляемых через журналы аудита, и инструкции о том, как получить доступ к этим данным для вашего клиента Azure AD B2C.

> [!IMPORTANT]
> Журналы аудита хранятся всего семь дней. Если требуется более длительный срок хранения, спланируйте скачивание и хранение журналов с помощью одного из описанных ниже методов. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Общие сведения о действиях, доступных в категории B2C для журналов аудита
Категория **B2C** в журналах аудита содержит следующие типы действий:
|тип действия; |ОПИСАНИЕ  |
|---------|---------|
|Авторизация |Действия, касающиеся авторизации пользователя для доступа к ресурсам B2C (например, доступ администратора к списку политик B2C)         |
|Каталог |Действия, связанные с атрибутами каталога, полученными при входе администратора через портал Azure |
|Приложение | Операции CRUD для приложений B2C |
|Ключ |Операции CRUD для ключей, хранящихся в контейнере ключей B2C |
|Ресурс |Операции CRUD для ресурсов B2C (например, политик и поставщиков удостоверений)
|Authentication |Проверка учетных данных пользователя и выдача токенов|

> [!NOTE]
> Сведения о действиях CRUD для объектов пользователя см. в категории **основного каталога**.

##<a name="example-activity"></a>Пример действия
Приведенный ниже пример показывает данные, полученные при входе пользователя с помощью внешнего поставщика удостоверений: ![Журналы аудита — пример](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Доступ к журналам аудита через портал Azure
1. Перейдите на [портал Azure](https://portal.azure.com). Перейдите в каталог B2C.
2. Щелкните **Azure Active Directory** на панели "Избранное" слева. 
    
    ![Журналы аудита — кнопка "Azure Active Directory"](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. В разделе **Действия** щелкните **Журналы аудита**

    ![Журналы аудита — раздел журналов](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. В раскрывающемся списке **Категория** выберите **B2C**.
3. Нажмите кнопку **Применить**.

    ![Журналы аудита — категория](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Вы увидите список действий, внесенных в журнал за последние семь дней. 
- Используйте раскрывающийся список **Тип ресурса действия** для фильтрации указанных выше типов действий.
- Используйте раскрывающийся список **Диапазон дат**, чтобы отфильтровать диапазон дат для указанных действий.
- Если щелкнуть определенную строку в списке, справа отображается контекстно-зависимое окно с дополнительными атрибутами, связанными с этим действием.
- Нажмите кнопку **Скачать**, чтобы скачать действия в виде CSV-файла.

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Доступ к журналам аудита через API отчетов Azure AD
Журналы аудита публикуются в том же конвейере, что и другие действия для Azure Active Directory, поэтому к ним можно обратиться через [API отчетов Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference). 

###<a name="prerequisites"></a>предварительным требованиям
Чтобы проверить подлинность в API отчетов Azure AD, нужно сначала зарегистрировать приложение. Выполните действия из раздела [Предварительные требования для доступа к интерфейсам API отчетов Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

###<a name="accesing-the-api"></a>Доступ к API
Чтобы скачать журналы аудита Azure AD B2C через API, нужно отфильтровать журналы категории **B2C**. Для фильтрации по категории используйте параметр строки запроса при вызове конечной точки API отчетов Azure AD, как показано ниже:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>Сценарий PowerShell
Следующий сценарий показывает пример использования PowerShell для запроса API отчетов Azure AD и сохранения результатов в файле JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"     
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {   
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago 

    # loop through each query page (1 through n)
    Do{
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

