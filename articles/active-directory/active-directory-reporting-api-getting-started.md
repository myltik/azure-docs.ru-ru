<properties
   pageTitle="Приступая к работе с API отчетов Azure AD"
   description="Как начать работу с API отчетов Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/17/2015"
   ms.author="kenhoff;yossib"/>


# Приступая к работе с API отчетов Azure AD

Azure Active Directory предоставляет возможность получения отчетов об активности, аудиту и безопасности. Полученные данные можно использовать на портале Azure, но их также может быть полезно использовать во многих других приложениях, например в приложениях систем SIEM, инструментах аудита и бизнес-аналитики.

API отчетов Azure AD предоставляют программный доступ к этим данным через набор API на основе REST, которые можно вызвать, используя различные языки программирования и инструменты.

Эта статья поможет вам узнать о вызове API отчетов Azure AD с помощью PowerShell. Вы можете изменить образец сценария PowerShell, чтобы получать доступ к данным любых доступных JSON-, XML- или текстовых отчетов в соответствии с требованиями к сценарию.

Чтобы использовать этот пример, необходимо наличие [Azure Active Directory](active-directory-whatis.md)

## Создание приложения Azure AD для доступа к API

API отчетов использует [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) для авторизации доступа к веб-API. Чтобы получить доступ к информации, используя каталог, необходимо создать приложение в Active Directory и предоставить ему соответствующие разрешения на доступ к данным Azure Active Directory.


### Создание приложения
- Перейдите на [портал управления Azure](https://manage.windowsazure.com/).
- Перейдите в каталог.
- Перейдите к приложениям.
- На нижней панели щелкните «Добавить».
	- Щелкните «Добавить приложение, которое разрабатывает моя организация».
	- **Имя** — любое имя. Рекомендуем выбрать имя, похожее на «Приложения API отчетов».
	- **Тип** — выберите «Веб-приложение и/или веб-API».
	- Нажмите кнопку со стрелкой, чтобы перейти к следующей странице.
	- **URL-адрес входа** — ```http://localhost```.
	- **URI кода приложения** — ```http://localhost```.
	- Установите флажок, чтобы завершить добавление приложения.

### Предоставление приложению разрешения на использование API
- Откройте вкладку «Приложения».
- Перейдите к созданному приложению.
- Выберите вкладку **Настройка**.
- В разделе «Разрешения для других приложений»:
	- Щелкните «Microsoft Azure Active Directory» > «Разрешения приложения», а затем выберите **Чтение данных каталога**.
- На нижней панели щелкните **Сохранить**.


### Получение идентификатора каталога, идентификатора клиента и секрета клиента

Ниже описывается получение идентификатора клиента и секрета клиента приложения. Вам также будет необходимо знать имя клиента. Им может быть *.onmicrosoft.com или имя пользовательского домена. Скопируйте их в другое расположение. Они понадобятся для изменения сценария.

#### Идентификатор клиента приложения
- Откройте вкладку «Приложения».
- Перейдите к созданному приложению.
- Откройте вкладку **Настройка**.
- Идентификатор клиента приложения будет отображаться в поле **Идентификатор клиента**.

#### Секрет клиента приложения
- Откройте вкладку «Приложения».
- Перейдите к созданному приложению.
- Откройте вкладку «Настройка».
- Создайте новый секретный ключ для своего приложения, выбрав длину в разделе «Ключи».
- Ключ отобразится после сохранения. Убедитесь, что скопировали и вставили его в безопасном расположении, так как в дальнейшем не будет возможности его извлечь.


## Изменение сценария
Чтобы изменить указанный ниже сценарий PowerShell для работы со своим каталогом, замените $ClientID, $ClientSecret и $tenantdomain правильными значения из «Делегирование доступа в Azure AD».

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID      = "<<YOUR CLIENT ID HERE>>"                # Should be a ~35 character string insert your info here
    $ClientSecret  = "<<YOUR CLIENT SECRET HERE>>"          # Should be a ~44 character string insert your info here
    $loginURL      = "https://login.windows.net"
    $tenantdomain  = "<<YOUR TENANT NAME HERE>>"            # For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
        $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        # Returns a list of all the available reports
        Write-host List of available reports
        Write-host =========================
        $allReports = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports?api-version=beta")
        Write-host $allReports.Content

        Write-host
        Write-host Data from the AccountProvisioningEvents report
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "accountProvisioningEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/accountProvisioningEvents?api-version=beta")
        Write-host $myReport.Content

        Write-host
        Write-host Data from the AuditEvents report with datetime filter
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "auditEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt 2015-05-20")
        Write-host $myReport.Content

        # Options for other output formats

        # to output the JSON use following line
        $myReport.Content | Out-File -FilePath accountProvisioningEvents.json -Force

        # to output the content to a name value list
        ($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath accountProvisioningEvents.txt -Force

        # to output the content in XML use the following line
        (($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath accountProvisioningEvents.xml -Force

    } else {
        Write-Host "ERROR: No Access Token"
        }


## Выполнение сценария
Завершив редактирование сценария, запустите его и убедитесь, что он вернул ожидаемые данные из отчета AuditEvents.

Сценарий возвращает список всех доступных отчетов, а в окне PowerShell — выходные данные в отчете AccountProvisioningEvents в формате JSON. Он также создает файлы с такими же выходными JSON-, текстовыми и XML-данными. Эксперимент можно настроить, изменив его сценарий так, чтобы он возвращал данные из других отчетов, и закомментировав необходимые форматы выходных данных.


## Дальнейшие действия
- Интересуют доступные отчеты о безопасности, аудиту и действиях? См. [Отчеты Azure AD о безопасности, аудите и активности](active-directory-view-access-usage-reports.md)
- Дополнительную информацию об отчетах об аудите см. в статье [События отчетов AD Azure об аудите ](active-directory-reporting-audit-events.md)
- Дополнительную информацию о службе Graph API REST см. в разделе [Отчеты и события Azure AD (предварительная версия)](https://msdn.microsoft.com/library/azure/mt126081.aspx)

<!---HONumber=July15_HO4-->