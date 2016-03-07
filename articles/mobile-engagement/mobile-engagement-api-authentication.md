<properties 
	pageTitle="Приступая к работе с API проверки подлинности Azure Mobile Engagement"
	description="Это руководство по миграции, предназначенное для клиентов, которые использовали проверку подлинности API Capptain, а теперь будут применять для этого новые API Enagagement Azure Mobile." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/17/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement. Использование API для проверки подлинности

## Обзор

В этом документе содержатся более подробные сведения о том, как запустить механизм аутентификации для новых API.

Предполагается, что у вас есть действующая подписка Azure и вы создали приложение Mobile Engagement, используя одно из наших [руководств для разработчиков](mobile-engagement-windows-store-dotnet-get-started.md).

## Аутентификация

Для аутентификации необходимо использовать маркер OAuth на основе Microsoft Azure Active Directory.

Чтобы выполнить проверку подлинности запроса API, в него нужно добавить заголовок авторизации. У него должен быть следующий формат:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Срок действия токенов Azure Active Directory — 1 час.

Существует несколько способов получить токен. Так как вызовы API обычно осуществляются из облачной службы, возможно, понадобится использовать ключ API. Ключ API в Azure — это пароль субъекта-службы. Следующая процедура описывает один из способов его ручной настройки.

> [AZURE.WARNING] Ключ, отображаемый в Azure Active Directory, — это НЕ ключ API Mobile Engagement, показанный на портале. Метод с использованием ключа API Mobile Engagement является устаревшим, поэтому вместо него здесь описана проверка подлинности AAD.

#### Однократная настройка (вручную)

Выполняя эту настройку, запишите сведения ниже, так как они понадобятся позже.
	
1. Создайте приложение Azure Active Directory, следуя инструкциям в [этом руководстве](../resource-group-create-service-principal-portal.md).

	- В этом документе в качестве имени приложения, которое вы выберете, будет использоваться `{AD_APP_NAME}`.
	- В качестве идентификатора клиента, который отображается в меню настройки, в этом документе используется `{CLIENT_ID}`.
	- Ключ отображается только раз после сохранения, и в этом документе это `{CLIENT_SECRET}`.
	- Нажмите кнопку **Просмотр конечных точек** на нижней панели, скопируйте адрес в строке **URL-адрес конечной точки токена Oauth 2.0** (в этом документе это `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`). <br/>                                    
2. Назначьте роль субъекта-службы, например Reader или Owner, с помощью [интерфейса командной строки Azure](../xplat-cli-install.md).

	Если вы используете ОС Windows, измените переменную среды `PATH`, добавив `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin`, чтобы активировать возможность использования команд Azure.

	Выполните следующие команды, чтобы настроить учетную запись с помощью интерфейса командной строки Azure:

		azure config mode arm 
		azure login

	Затем используйте такую команду, чтобы найти идентификатор объекта приложения:

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	Запишите значение `Object Id`, указанное в выходных данных.

	Затем назначьте роль `Owner` субъекту-службе, выполнив следующую команду:
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### Однократная настройка (с использованием сценария)

Это другой способ выполнить уже описанные действия, но с использованием сценария PowerShell.

1. Установите последнюю версию Azure PowerShell.

	Сейчас доступна версия 1.2.1, которую можно скачать [здесь](https://github.com/Azure/azure-powershell/releases/tag/v1.2.1-February2016).

2. Откройте Windows PowerShell в режиме администратора и убедитесь, что вы установили [командлеты диспетчера ресурсов Azure](https://msdn.microsoft.com/library/mt125356.aspx).

		Install-Module AzureRM
		Install-AzureRM

3. Выполните следующую команду:

		Import-Module AzureRM.profile

4. Выполните следующую команду, чтобы открыть диалоговое окно входа. После входа команда отобразит активную подписку, т. е. ту, по отношению к которой будут выполняться все команды.

		Add-AzureRmAccount

5. Выполните следующую команду, чтобы просмотреть список всех подписок. Скопируйте GUID подписки, которую нужно использовать.

		Get-AzureRmSubscription

6. Выполните следующую команду и укажите GUID подписки, чтобы настроить использование подписки. Это пригодится при наличии нескольких подписок.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. Скопируйте текст сценария [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) на локальный компьютер и запустите сценарий.

	>[Azure.Note] Политика безопасности по умолчанию может блокировать выполнение сценариев PowerShell. В таком случае используйте следующую команду, чтобы настроить временную политику и обеспечить возможность выполнить сценарий:

	>	Set-ExecutionPolicy RemoteSigned

	В сценарии отобразится запрос на ввод имени, которое нужно назначить субъекту-службе. Можно указать любое имя.

	По завершении сценария отобразятся четыре значения, которые нужны для программной проверки подлинности с использованием AD: **TenantId**, **SubscriptionId**, **ApplicationId** и **Secret**.

	Скопируйте эти значения для справки. Чтобы получить маркер доступа сейчас, следует использовать в качестве значения TenantId `{TENANT_ID}`, в качестве значения ApplicationId — `{CLIENT_ID}`, а в качестве значения Secret — `{CLIENT_SECRET}`.

8. На портале управления Azure убедитесь, что новое приложение AD отображается в разделе **Приложения, которыми владеет моя компания**.

#### Получение допустимого маркера

Чтобы получить новый маркер, вызовите такой API:

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

Вот пример запроса:

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

Вот пример ответа на запрос:

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

В этом примере предусмотрено кодирование URL-адресов параметров POST, поэтому на самом деле значение `resource` — `https://management.core.windows.net/`. Следует также закодировать URL-адрес параметра `{CLIENT_SECRET}`, так как в нем могут содержаться специальные символы.

Теперь при каждом вызове API добавляйте в запрос заголовок авторизации:

	Authorization: Bearer {ACCESS_TOKEN}

Если вернется код состояния 401, проверьте текст ответа. Возможно, истек срок действия маркера. В этом случае следует получить новый маркер.

##Использование API

Теперь, когда есть допустимый маркер, можно вызывать API.

1. С каждым запросом API необходимо передавать допустимый действующий маркер, полученный в результате выполнения действий, которые описаны в предыдущем разделе.

2. В универсальном коде ресурса (URI) запроса нужно добавить несколько параметров для идентификации вашего приложения. URI будет выглядеть следующим образом:

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Чтобы получить параметры, щелкните имя приложения, а затем — "Панель мониторинга", и вы увидите страницу с такими тремя параметрами:

	- **1** `{subscription-id}`;
	- **2** `{app-collection}`;
	- **3** `{app-resource-name}`.

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/> 1. Игнорируйте параметр корневого адреса API, как и ранее.<br/> 2. Используемое имя ресурса приложения должно отличаться от имени самого приложения.

<!---HONumber=AcomDC_0224_2016-->