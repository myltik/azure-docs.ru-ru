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
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# Приступая к работе с API отчетов Azure AD

*Данная документация является частью [руководства по отчетам Azure Active Directory](active-directory-reporting-guide.md).*

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

Ниже описывается получение идентификатора клиента и секрета клиента приложения. Вам также будет необходимо знать имя клиента. Им может быть *.onmicrosoft.com или имя пользовательского домена. Скопируйте их отдельно, так как они понадобятся для изменения сценария.

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
Чтобы изменить один из указанных ниже сценариев для работы со своим каталогом, замените $ClientID, $ClientSecret и $tenantdomain правильными значениями из «Делегирование доступа в Azure AD».

### Сценарий PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

### Сценарий Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"





## Выполнение сценария
Завершив редактирование сценария, запустите его и убедитесь, что он вернул ожидаемые данные из отчета AuditEvents.

Сценарий возвращает список всех доступных отчетов, а в окне PowerShell — выходные данные в отчете AccountProvisioningEvents в формате JSON. Он также создает файлы с такими же выходными JSON-, текстовыми и XML-данными. Эксперимент можно настроить, изменив его сценарий так, чтобы он возвращал данные из других отчетов, и закомментировав необходимые форматы выходных данных.

## Примечания

- Количество событий, возвращаемых API отчетов Azure AD (с помощью разбиения на страницы OData), не ограничено.
	- Сведения об ограничениях хранения данных отчетов приведены в статье [Политики хранения отчетов](active-directory-reporting-retention.md).


## Дальнейшие действия
- Интересуют доступные отчеты о безопасности, аудиту и действиях? См. [Отчеты Azure AD о безопасности, аудите и активности](active-directory-view-access-usage-reports.md)
- Дополнительную информацию об отчетах об аудите см. в статье [События отчетов AD Azure об аудите ](active-directory-reporting-audit-events.md)
- Дополнительную информацию о службе Graph API REST см. в разделе [Отчеты и события Azure AD (предварительная версия)](https://msdn.microsoft.com/library/azure/mt126081.aspx)

<!---HONumber=AcomDC_1217_2015-->