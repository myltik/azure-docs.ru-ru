<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: использование API Graph | Microsoft Azure"
	description="Сведения о том, как вызвать API Graph для клиента B2C, используя удостоверение приложения для автоматизации процесса."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="dastrock"/>

# Предварительная версия Azure AD B2C: использование API Graph

Как правило, клиенты Azure AD B2C (Azure AD) отличаются очень большими размерами. Это значит, что многие распространенные задачи управления клиентами необходимо решать программным путем. Основным примером может служить управление пользователями. Возможно, вам потребуется перенести существующее хранилище пользователя в клиент B2C. Кроме того, может потребоваться разместить регистрацию пользователей на своей странице и создавать учетные записи пользователей Azure AD в фоновом режиме. Для выполнения задач такого типа требуется возможность создавать, читать, обновлять и удалять учетные записи пользователей. Такую возможность предоставляет API Graph Azure AD.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Для клиентов B2C используются в основном два режима обмена данными с API Graph.

- Если речь идет об интерактивных разовых задачах, то их следует выполнять под учетной записью администратора клиента B2C. Для этого прежде чем выполнять вызовы в API Graph, администратор должен войти в систему со своими учетными данными.
- Для выполнения автоматизированных непрерывно выполняемых задач управления следует использовать тип учетной записи службы, которой предоставлены необходимые привилегии. В Azure AD это можно сделать путем регистрации приложения и выполнения проверки подлинности. Для этого нужен **идентификатор приложения**, который использует [предоставление учетных данных клиента OAuth 2.0](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). В этом случае приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя.

В этой статье мы рассмотрим автоматизированный вариант использования. Для демонстрации действий мы создадим в .NET 4.5 `B2CGraphClient`, который выполняет операции создания, чтения, обновления и удаления пользователя (CRUD). В клиенте предусмотрен интерфейс командной строки Windows, позволяющий вызывать различные методы. При этом код записывается как для неинтерактивной автоматизированной задачи.

## Получение клиента Azure AD B2C

Для создания приложения или пользователей, а также для работы с Azure AD в целом требуется клиент с поддержкой B2C и учетная запись глобального администратора для этого клиента. Если у вас нет клиента, создайте его, следуя инструкциям в статье [Предварительная версия Azure Active Directory B2C: создание клиента Azure AD B2C](active-directory-b2c-get-started.md).

## Регистрация приложения службы в клиенте

