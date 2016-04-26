<properties
   pageTitle="Приступая к работе с API отчетов Azure AD | Microsoft Azure"
   description="Как начать работу с API отчетов Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/07/2016"
   ms.author="dhanyahk"/>


# Приступая к работе с API отчетов Azure Active Directory

*Данная документация является частью [руководства по отчетам Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory \(AD\) предоставляет возможность получения отчетов об активности, аудите и безопасности. Полученные данные можно использовать на классическом портале Azure, но их также может быть полезно использовать во многих других приложениях, например в приложениях систем SIEM, инструментах аудита и бизнес-аналитики.

[API отчетов Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) предоставляют программный доступ к этим данным через набор API на основе REST, которые можно вызвать, используя различные языки программирования и инструменты.

Эта статья поможет вам узнать о вызове API отчетов Azure AD с помощью PowerShell. Вы можете изменить образец сценария PowerShell, чтобы получать доступ к данным любых доступных JSON-, XML- или текстовых отчетов в соответствии с требованиями к сценарию.

Для использования этого примера потребуется клиент [Azure Active Directory](active-directory-whatis.md).

## Создание приложения Azure AD для доступа к API

API отчетов использует [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) для авторизации доступа к веб-API. Чтобы получить доступ к информации, используя каталог, необходимо создать приложение в клиенте Azure AD и предоставить ему соответствующие разрешения на доступ к данным Azure AD.


### Создание приложения
- Перейдите на [классический портал Azure](https://manage.windowsazure.com/).
- Перейдите к своему клиенту Azure AD.
- Откройте вкладку **Приложения**.
- На нижней панели щелкните **Добавить**.
	- Щелкните "Добавить приложение, разрабатываемое моей организацией".
	- **Имя** — любое имя. Рекомендуем выбрать имя, похожее на «Приложения API отчетов».
	- **Тип** — выберите «Веб-приложение и/или веб-API».
	- Нажмите кнопку со стрелкой, чтобы перейти к следующей странице.
	- **URL-адрес входа** — ```http://localhost```.
	- **URI кода приложения** — ```http://localhost```.
	- Установите флажок, чтобы завершить добавление приложения.

### Предоставление приложению разрешения на использование API
- Откройте вкладку **Приложения**.
- Перейдите к созданному приложению.
- Выберите вкладку **Настройка**.
- В разделе «Разрешения для других приложений»:
	- Щелкните "Azure Active Directory" \> "Разрешения приложения", а затем выберите **Чтение данных каталога**.
- На нижней панели щелкните **Сохранить**.


### Получение идентификатора каталога, идентификатора клиента и секрета клиента

Ниже описывается получение идентификатора клиента и секрета клиента приложения. Вам также будет необходимо знать имя клиента. Им может быть \*.onmicrosoft.com или имя пользовательского домена. Скопируйте их отдельно, так как они понадобятся для изменения сценария.

#### Идентификатор клиента приложения
- Откройте вкладку **Приложения**.
- Перейдите к созданному приложению.
- Откройте вкладку **Настройка**.
- Идентификатор клиента приложения будет отображаться в поле **Идентификатор клиента**.

#### Секрет клиента приложения
- Откройте вкладку **Приложения**.
- Перейдите к созданному приложению.
- Откройте вкладку **Настройка**.
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

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&\`$filter=eventTime gt $7daysago"

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

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Выполнение сценария
Завершив редактирование сценария, запустите его и убедитесь, что он вернул ожидаемые данные из отчета AuditEvents.

Сценарий выводит список всех доступных отчетов, а в окне PowerShell отображает выходные данные из отчета AccountProvisioningEvents в формате JSON. Он также создает файлы с такими же выходными JSON-, текстовыми и XML-данными. Вы можете поэкспериментировать, изменив сценарий так, чтобы он возвращал данные из других отчетов, и закомментировав ненужные форматы выходных данных.

## Примечания

- Количество событий, возвращаемых API отчетов Azure AD \(с помощью разбиения на страницы OData\), не ограничено.
- Сведения о периоде удержания данных отчетов приведены в статье [Политики периода удержания отчетов](active-directory-reporting-retention.md).


## Дальнейшие действия
- Интересуют доступные отчеты о безопасности, аудите и активности? См. [Отчеты Azure AD о безопасности, аудите и активности](active-directory-view-access-usage-reports.md)
- Дополнительную информацию об отчетах об аудите см. в статье [События отчетов AD Azure об аудите ](active-directory-reporting-audit-events.md)
- Дополнительную информацию о службе REST API Graph для Azure AD см. в разделе [Reports and events \(preview\) \| Graph API concepts](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)

<!---HONumber=AcomDC_0413_2016-->