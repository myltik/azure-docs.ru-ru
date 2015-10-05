<properties
	pageTitle="Предварительная версия Azure AD B2C | Microsoft Azure"
	description="Как вызвать API Graph для каталога B2C, используя удостоверение приложения для автоматизации процесса."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>


# Предварительная версия Azure AD B2C: использование API Graph

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Обычно каталоги Azure AD B2C отличаются большими размерами, а значит, многие распространенные задачи управления каталогами необходимо решать программным путем. Основным примером может служить управление пользователями — возможно, вам потребуется перенести существующее хранилище пользователей в каталог B2C или разместить регистрацию пользователей на своей странице и создавать учетные записи пользователей Azure AD в фоновом режиме. Для выполнения таких типов задач требуется возможность создавать, читать, обновлять и удалять учетные записи пользователей, обеспечить которую позволяет API Graph Azure AD.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
Для каталогов B2C используются в основном два режима обмена данными с API Graph.

- Если речь идет об интерактивных разовых задачах, то задачи управления должны выполняться под учетной записью администратора в каталоге B2C. Для использования этого режима, прежде чем выполнять вызовы в API Graph, администратор должен войти в систему со своими учетными данными.
- В случае автоматизированных непрерывно выполняемых задач задачи управления должны выполняться с использованием типа учетной записи службы, которому были предоставлены необходимые привилегии. В Azure AD это можно сделать, зарегистрировав приложение и настроив проверку подлинности в Azure AD по "удостоверению приложения", используя [предоставление учетных данных клиента OAuth 2.0](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). В этом случае приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя.  

В этой статье мы продемонстрируем действия в автоматизированном варианте использования. Для этого в .NET 4.5 мы соберем B2CGraphClient, выполняющий операции CRUD пользователя. Чтобы клиент можно было опробовать на практике, в нем предусмотрен интерфейс командной строки Windows, позволяющий вызывать различные методы. При этом код записывается как для неинтерактивной, автоматизированной задачи. Давайте приступим.

## Создание каталога с поддержкой B2C

Для создания приложения или пользователей, а также для работы с Azure AD в целом требуется каталог с поддержкой B2C и учетная запись администратора в этом каталоге. Если у вас еще нет каталога и учетной записи, выполните инструкции руководства [Приступая к работе с Azure AD B2C](active-directory-b2c-get-started.md).

## Регистрация приложения службы в каталоге

