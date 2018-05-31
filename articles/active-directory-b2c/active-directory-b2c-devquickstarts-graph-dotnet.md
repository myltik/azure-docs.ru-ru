---
title: Использование API Graph в Azure AD B2C | Документация Майкрософт
description: Сведения о том, как вызвать API Graph для клиента B2C, используя удостоверение приложения для автоматизации процесса.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.openlocfilehash: 731ff24fe9cc1b5dbf0c597139a96ae80b863cc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140037"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C. Использование API Graph Azure AD

>[!NOTE]
> Необходимо использовать [API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) для управления пользователями в каталоге Azure AD B2C. Он отличается от API Microsoft Graph. Дополнительные сведения см. [здесь](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Как правило, клиенты Azure AD B2C (Azure AD) отличаются очень большими размерами. Это значит, что многие распространенные задачи управления клиентами необходимо решать программным путем. Основным примером может служить управление пользователями. Возможно, вам потребуется перенести существующее хранилище пользователя в клиент B2C. Кроме того, может потребоваться разместить регистрацию пользователей на своей странице и создавать учетные записи пользователей каталога Azure AD B2C в фоновом режиме. Для выполнения задач такого типа требуется возможность создавать, читать, обновлять и удалять учетные записи пользователей. Такую возможность предоставляет API Graph Azure AD.

Для клиентов B2C используются в основном два режима обмена данными с API Graph.

* Если речь идет об интерактивных разовых задачах, то их следует выполнять под учетной записью администратора клиента B2C. Для этого прежде чем выполнять вызовы в API Graph, администратор должен войти в систему со своими учетными данными.
* Для выполнения автоматизированных непрерывно выполняемых задач управления следует использовать тип учетной записи службы, которой предоставлены необходимые привилегии. В Azure AD это можно сделать путем регистрации приложения и выполнения проверки подлинности. Для этого нужен **идентификатор приложения** , который использует [предоставление учетных данных клиента OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). В этом случае приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя.

В этой статье вы узнаете, как выполнить автоматизированный вариант использования. Вы создадите `B2CGraphClient` .NET 4.5, который выполняет операции пользователя: создание, чтение, обновление и удаление (CRUD). В клиенте предусмотрен интерфейс командной строки Windows, позволяющий вызывать различные методы. При этом код записывается как для неинтерактивной автоматизированной задачи.

## <a name="get-an-azure-ad-b2c-tenant"></a>Получение клиента Azure AD B2C
Перед созданием приложений или пользователей вам потребуется клиент Azure AD B2C. Если у вас нет клиента, создайте его, следуя инструкциям в статье [Предварительная версия Azure Active Directory B2C: создание клиента Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Регистрация приложения в клиенте
После создания клиента B2C необходимо зарегистрировать приложение на [портале Azure](https://portal.azure.com).

> [!IMPORTANT]
> Чтобы использовать Graph API с вашим клиентом B2C, вам необходимо зарегистрировать приложение, используя службу *регистрации приложений* на портале Azure, **а не** меню приложений *Azure AD B2C*. Следующие инструкции приведут вас к нужному меню. Вам не удастся повторно использовать имеющиеся приложения B2C, зарегистрированные в меню *приложений* Azure AD B2C.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите клиент Azure AD B2C, щелкнув имя своей учетной записи в правом верхнем углу страницы.
3. На панели навигации слева выберите **Все службы**, щелкните **Регистрация приложений**, а затем нажмите кнопку **Добавить**.
4. Следуйте инструкциям на экране, а затем создайте новое приложение. 
    1. Выберите в качестве типа приложения **Веб-приложение или API**.    
    2. Укажите **любой URL-адрес для входа** (например, https://B2CGraphAPI)), так как он не неважен в этом примере.  
5. Теперь приложение появится в списке приложений. Щелкните его, чтобы получить **идентификатор приложения** (также известный как идентификатор клиента). Скопируйте его, так как он потребуется вам в одном из следующих разделов.
6. В меню "Параметры" щелкните **Ключи**.
7. В разделе **Пароли** введите описание ключа, выберите длительность и нажмите кнопку **Сохранить**. Скопируйте значение ключа (также известного как "Секрет клиента") для использования в следующем разделе.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Настройка разрешений на создание, чтение и обновление для приложения
Теперь необходимо настроить для приложения все необходимые разрешения на создание, чтение, обновление и удаление пользователей.

1. В меню "Регистрация приложений" на портале Azure выберите приложение.
2. В меню "Параметры" щелкните **Необходимые разрешения**.
3. В меню "Необходимые разрешения" щелкните **Windows Azure Active Directory**.
4. В меню "Разрешить доступ" выберите разрешение **Чтение и запись данных каталога** в списке **Разрешения приложений** и нажмите кнопку **Сохранить**.
5. Наконец, в меню "Необходимые разрешения" нажмите кнопку **Предоставить разрешения**.

Теперь у вас есть приложение с разрешением на создание, чтение и обновление пользователей из клиента B2C.

> [!NOTE]
> Для полной обработки предоставления разрешений может потребоваться несколько минут.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Настройка разрешений на удаление для приложения
В настоящее время разрешения на *чтение и запись данных каталога* **не** предусматривают возможность выполнять удаление, например удаление пользователей. Если вы хотите предоставить приложению возможность удалять пользователей, необходимо выполнить следующие дополнительные действия, требующие использования PowerShell. В противном случае перейдите к следующему разделу.

Если вы еще не сделали это, установите [модуль Azure AD PowerShell версии 1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Затем откройте модуль PowerShell и подключитесь к клиенту Azure AD B2C.

> [!IMPORTANT]
> Необходимо использовать учетную запись администратора клиента B2C, которая является **локальной** по отношению к клиенту B2C. Эти учетные записи выглядят следующим образом: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Теперь мы используем **идентификатор приложения** в приведенном ниже сценарии, чтобы назначить приложению роль администратора учетных записей пользователей, которая позволяет удалять пользователей. Идентификаторы этих ролей известны, поэтому можно просто ввести **идентификатор приложения** в приведенный ниже скрипт.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Теперь приложение имеет разрешение на удаление пользователей из клиента B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Скачивание, настройка и создание примера кода
Сначала скачайте и запустите пример кода. После этого мы сможем рассмотреть его подробнее.  Вы можете [скачать пример кода в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Его можно клонировать в необходимый каталог.

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Откройте решение Visual Studio `B2CGraphClient\B2CGraphClient.sln` в Visual Studio. В проекте `B2CGraphClient` откройте файл `App.config`. Замените три параметра приложения собственными значениями.

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Затем щелкните решение `B2CGraphClient` правой кнопкой мыши и еще раз создайте пример кода. Если вам удастся выполнить это действие, в каталоге `B2CGraphClient\bin\Debug` появится исполняемый файл `B2C.exe`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Создание операций CRUD пользователя с помощью API Graph
Чтобы использовать B2CGraphClient, откройте командную строку Windows (`cmd`) и замените свой каталог каталогом `Debug`. Затем выполните команду `B2C Help` .

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Появится краткое описание каждой команды. Каждый раз, когда вы вызываете одну из этих команд, клиент `B2CGraphClient` создает запрос к службе API Graph Azure AD.

### <a name="get-an-access-token"></a>Получение маркера доступа
При любом запросе к API Graph требуется маркер доступа для проверки подлинности. `B2CGraphClient` использует библиотеку проверки подлинности Active Directory (ADAL) с открытым кодом. ADAL упрощает получение маркеров, так как предоставляет простой API и выполняет некоторые важные действия, такие как кэширование маркеров доступа. Использовать ADAL для получения маркеров необязательно. Их можно получать, составляя HTTP-запросы вручную.

> [!NOTE]
> В этом примере кода используется ADAL версии 2 для связи с API Graph.  Для получения маркеров доступа, которые могут использоваться с API Graph Azure AD, необходимо использовать ADAL версии 2 или 3.
> 
> 

После запуска `B2CGraphClient` создает экземпляр класса `B2CGraphClient`. Конструктор для этого класса настраивает формирование шаблонов проверки подлинности ADAL.

```csharp
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

Для примера используем команду `B2C Get-User` . При вызове `B2C Get-User` без дополнительных входных данных CLI вызывает метод `B2CGraphClient.GetAllUsers(...)`. А тот, в свою очередь, вызывает метод `B2CGraphClient.SendGraphGetRequest(...)`, который отправляет HTTP-запрос GET в API Graph. Прежде чем отправить запрос GET, `B2CGraphClient.SendGraphGetRequest(...)` получает маркер доступа, используя ADAL.

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Чтобы получить маркер доступа для API Graph, вызовите метод ADAL `AuthenticationContext.AcquireToken(...)` . ADAL возвращает `access_token` , представляющий удостоверение приложения.

### <a name="read-users"></a>Чтение пользователей
Чтобы извлечь список пользователей или получить определенного пользователя из API Graph, отправьте HTTP-запрос `GET` в конечную точку `/users`. Для всех пользователей клиента запрос будет выглядеть следующим образом:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть, как работает этот запрос, выполните следующую команду:

 ```cmd
 B2C Get-User
 ```

При этом необходимо обратить внимание на два важных момента.

* Маркер доступа, полученный из ADAL, добавлен в заголовок `Authorization` с помощью схемы `Bearer`.
* Для клиентов B2C необходимо использовать параметр запроса `api-version=1.6`.

Оба фрагмента сведений обрабатываются в методе `B2CGraphClient.SendGraphGetRequest(...)` .

```csharp
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

### <a name="create-consumer-user-accounts"></a>Создание учетных записей пользователей-клиентов
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
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Для создания пользователей-потребителей требуется большинство свойств в этом запросе. Для получения дополнительных сведений щелкните [здесь](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Обратите внимание, что комментарии `//` добавлены для наглядности. Их не нужно добавлять в запрос.

Чтобы просмотреть запрос, выполните следующие команды:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Команда `Create-User` принимает JSON-файл в качестве входного параметра. Он содержит представление JSON объекта пользователя. Пример кода содержит два примера JSON-файла: `usertemplate-email.json` и `usertemplate-username.json`. Вы можете изменить эти файлы с учетом своих требований. Помимо указанных выше можно использовать и другие поля, включенные в эти файлы. Дополнительные сведения о других необязательных полях см. в [справочнике по сущностям API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Процесс формирования запроса POST можно увидеть в методе `B2CGraphClient.SendGraphPostRequest(...)`.

* Сначала добавляется маркер доступа в заголовок `Authorization` запроса.
* Затем задается параметр `api-version=1.6`.
* После чего добавляется объект пользователя JSON в текст запроса.

> [!NOTE]
> Если у учетных записей, переносимых из существующего хранилища пользователей, надежность пароля ниже, чем [высокая надежность пароля, обеспечиваемая Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), можно отключить требование надежного пароля с помощью значения `DisableStrongPassword` свойства `passwordPolicies`. Например, можно изменить запрос создания пользователя, приведенный выше, следующим образом: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Обновление учетных записей пользователей-клиентов
Обновление объектов пользователей аналогично процессу их создания, за исключением того, что при обновлении используется метод HTTP `PATCH`.

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Для обновления пользователя можно добавить в JSON-файлы новые данные. Можно использовать `B2CGraphClient`, чтобы выполнить одну из следующих команд:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Сведения о том, как отправить этот запрос, см. в методе `B2CGraphClient.SendGraphPatchRequest(...)`.

### <a name="search-users"></a>Поиск пользователей
Искать пользователей в клиенте B2C можно разными способами. Можно использовать идентификатор объекта пользователя или идентификатор входа пользователя (т. е. свойство `signInNames`).

Выполните одну из следующих команд для поиска конкретного пользователя.

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Вот несколько примеров.

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Удаление пользователей
Удаление пользователей выполняется очень просто. Воспользуйтесь методом HTTP `DELETE` и составьте URL-адрес с правильным идентификатором объекта.

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Чтобы увидеть пример, выполните следующую команду и просмотрите запрос delete, который будет выведен на консоль.

```cmd
B2C Delete-User <object-id-of-user>
```

Сведения о том, как отправить этот запрос, см. в методе `B2CGraphClient.SendGraphDeleteRequest(...)`.

Наряду с управлением пользователями API Graph позволяет выполнять и многие другие действия. Дополнительные сведения о каждом действии и примеры запросов см. в [справочнике по API Graph для Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="use-custom-attributes"></a>Использование настраиваемых атрибутов
В большинстве клиентских приложений необходимо сохранять те или иные данные профилей пользователей-клиентов. Один из способов решения данной задачи — настройка пользовательского атрибута в клиенте B2C. Этот атрибут можно использовать как и любое другое свойство объекта пользователя. Атрибут можно обновить, удалить, использовать для запроса, отправить как утверждение в маркере входа и т. д.

Инструкции по указанию настраиваемых атрибутов клиента B2C см. в [справочнике по настраиваемым атрибутам B2C](active-directory-b2c-reference-custom-attr.md).

Для просмотра пользовательских атрибутов, определенных в клиенте B2C, можно использовать `B2CGraphClient`.

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Выходные данные этих функций содержат сведения о каждом пользовательском атрибуте, например:

```json
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

Полное имя, например `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, можно использовать как свойство объектов пользователей.  Для этого достаточно добавить в JSON-файл новое свойство, указать значение для него и запустить указанный ниже код.

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Использование `B2CGraphClient`позволяет получить приложение-службу, которое обеспечивает управление пользователями клиента B2C программным путем. `B2CGraphClient` проходит аутентификацию в API Graph Azure AD по собственному удостоверению приложения. Кроме того, B2CGraphClient получает маркеры, используя секрет клиента. Добавляя эту функцию в свое приложение, учитывайте некоторые ключевые особенности приложений B2C.

* Приложению необходимо предоставить соответствующие разрешения в клиенте.
* На данном этапе для получения токенов доступа необходимо использовать ADAL (не MSAL). (Кроме того, сообщения протокола можно отправлять напрямую, не используя библиотеку.)
* При вызове API Graph укажите `api-version=1.6`.
* При создании и обновлении пользователей-клиентов требуются некоторые свойства, как описано выше.

Если у вас есть вопросы о действиях, которые вы хотели бы выполнить с помощью API Graph для клиента B2C, оставьте комментарий к статье или отправьте заявку в репозиторий примеров кода GitHub.

