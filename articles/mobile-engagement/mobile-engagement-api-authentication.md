<properties 
	pageTitle="Аутентификация с помощью интерфейсов REST API Mobile Engagement"
	description="Описывается аутентификация с помощью интерфейсов REST API Azure Mobile Engagement." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="03/11/2016"
	ms.author="wesmc"/>

# Аутентификация с помощью интерфейсов REST API Mobile Engagement

## Обзор

В этом документе описывается, как получить допустимый маркер Oauth AAD для проверки подлинности с помощью интерфейсов REST API Mobile Engagement.

Предполагается, что у вас есть действующая подписка Azure и вы создали приложение Mobile Engagement, используя одно из наших [руководств для разработчиков](mobile-engagement-windows-store-dotnet-get-started.md).

## Аутентификация

Для проверки подлинности необходимо использовать маркер OAuth на основе Microsoft Azure Active Directory.

Чтобы выполнить проверку подлинности запроса API, в него нужно добавить заголовок авторизации, который имеет следующий вид:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Срок действия токенов Azure Active Directory — 1 час.

Существует несколько способов получить токен. Так как вызовы API обычно осуществляются из облачной службы, возможно, понадобится использовать ключ API. Ключ API в Azure — это пароль субъекта-службы. Следующая процедура описывает один из способов его ручной настройки.

### Однократная настройка (с использованием сценария)

Следуйте приведенным ниже инструкциям, чтобы выполнить установку с помощью сценария PowerShell, для реализации которого требуется минимальное время и использование наиболее допустимых значений по умолчанию. При необходимости также можно выполнить указания по [установке вручную](mobile-engagement-api-authentication-manual.md) непосредственно на портале Azure и осуществить более точную настройку.

1. Получите последнюю версию Azure PowerShell [здесь](http://aka.ms/webpi-azps). Дополнительные сведения об инструкциях по скачиванию см. по этой [ссылке](../powershell-install-configure.md).  

2. После установки Azure PowerShell используйте следующие команды, чтобы проверить наличие установленного **модуля Azure**:

    а. Убедитесь, что модуль Azure PowerShell находится в списке доступных модулей.
    
		Get-Module –ListAvailable 

	![Доступные модули Azure][1]
    	
    b. Если модуль Azure PowerShell отсутствует в приведенном выше списке, выполните следующую команду:
    	
		Import-Module Azure 
    	
3. Войдите в диспетчер ресурсов из Azure PowerShell, выполнив следующую команду и указав имя пользователя и пароль для учетной записи Azure:
    	
		Login-AzureRmAccount

4. Если у вас несколько подписок, следует выполнить следующую команду:

	а. Получите список всех подписок и скопируйте SubscriptionId нужной подписки. Убедитесь, что эта подписка используется с приложением Mobile Engagement, с которым вы будете работать с помощью API.

		Get-AzureRmSubscription

	b. Выполните указанную далее команду и укажите SubscriptionId подписки, чтобы настроить использование подписки.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Скопируйте текст сценария [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) на локальный компьютер, сохраните его как командлет PowerShell (например `APIAuth.ps1`) и выполните его `.\APIAuth.ps1`.
	
6. Сценарий запросит ввести значение для **principalName**. Укажите подходящее имя, которое будет использоваться для создания приложения Active Directory (например APIAuth).

7. По завершении сценария отобразятся четыре значения, которые нужны для программной проверки подлинности с использованием AD. Скопируйте их.
		
	**TenantId**, **SubscriptionId**, **ApplicationId** и **Secret**.

	Вы будете использовать TenantId в качестве `{TENANT_ID}`, ApplicationId — в качестве `{CLIENT_ID}`, а Secret — в качестве — `{CLIENT_SECRET}`.

	> [AZURE.NOTE] Политика безопасности по умолчанию может блокировать выполнение сценариев PowerShell. В таком случае используйте следующую команду, чтобы настроить временную политику и обеспечить возможность выполнить сценарий:

    	> Set-ExecutionPolicy RemoteSigned

8. Вот как выглядит набор командлетов PS.

	![][3]

9. На портале управления Azure убедитесь, что в разделе **Приложения, которыми владеет моя компания** отображается новое приложение AD с именем, переданным в сценарий **principalName**.

	![][4]

#### Получение допустимого маркера

1. Вызовите API со следующими параметрами и замените TENANT\_ID, CLIENT\_ID и CLIENT\_SECRET:

	- **URL-адрес запроса** как **https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
- **Заголовок Content-Type HTTP** как *application/x-www-form-urlencoded*
	- **Текст запроса HTTP** как *grant\_type=client\_credentials&client\_id={CLIENT\_ID}&client\_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

	Вот пример запроса:

		POST /{TENANT_ID}/oauth2/token HTTP/1.1
		Host: login.microsoftonline.com
		Content-Type: application/x-www-form-urlencoded
		grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
		urce=https%3A%2F%2Fmanagement.core.windows.net%2F

	Вот пример ответа на запрос:

		HTTP/1.1 200 OK
		Content-Type: application/json; charset=utf-8
		Content-Length: 1234
	
		{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
		5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

	В этом примере предусмотрено кодирование параметров POST в URL-адреса, поэтому на самом деле значение `resource` — `https://management.core.windows.net/`. Следует также закодировать параметр `{CLIENT_SECRET}` в URL-адрес, так как в нем могут содержаться специальные знаки.

	> [AZURE.NOTE] Для тестирования можно использовать средство клиента HTTP, такое как [Fiddler](http://www.telerik.com/fiddler) или [расширение Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop).

2. Теперь при каждом вызове API добавляйте в запрос заголовок авторизации:

		Authorization: Bearer {ACCESS_TOKEN}

	Если вернется код состояния 401, проверьте текст ответа. Возможно, истек срок действия маркера. В этом случае следует получить новый маркер.

##Использование API

Теперь, когда есть допустимый маркер, можно вызывать API.

1. С каждым запросом API необходимо передавать допустимый действующий маркер, полученный в результате выполнения действий, которые описаны в предыдущем разделе.

2. В универсальном коде ресурса (URI) запроса нужно добавить несколько параметров для идентификации вашего приложения. URI будет выглядеть следующим образом:

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Чтобы получить параметры, щелкните имя приложения, а затем — "Панель мониторинга", и вы увидите страницу с такими тремя параметрами:

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`
	- **4** Ваша группа ресурсов будет носить имя **MobileEngagement** до тех пор, пока не будет создана новая группа. 

	![Параметры URI API Mobile Engagement][2]

>[AZURE.NOTE] <br/>
>1. Игнорируйте параметр корневого адреса API, так как он предназначался для предыдущих API.<br/>
>2. Если вы создали приложение с помощью классического портала Azure, используемое имя ресурса приложения должно отличаться от имени самого приложения. Если вы создали приложение на портале Azure, используйте имя самого приложения (для приложений, создаваемых на новом портале, никакой разницы между именем ресурса приложения и именем приложения нет).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png

<!---HONumber=AcomDC_0518_2016-->