Теперь, когда каталог B2C у вас есть, необходимо создать приложение службы с помощью командлетов Azure AD Powershell. Для начала загрузите и установите [помощник по входу в Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Затем загрузите и установите [64-разрядный модуль Azure Active Directory для Windows PowerShell.](http://go.microsoft.com/fwlink/p/?linkid=236297).

Установив модуль Powershell, откройте Powershell и подключитесь к каталогу B2C. После выполнения `Get-Credential` появится запрос имени пользователя и пароля. Введите данные своей учетной записи администратора каталога B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
``` 

Прежде чем создавать приложения, необходимо создать новый "секрет клиента". Приложение будет использовать секрет клиента для проверки подлинности в Azure AD и получения маркеров доступа. Допустимый секрет можно создать в Powershell.

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
``` 

Последняя из указанных выше команд позволяет распечатать созданный секрет клиента. Скопируйте его в безопасное место, он вам скоро понадобится. Теперь можно создать приложение, указав в учетных данных новый секрет клиента.

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

Если приложение будет создано, этот код выведет на печать некоторые свойства приложения, такие как указанные выше. Вам потребуются свойства `ObjectId` и `AppPrincipalId`, поэтому также скопируйте их значения.

Теперь, когда вы создали приложение в каталоге B2C, назначьте ему разрешения, необходимые для выполнения операций CRUD. Приложению нужно назначить три различные роли: модули чтения каталога (для чтения пользователей), модули записи каталога (для создания и обновления пользователей) и администратор учетных записей пользователей (для удаления пользователей). Идентификаторы этих ролей известны, что позволяет выполнить указанные ниже команды, заменив параметр `-RoleMemberObjectId` на `ObjectId` из представленного выше кода. Чтобы просмотреть список всех ролей каталога, выполните команду `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

Теперь у вас есть приложение, у которого есть разрешение на создание, чтение, обновление и удаление пользователей из каталога B2C. Давайте напишем код, в котором оно будет использоваться.

## Загрузка, настройка и построение образца кода

Для начала загрузим и выполним образец кода. А теперь посмотрим, что происходит за сценой. Вы можете [загрузить образец кода в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) или клонировать его в желаемый каталог.

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Откройте решение Visual Studio `B2CGraphClient\B2CGraphClient.sln` в Visual Studio. В проекте `B2CGraphClient` откройте файл `App.config`. Замените три параметра приложения собственными значениями, например:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

Теперь щелкните решение `B2CGraphClient` правой кнопкой мыши и постройте образец кода заново. В случае успеха у вас появится исполняемый файл `B2C.exe` в каталоге `B2CGraphClient\bin\Debug`.

## CRUD пользователя с API Graph

Чтобы использовать B2CGraphClient, откройте командную строку Windows и перейдите в каталог `Debug` с помощью команды cd. Выполните команду `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Появится краткое описание каждой команды. При вызове любой из этих команд B2CGraphClient будет отправлять запрос в API Graph Azure AD.

### Получение маркера доступа

При любом запросе в Graph API требуется маркер доступа для проверки подлинности. Для получения маркеров доступа B2CGraphClient использует библиотеку проверки подлинности Active Directory (ADAL) с открытым исходным кодом. Использовать ADAL для получения маркеров необязательно — их можно получать, составляя HTTP-запросы вручную. ADAL упрощает получение маркеров, так как предоставляет простой API и берет на себя некоторые важные действия, такие как кэширование маркеров доступа.

> [AZURE.NOTE]Код в этом примере использует ADAL версии 2 — общедоступную версию ADAL. В нем НЕ используется ADAL версии 4 — предварительная версия, предназначенная для работы с Azure AD B2C. В случае предварительной версии Azure AD B2C для взаимодействия с Graph API необходимо использовать ADAL версии 2. Со временем доступ к API Graph будет реализован и в ADAL версии 4, поэтому использовать в полном решении Azure AD B2C две различные версии ADAL необязательно.

После запуска B2CGraphClient создает экземпляр класса `B2CGraphClient`. Конструктор для этого класса настраивает формирование шаблонов проверки подлинности ADAL.

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the directory to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are 
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Для примера возьмем команду `B2C Get-User`. Если `Get-User` вызывается без дополнительных входных данных, интерфейс командной строки вызывает метод `B2CGraphClient.GetAllUsers(...)`, а тот, в свою очередь, вызывает метод `B2CGraphClient.SendGraphGetRequest(...)`, который отправляет в API Graph HTTP-запрос GET. Прежде чем отправить запрос GET, он получает маркер доступа, используя ADAL.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	... 

```

Как видите, маркер доступа для API Graph можно получить, вызвав метод ADAL `AuthenticationContext.AcquireToken(...)`. ADAL возвращает маркер доступа, представляющий удостоверение приложения.

### Чтение пользователей

Чтобы извлечь список пользователей из API Graph или получить определенного пользователя, отправьте HTTP-запрос GET в конечную точку `/users`. Для всех пользователей в каталоге запрос будет выглядеть следующим образом:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

Чтобы увидеть, как работает этот запрос, выполните следующую команду:

 ```
 > B2C Get-User
 ```
 
При этом необходимо обратить внимание на два важных момента.

- Маркер доступа, полученный из ADAL, должен быть добавлен в заголовок `Authorization` с помощью схемы `Bearer`.
- Для каталогов B2C необходимо использовать параметр запроса `api-version=beta`.


> [AZURE.NOTE]Бета-версия API Graph Azure AD включает функции предварительного просмотра. Данные бета-версии см. в [этой записи блога рабочей группы API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).

Оба фрагмента данных обрабатываются в методе `B2CGraphClient.SendGraphGetRequest(...)`.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...
	
	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}
	
	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);
	
	... 
```
		
### Создание учетных записей пользователей-клиентов 

При создании учетных записей пользователей в каталоге B2C можно отправить HTTP-запрос POST в конечную точку `/users`.

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.
	 
	"accountEnabled": false,                    // always set to false
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	}
}
```

Для создания пользователей-клиентов требуется каждое свойство, включенное в приведенный выше запрос. Комментарии `//` добавлены для иллюстрации, и включать их в запрос не нужно.