Теперь, когда у вас есть клиент B2C, необходимо создать приложение-службу с помощью командлетов Azure AD PowerShell. Для начала скачайте и установите [помощник по входу в Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Затем скачайте и установите [64-разрядный модуль Azure Active Directory для Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Чтобы использовать API Graph с вашим клиентом B2C, потребуется зарегистрировать выделенное приложение с помощью PowerShell. Следуйте указанным ниже инструкциям. Вам не удастся повторно использовать уже существующие приложения B2C, зарегистрированные на портале Azure. Это ограничение предварительной версии Azure AD B2C. Мы удалим его в ближайшем будущем. После этого мы соответствующим образом обновим данную статью.

Установив модуль Powershell, откройте Powershell и подключитесь к клиенту B2C. После запуска команды `Get-Credential` отобразится запрос на ввод имени пользователя и пароля. Введите имя пользователя и пароль учетной записи администратора клиента B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Прежде чем создавать приложения, необходимо создать **секрет клиента**. Приложение будет использовать секрет клиента для проверки подлинности в Azure AD и получения маркеров доступа. Допустимый секрет можно создать в Powershell.

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

Последняя команда позволяет распечатать созданный секрет клиента. Скопируйте его в безопасное место. Он понадобится вам позднее. Теперь можно создать приложение, указав в учетных данных новый секрет клиента.

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Если приложение будет создано, этот код выведет на печать некоторые свойства приложения, которые указаны выше. Копируйте также значения параметров `ObjectId` и `AppPrincipalId`.

Теперь, когда вы создали приложение в клиенте B2C, назначьте ему разрешения, необходимые для выполнения операций CRUD. Приложению нужно назначить три различные роли: модули чтения каталога (для чтения пользователей), модули записи каталога (для создания и обновления пользователей) и администратор учетных записей пользователей (для удаления пользователей). Идентификаторы этих ролей известны, поэтому можно выполнить указанные ниже команды, заменив параметр `-RoleMemberObjectId` параметром `ObjectId` из представленного выше кода. Чтобы отобразить список всех ролей каталога, выполните команду `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Теперь у вас есть приложение с разрешением на создание, чтение, обновление и удаление пользователей из клиента B2C.

## Скачивание, настройка и создание примера кода

Сначала скачайте и запустите пример кода. После этого мы сможем рассмотреть его подробнее. Вы можете [скачать пример кода в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Его можно клонировать в необходимый каталог.

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Откройте решение Visual Studio `B2CGraphClient\B2CGraphClient.sln` в Visual Studio. В проекте `B2CGraphClient` откройте файл `App.config`. Замените три параметра приложения собственными значениями.

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Затем щелкните решение `B2CGraphClient` правой кнопкой мыши и еще раз создайте пример кода. Если вам удастся выполнить это действие, в каталоге `B2CGraphClient\bin\Debug` появится исполняемый файл `B2C.exe`.

## Создание операций CRUD пользователя с помощью API Graph

Чтобы использовать B2CGraphClient, откройте командную строку Windows (`cmd`) и измените каталог на `Debug`. Затем выполните команду `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Появится краткое описание каждой команды. Каждый раз при вызове одной из этих команд `B2CGraphClient` создает запрос к службе API Graph Azure AD.

### Получение маркера доступа

При любом запросе к API Graph требуется маркер доступа для проверки подлинности. Для получения маркеров доступа `B2CGraphClient` использует библиотеку проверки подлинности Active Directory (ADAL) с открытым исходным кодом. ADAL упрощает получение маркеров, так как предоставляет простой API и выполняет некоторые важные действия, такие как кэширование маркеров доступа. Использовать ADAL для получения маркеров необязательно. Их можно получать, составляя HTTP-запросы вручную.

> [AZURE.NOTE]
	В примере кода используется ADAL версии 2 (общедоступная версия). В нем не используется ADAL версии 4 — предварительная версия, предназначенная для работы с Azure AD B2C. В случае предварительной версии Azure AD B2C для взаимодействия с Graph API необходимо использовать ADAL версии 2. Со временем доступ к API Graph будет реализован и в ADAL версии 4, поэтому использовать в полном решении Azure AD B2C две версии ADAL необязательно.

После запуска `B2CGraphClient` создает экземпляр класса `B2CGraphClient`. Конструктор для этого класса настраивает формирование шаблонов проверки подлинности ADAL.

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are
	// provided to Azure AD in order to receive an access_token by using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Для примера используем команду `B2C Get-User`. При вызове `B2C Get-User` без дополнительных входных данных CLI вызывает метод `B2CGraphClient.GetAllUsers(...)`. А тот, в свою очередь, вызывает метод `B2CGraphClient.SendGraphGetRequest(...)`, который отправляет HTTP-запрос GET в API Graph. Прежде чем отправить запрос GET, `B2CGraphClient.SendGraphGetRequest(...)` получает маркер доступа, используя ADAL.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Маркер доступа для API Graph можно получить, вызвав метод ADAL `AuthenticationContext.AcquireToken(...)`. ADAL возвращает `access_token`, представляющий удостоверение приложения.

### Чтение пользователей

Чтобы извлечь список пользователей или получить определенного пользователя из API Graph, отправьте HTTP-запрос `GET` в конечную точку `/users`. Для всех пользователей клиента запрос будет выглядеть следующим образом:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть, как работает этот запрос, выполните следующую команду:

 ```
 > B2C Get-User
 ```

При этом необходимо обратить внимание на два важных момента.

- Маркер доступа, полученный из ADAL, добавлен в заголовок `Authorization` с помощью схемы `Bearer`.
- Для клиентов B2C необходимо использовать параметр запроса `api-version=1.6`.

Оба фрагмента сведений обрабатываются в методе `B2CGraphClient.SendGraphGetRequest(...)`.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the 1.6 Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Создание учетных записей пользователей-клиентов

При создании учетных записей пользователей в клиенте B2C можно отправить HTTP-запрос `POST` в конечную точку `/users`.

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"signInNames": [                            // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "LocalAccount",            // always set to 'LocalAccount'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Для создания пользователей-потребителей требуется большинство свойств в этом запросе. Для получения дополнительных сведений щелкните [здесь](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Обратите внимание, что для иллюстрации добавлены комментарии `//`. Их не нужно добавлять в запрос.

Чтобы просмотреть запрос, выполните следующие команды:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Команда `Create-User` принимает JSON-файл в качестве входного параметра. Он содержит представление JSON объекта пользователя. В примере кода находятся два примера JSON-файла: `usertemplate-email.json` и `usertemplate-username.json`. Вы можете изменить эти файлы с учетом своих требований. Помимо указанных выше можно использовать и другие поля, включенные в эти файлы. Дополнительные сведения о других необязательных полях см. в [справочнике по сущности API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Процесс формирования запроса POST можно увидеть в методе `B2CGraphClient.SendGraphPostRequest(...)`.

- Сначала добавляется маркер доступа в заголовок `Authorization` запроса.
- Затем задается параметр `api-version=1.6`.
- После чего добавляется объект пользователя JSON в текст запроса.

> [AZURE.NOTE]
Если у учетных записей, переносимых из существующего хранилища пользователей, надежность пароля ниже, чем [высокая надежность пароля, обеспечиваемая Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), можно отключить требование надежного пароля с помощью значения `DisableStrongPassword` свойства `passwordPolicies`. Например, можно изменить запрос создания пользователя, приведенный выше, следующим образом: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### Обновление учетных записей пользователей-клиентов

Обновление объектов пользователей аналогично процессу их создания, за исключением того, что при обновлении используется метод HTTP `PATCH`.

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

Для обновления пользователя можно добавить в JSON-файлы новые данные. Можно использовать `B2CGraphClient`, чтобы выполнить одну из следующих команд.

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Сведения о том, как отправить этот запрос, см. в методе `B2CGraphClient.SendGraphPatchRequest(...)`.

### Поиск пользователей

Искать пользователей в клиенте B2C можно разными способами. Можно использовать идентификатор объекта пользователя или идентификатор входа пользователя (т. е. свойство `signInNames`).

Выполните одну из следующих команд для поиска конкретного пользователя.

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Вот несколько примеров.

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### Удаление пользователей

Удаление пользователей выполняется очень просто. Воспользуйтесь методом HTTP `DELETE` и составьте URL-адрес с правильным идентификатором объекта.

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть пример, выполните следующую команду и просмотрите запрос delete, который будет выведен на консоль.

```
> B2C Delete-User <object-id-of-user>
```

Сведения о том, как отправить этот запрос, см. в методе `B2CGraphClient.SendGraphDeleteRequest(...)`.

Наряду с управлением пользователями API Graph позволяет выполнять и многие другие действия. Дополнительные сведения о каждом из таких действий и примеры запросов см. в [справочнике по API Graph для Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## Использование настраиваемых атрибутов

В большинстве клиентских приложений необходимо сохранять те или иные данные профилей пользователей-клиентов. Один из способов решения данной задачи — настройка пользовательского атрибута в клиенте B2C. Этот атрибут можно использовать как и любое другое свойство объекта пользователя. Атрибут можно обновить, удалить, использовать для запроса, отправить как утверждение в маркере входа и т. д.

Инструкции по указанию настраиваемых атрибутов клиента B2C см. в [справочнике по настраиваемым атрибутам в предварительной версии B2C](active-directory-b2c-reference-custom-attr.md).

Для просмотра пользовательских атрибутов, определенных в клиенте B2C, можно использовать `B2CGraphClient`.

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Выходные данные этих функций содержат сведения о каждом пользовательском атрибуте, например:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Полное имя, например `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, можно использовать как свойство объектов пользователей. Для этого достаточно добавить в JSON-файл новое свойство, указать значение для него и запустить указанный ниже код.

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Использование `B2CGraphClient` позволяет получить приложение-службу, которое обеспечивает управление пользователями клиента B2C программным путем. `B2CGraphClient` проходит аутентификацию в API Graph Azure AD по собственному удостоверению приложения. Кроме того, B2CGraphClient получает маркеры, используя секрет клиента. Добавляя эту функцию в свое приложение, учитывайте некоторые ключевые особенности приложений B2C.

- Приложению необходимо предоставить соответствующие разрешения в клиенте.
- На данном этапе для получения маркеров доступа необходимо использовать ADAL версии 2. (Кроме того, сообщения протокола можно отправлять напрямую, не используя библиотеку.)
- При вызове API Graph укажите `api-version=1.6`.
- При создании и обновлении пользователей-клиентов требуются некоторые свойства, как описано выше.

Если у вас есть вопросы о действиях, которые вы хотели бы выполнить с помощью API Graph для клиента B2C, оставьте комментарий к статье или отправьте заявку в репозиторий примеров кода GitHub.

<!---HONumber=AcomDC_0601_2016-->