<properties
    pageTitle="Примеры для API отчета о действиях при входе Azure Active Directory | Microsoft Azure"
    description="Как начать работу с API отчетов Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Примеры для API отчета о действиях при входе Azure Active Directory

Эта статья входит в серию статей об API отчетов Azure Active Directory. Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным о действиях при входе, используя код или связанные инструменты. Цель этой статьи — предоставить пример кода для **API действий при входе**.

См.:

- Основные сведения см. в разделе [Журналы аудита](active-directory-reporting-azure-portal.md#audit-logs).
- Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).


## Предварительные требования
Перед использованием примеров в этой статье выполните [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md).


##Сценарий PowerShell

	# This script will require the Web Application and permissions setup in Azure Active Directory
	$ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
	$ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
	$loginURL       = "https://login.windows.net/"
	$tenantdomain   = "<tenantDomain>"
	$ daterange            # For example, contoso.onmicrosoft.com

	$7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
	# or, AddMinutes(-5)

	Write-Output $7daysago

	# Get an Oauth 2 access token based on client id, secret and tenant domain
	$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

	$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

	if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
	
	$i=0
  	
	Do{
		Write-Output "Fetching data using Uri: $url"
		$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
		Write-Output "---------------------------------------------"
		$myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
		$url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
		$i = $i+1
	} while($url -ne $null)

	} else {
    
		Write-Host "ERROR: No Access Token"
	}




## Выполнение сценария
Завершив редактирование сценария, запустите его и убедитесь, что он вернул ожидаемые данные из отчета о журналах аудита.

Сценарий возвращает выходные данные из отчета о входе в формате JSON. Он также создает файл `SigninActivities.json` с такими же выходными данными. Вы можете поэкспериментировать, изменив сценарий так, чтобы он возвращал данные из других отчетов, и закомментировав ненужные форматы выходных данных.



## Дальнейшие действия

- Хотите настроить примеры в этой статье? Просмотрите [API отчета о действиях при входе Azure Active Directory](active-directory-reporting-api-sign-in-activity-reference.md).

- Полный обзор использования API отчетов Azure Active Directory см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

- Дополнительные сведения об отчетах Azure Active Directory см. в статье [Руководство по отчетам Azure Active Directory](active-directory-reporting-guide.md).

<!---HONumber=AcomDC_0928_2016-->