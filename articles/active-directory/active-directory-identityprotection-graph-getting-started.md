.<properties
	pageTitle="Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph | Microsoft Azure"
	description="Общие сведения о выполнении запросов с помощью Microsoft Graph для получения списка рисковых событий и связанных сведений из Azure Active Directory."
	services="active-directory"
	keywords="защита идентификации azure active directory, события риска, уязвимость, политика безопасности, Microsoft Graph"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="markvi"/>

# Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph

Microsoft Graph — это конечная точка единого Microsoft API и источник API-интерфейсов для [защиты идентификации Azure Active Directory](active-directory-identityprotection.md). Наш первый API, **identityRiskEvents**, позволяет запрашивать у Microsoft Graph список [рисковых событий](active-directory-identityprotection-risk-events-types.md) и связанных с ними сведений. В статье описывается, как выполнять запросы к этому API. Дополнительные сведения, полная документация и доступ к Graph Explorer представлены на [веб-сайте Microsoft Graph](https://graph.microsoft.io/).

Получить доступ к данным защиты идентификации с помощью Microsoft Graph можно в три этапа.

1. Добавление приложения с секретом клиента.

2. Использование этого секрета и других данных для прохождения проверки подлинности в Microsoft Graph и получения маркера проверки подлинности.

3. Использование полученного маркера для отправки запросов к конечной точке API и получения данных защиты идентификации.

Перед началом работы вам потребуются:

- права администратора для создания приложения в Azure AD;
- имя домена клиента (например, contoso.onmicrosoft.com).



## Добавление приложения с секретом клиента


1. [Войдите](https://manage.windowsazure.com) на классический портал Azure, используя учетную запись администратора.

1. В области навигации слева щелкните **Active Directory**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. В меню вверху щелкните **Приложения**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. В нижней части страницы нажмите кнопку **Добавить**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. В диалоговом окне **Что необходимо сделать** щелкните **Добавить приложение, разрабатываемое моей организацией**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. В диалоговом окне **Расскажите о своем приложении** выполните следующие действия.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

	а. В текстовое поле **Имя** введите имя приложения (например, приложение API рискового события AADIP).

	b. В качестве **типа** выберите **Веб-приложение и/или веб-API**.

	c. Нажмите кнопку **Далее**.


5. В диалоговом окне **Свойства приложения** выполните следующие действия:

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

	а. В текстовое поле **URL-адрес входа** введите `http://localhost`.

	b. В текстовое поле **URI кода приложения** введите `http://localhost`.

	c. Нажмите **Завершено**.


Теперь вы можете настроить приложение.

![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## Предоставление приложению разрешения на использование API


1. На странице приложения в меню вверху щелкните **Настройка**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. В разделе **Разрешения для других приложений** щелкните **Добавить приложение**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. В диалоговом окне **Разрешения для других приложений** выполните следующие действия:

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

	а. Выберите **Microsoft Graph**.

	b. Нажмите **Завершено**.


1. Щелкните **Разрешения приложения: 0**, а затем выберите **Read all identity risk event information** (Прочитать все сведения о событиях риска идентификации).

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. В нижней части страницы нажмите кнопку **Сохранить**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## Получение ключа доступа

1. На странице приложения в разделе **ключей** выберите для продолжительности значение в 1 год.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. В нижней части страницы нажмите кнопку **Сохранить**.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. В разделе ключей скопируйте значение созданного ключа и сохраните его в надежном месте.

	![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

	> [AZURE.NOTE] Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.


1. В разделе **свойств** скопируйте **идентификатор клиента** и сохраните его в надежном месте.


## Проверка подлинности в Microsoft Graph и выполнение запросов с помощью API рисковых событий идентификации

На этом этапе вам понадобятся:

- скопированный ранее идентификатор клиента;

- скопированный ранее ключ;

- имя домена клиента.


Чтобы выполнить проверку подлинности, отправьте запрос POST по адресу `https://login.microsoft.com` со следующими параметрами в тексте:

- grant\_type: “**client\_credentials**”

- resource: “**https://graph.microsoft.com**”

- client\_id: <идентификатор\_вашего\_клиента>

- client\_secret: <ваш\_ключ>


> [AZURE.NOTE] Необходимо указать значения для параметров **client\_id** и **client\_secret**.

В случае успешного выполнения этот метод возвращает маркер проверки подлинности. Для вызова API создайте заголовок со следующим параметром:

	`Authorization`=”<token_type> <access_token>"


При проверке подлинности тип маркера и маркер доступа можно найти в возвращаемом маркере.

Отправьте этот заголовок как запрос по следующему URL-адресу API: `https://graph.microsoft.com/beta/identityRiskEvents`

В случае успеха в ответ вы получите коллекцию рисковых событий идентификации и связанных данных в формате OData JSON. Эти данные можно проанализировать и обработать по своему усмотрению.

Ниже приведен образец кода для проверки подлинности и вызова API с помощью Powershell. Просто добавьте идентификатор клиента, ключ и домен клиента.

	$ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
	$ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
	$tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

	$loginURL       = "https://login.microsoft.com"
	$resource       = "https://graph.microsoft.com"

	$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
	$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

	Write-Output $oauth

	if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    	$url = "https://graph.microsoft.com/beta/identityRiskEvents"
    	Write-Output $url

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
        	Write-Output $event
    	}

	} else {
    	Write-Host "ERROR: No Access Token"
	} 


## Дальнейшие действия

Поздравляем, вы только что выполнили первый вызов Microsoft Graph. Теперь вы можете запрашивать рисковые события идентификации и использовать данные по своему усмотрению.

Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](https://graph.microsoft.io/docs), а также на [веб-сайте Microsoft Graph](https://graph.microsoft.io/). Кроме того, сохраните в закладки страницу, посвященную [API защиты идентификации Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root). Она содержит список всех доступных в Graph API-интерфейсов защиты идентификации. Мы будем добавлять на эту страницу новые способы работы с защитой идентификации через API, поэтому следите за новостями.


## Дополнительные ресурсы

- [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)

- [Типы событий риска, обнаруживаемые защитой идентификации Azure Active Directory](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

<!---HONumber=AcomDC_0824_2016-->