Чтобы увидеть, как работает этот запрос, выполните одну из следующих команд:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Команда `Create-User` принимает файл `.json` как входной параметр, содержащий представление JSON объекта-пользователя. Образец кода включает два примера файлов `.json`, `usertemplate-email.json` и `usertemplate-username.json`, которые вы можете изменить согласно своим потребностям. Помимо указанных выше можно использовать и другие поля, включенные в эти файлы. Информацию о других полях см. в [справочнике по сущностям API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Формирование запроса POST можно увидеть в методе `B2CGraphClient.SendGraphPostRequest(...)`, который:

- присоединяет маркер доступа к заголовку `Authorization` запроса;
- устанавливает значение `api-version=beta`;
- включает объект пользователя JSON в тело запроса.

### Обновление учетных записей пользователей-клиентов

Обновление пользовательских объектов аналогично процессу их создания, но выполняется с использованием HTTP-команды PATCH.

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

Для обновления пользователя можно обновить JSON-файлы, добавив новые данные, или выполнить одну из следующих команд B2CGraphClient.

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```
	
Информацию об отправке этого запроса вы найдете в методе `B2CGraphClient.SendGraphPatchRequest(...)`.

### Удаление пользователей

Удаление пользователей выполняется напрямую — просто воспользуйтесь HTTP-командой DELETE и составьте URL-адрес с правильным идентификатором объекта.

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть пример, выполните указанную ниже команду и просмотрите запрос DELETE, который будет выведен на консоль.

```
> B2C Delete-User <object-id-of-user>
```

Информацию об отправке этого запроса вы найдете в методе `B2CGraphClient.SendGraphDeleteRequest(...)`.

Наряду с управлением пользователями API Graph позволяет выполнять и многие другие действия. Подробные сведения о каждом из таких действий и примеры запросов см. в [справочнике по API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## Использование настраиваемых атрибутов

Почти в любом клиентском приложении необходимо сохранять те или иные данные профилей пользователей-клиентов. Один из способов решения данной задачи — настройка пользовательского атрибута в каталоге B2C, позволяющая работать с этим атрибутом как с любым другим свойством объекта пользователя. Атрибут можно обновить, удалить, использовать для запроса, отправить как утверждение в маркере входа и т. д.

Инструкции по настройке пользовательского атрибута в каталоге B2C см. в [справочнике по пользовательским атрибутам в предварительной версии B2C](active-directory-b2c-reference-custom-attr.md).

Для просмотра пользовательских атрибутов, определенных в каталоге B2C, можно использовать B2CGraphClient.

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Выходные данные этих функций содержат сведения о каждом пользовательском атрибуте.

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

Полное имя, например `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, можно использовать в качестве свойства объектов пользователей. Для это достаточно обновить значение свойства в соответствующем файле `.json` и выполнить следующую команду:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

И все! В B2CGraphClient входит приложение-служба, позволяющее управлять пользователями каталога B2C программным путем. Оно проходит проверку подлинности в API Graph Azure AD по собственному удостоверению и получает маркеры, используя секрет клиента. Добавляя эту функцию в свое приложение, учитывайте некоторые ключевые особенности приложений B2C.

- Приложению необходимо предоставить соответствующие разрешения в каталоге.
- На данном этапе для получения маркеров доступа необходимо использовать ADAL версии 2 (или отправлять сообщения протокола напрямую, без привлечения библиотеки).
- При вызове API Graph используйте [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Некоторые описанные выше свойства при создании и обновлении пользователей-клиентов являются обязательными.

Если у вас есть вопросы или запросы о действиях, которые вы хотели бы выполнить с помощью API Graph в каталоге B2C, обращайтесь! Оставьте комментарий к статье или отправьте заявку в репозиторий образцов кода GitHub.

<!---HONumber=Sept15_HO4-->