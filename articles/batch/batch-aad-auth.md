---
title: Аутентификация решений пакетной службы Azure с помощью Azure Active Directory | Документация Майкрософт
description: Пакетная служба поддерживает Azure AD для аутентификации из пакетной службы.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: danlep
ms.openlocfilehash: c28af5a9773cc362663831346b58f599aed6ea9a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789271"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Аутентификация решений пакетной службы с помощью Active Directory

Пакетная служба Azure поддерживает аутентификацию [Azure Active Directory][aad_about] (Azure AD). Azure AD — многопользовательский облачный каталог и служба управления удостоверениями корпорации Майкрософт. Инфраструктура Azure использует Azure AD для аутентификации клиентов, администраторов служб и пользователей организации.

Процесс аутентификации Azure AD в пакетной службе Azure можно выполнить двумя способами:

- Аутентификация пользователя, взаимодействующего с приложением, с использованием **встроенной аутентификации**. Приложение, использующее встроенную аутентификацию, собирает учетные данные пользователя и использует их для аутентификации доступа к ресурсам пакетной службы.
- Аутентификация автоматического приложения с использованием **субъекта-службы**. Субъект-служба определяет политику и разрешения приложения, чтобы представить приложение при получении доступа к ресурсам в среде выполнения.

Дополнительные сведения о службе Azure AD см. в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Конечные точки аутентификации

Чтобы выполнить аутентификацию приложений пакетной службы с помощью Azure AD, необходимо включить некоторые известные конечные точки в код.

### <a name="azure-ad-endpoint"></a>Конечная точка Azure AD

Базовая конечная точка авторизации Azure AD:

`https://login.microsoftonline.com/`

Чтобы выполнить аутентификацию с помощью Azure AD, вам потребуется эта конечная точка и идентификатор клиента (идентификатором каталога). Идентификатор клиента определяет клиент Azure AD для аутентификации. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> В процессе аутентификации с использованием субъекта-службы требуется конечная точка определенного клиента. 
> 
> При аутентификации с помощью встроенной аутентификации эта конечная точка является не обязательной. Тем не менее мы советуем использовать ее. Но вы также можете использовать общую конечную точку Azure AD, которая предоставляет универсальный интерфейс для сбора общих учетных данных в случае отсутствия конкретного клиента. Общая конечная точка выглядит так: `https://login.microsoftonline.com/common`.
>
>

Дополнительные сведения о конечных точках Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Конечная точка ресурса пакетной службы

**Конечная точка ресурса пакетной службы Azure** позволяет получить токен аутентификации запросов для пакетной службы:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Регистрация приложения в клиенте

При использовании Azure AD для аутентификации в первую очередь необходимо зарегистрировать приложение в клиенте Azure AD. Зарегистрировав приложение, вы сможете вызвать [библиотеку аутентификации Azure Active Directory ][aad_adal] (ADAL) из кода. Эта библиотека предоставляет API для аутентификации с помощью Azure AD в приложении. Регистрация приложения необходима, если вы планируете использовать встроенную аутентификацию или аутентификацию с использованием субъекта-службы.

При регистрации приложения вы отправляете сведения о приложении в Azure AD. После этого служба Azure AD предоставит идентификатор приложения (*идентификатор клиента*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе приложения см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Чтобы зарегистрировать приложение пакетной службы, выполните инструкции, приведенные в разделе [Добавление приложения](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) статьи [Интеграция приложений с Azure Active Directory][aad_integrate]. При регистрации приложения как собственного вы можете указать любой допустимый универсальный код ресурса (URI) в качестве **URI перенаправления**. Реальную конечную точку указывать необязательно.

После регистрации приложения отобразится его идентификатор:

![Регистрация приложения пакетной службы в Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Получение идентификатора клиента для Active Directory

Идентификатор клиента определяет клиент Azure AD, который предоставляет службы аутентификации приложению. Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
2. Щелкните **Свойства**.
3. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Копирование идентификатора каталога](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Использование встроенной аутентификации

Чтобы выполнить аутентификацию с помощью встроенной аутентификации, необходимо предоставить приложению разрешения на подключение к API пакетной службы. Это позволит ему выполнить аутентификацию вызовов к API пакетной службы с помощью Azure AD.

Чтобы предоставить приложению доступ к пакетной службе, после [регистрации приложения](#register-your-application-with-an-azure-ad-tenant) выполните следующие действия на портале Azure:

1. На портале Azure на панели навигации слева выберите **Все службы**. Щелкните **Регистрация приложений**.
2. Найдите имя приложения в списке зарегистрированных приложений:

    ![Поиск имени приложения](./media/batch-aad-auth/search-app-registration.png)

3. Щелкните приложение, а затем **Параметры**. В разделе **Доступ через API** выберите **Необходимые разрешения**.
4. В колонке **Необходимые разрешения** нажмите кнопку **Добавить**.
5. В разделе **Выбор API** найдите API пакетной службы. Продолжайте поиск для каждой из этих строк, пока не найдете API:
    1. **MicrosoftAzureBatch**;
    2. **Microsoft Azure Batch**. Более новые клиенты Azure AD могут использовать это имя.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** — это идентификатор API пакетной службы. 
6. Выберите найденный API пакетной службы и щелкните **Выбрать**.
7. В разделе **Выбор разрешений** установите флажок рядом с параметром **Access Azure Batch Service** (Доступ к пакетной службе Azure) и щелкните **Выбрать**.
8. Нажмите кнопку **Done**(Готово).

Теперь в окне **Необходимые разрешения** вы можете видеть, что приложение Azure AD имеет доступ к библиотеке ADAL и API пакетной службы. Разрешения библиотеке ADAL предоставляются автоматически во время регистрации приложения в Azure AD.

![Предоставление разрешений API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Использование субъекта-службы 

Чтобы выполнить аутентификацию приложения, которое выполняется автоматически, необходимо использовать субъект-службу. После регистрации приложения выполните приведенные ниже действия на портале Azure, чтобы настроить субъект-службу.

1. Запросите секретный ключ приложения.
2. Назначьте приложению роль RBAC.

### <a name="request-a-secret-key-for-your-application"></a>Запрос секретного ключа приложения

При выполнении аутентификации с помощью субъекта-службы приложение отправляет в Azure AD идентификатор приложения и секретный ключ. Вам необходимо создать и скопировать секретный ключ, чтобы использовать его из кода.

На портале Azure сделайте следующее:

1. На портале Azure на панели навигации слева выберите **Все службы**. Щелкните **Регистрация приложений**.
2. Найдите имя приложения в списке зарегистрированных приложений.
3. Щелкните приложение, а затем **Параметры**. В разделе **Доступ через API** выберите **Ключи**.
4. Чтобы создать ключ, введите его описание. Затем выберите срок действия ключа — год или два. 
5. Нажмите кнопку **Сохранить**, чтобы создать и отобразить ключ. Скопируйте значение ключа в безопасное место, так как у вас больше не будет доступа к нему после закрытия колонки. 

    ![Создание секретного ключа](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Назначение приложению роли RBAC

Чтобы выполнить аутентификацию с использованием субъекта-службы, вам необходимо назначить приложению роль RBAC. Выполните следующие действия.

1. На портале Azure перейдите к учетной записи пакетной службы, используемой приложением.
2. В колонке **Параметры** учетной записи пакетной службы выберите **Управление доступом (IAM)**.
3. Нажмите кнопку **Add** (Добавить). 
4. В раскрывающемся списке **Роль** выберите роль приложения — _Участник_ или _Читатель_. Дополнительные сведения об этих ролях см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../role-based-access-control/overview.md).  
5. В поле **Выбрать** введите имя приложения. Выберите свое приложение в списке, а затем щелкните **Сохранить**.

После этого приложение с назначенной ролью RBAC должно появиться в параметрах контроля доступа. 

![Назначение приложению роли RBAC](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Получение идентификатора клиента для Azure Active Directory

Идентификатор клиента определяет клиент Azure AD, который предоставляет службы аутентификации приложению. Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
2. Щелкните **Свойства**.
3. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Копирование идентификатора каталога](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Примеры кода

В примерах кода в этом разделе показано, как выполнить аутентификацию в Azure AD, используя встроенную и субъект-службу. В большинстве примеров кода используется .NET, но общие концепции актуальны и для других языков.

> [!NOTE]
> Срок действия маркера проверки подлинности Azure AD истекает через час. При использовании долговременного объекта **BatchClient** мы советуем получать маркер из ADAL при каждом запросе, чтобы у вас всегда был допустимый маркер. 
>
>
> Чтобы достичь этого в .NET, напишите метод, который получает маркер из Azure AD, и передайте этот метод в качестве делегата в объект **BatchTokenCredentials**. Чтобы гарантировать предоставление допустимого маркера, при каждом запросе к пакетной службе вызывается метод делегата. По умолчанию ADAL кэширует маркеры, поэтому новый маркер извлекается из Azure AD только при необходимости. Дополнительные сведения о маркерах в Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Пример кода. Использование встроенной аутентификации Azure AD с библиотекой .NET для пакетной службы

Чтобы выполнить аутентификацию с использованием встроенной аутентификации из библиотеки .NET для пакетной службы, укажите ссылку на пакет [.NET для пакетной службы Azure](https://www.nuget.org/packages/Azure.Batch/) и пакет [библиотеки ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Добавьте в код следующие инструкции `using`:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Укажите ссылку на конечную точку Azure AD в коде, в том числе идентификатор клиента. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Укажите ссылку на конечную точку ресурса пакетной службы:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Укажите ссылку на учетную запись пакетной службы:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Идентификатор приложения становится доступен после регистрации приложения на портале Azure:

```csharp
private const string ClientId = "<application-id>";
```

Скопируйте также указанный URI перенаправления, если приложение зарегистрировано в качестве собственного приложения. URI перенаправления, указанный в коде, должен соответствовать URI перенаправления, который был указан при регистрации приложения:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Напишите метод обратного вызова, чтобы получить маркер проверки подлинности из Azure AD. Приведенный здесь метод обратного вызова **GetAuthenticationTokenAsync** вызывает библиотеку ADAL для аутентификации пользователя, взаимодействующего с приложением. Предоставленный ADAL метод **AcquireTokenAsync** запрашивает учетные данные пользователя. Как только пользователь введет их, приложение продолжит работу (если его учетные данные не кэшированы).

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Создайте объект **BatchTokenCredentials**, который принимает делегат в качестве параметра. Используйте эти учетные данные, чтобы открыть объект **BatchClient**. Вы можете использовать объект **BatchClient** для последующих операций в пакетной службе.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Пример кода. Использование субъекта-службы Azure AD с библиотекой .NET для пакетной службы

Чтобы выполнить аутентификацию с использованием субъекта-службы из библиотеки .NET для пакетной службы, укажите ссылки на пакет [.NET для пакетной службы Azure](https://www.nuget.org/packages/Azure.Batch/) и пакет [библиотеки ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Добавьте в код следующие инструкции `using`:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Укажите ссылку на конечную точку Azure AD в коде, в том числе идентификатор клиента. При использовании субъекта-службы необходимо указать конечную точку определенного клиента. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Укажите ссылку на конечную точку ресурса пакетной службы:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Укажите ссылку на учетную запись пакетной службы:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Идентификатор приложения становится доступен после регистрации приложения на портале Azure:

```csharp
private const string ClientId = "<application-id>";
```

Укажите секретный ключ, скопированный на портале Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Напишите метод обратного вызова, чтобы получить маркер проверки подлинности из Azure AD. Приведенный здесь метод обратного вызова **GetAuthenticationTokenAsync** вызывает библиотеку ADAL для автоматической аутентификации:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Создайте объект **BatchTokenCredentials**, который принимает делегат в качестве параметра. Используйте эти учетные данные, чтобы открыть объект **BatchClient**. Применяйте этот объект **BatchClient** для всех последующих операций в пакетной службе.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Пример кода. Использование субъекта-службы Azure AD с библиотекой Python для пакетной службы

Чтобы выполнить аутентификацию с использованием субъекта-службы из библиотеки Python для пакетной службы, установите модули [azure-batch](https://pypi.org/project/azure-batch/) и [azure-common](https://pypi.org/project/azure-common/) и укажите ссылки на них.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Если вы используете субъект-службу, обязательно укажите идентификатор клиента. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе [Получение идентификатора клиента для Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Укажите ссылку на конечную точку ресурса пакетной службы:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Укажите ссылку на учетную запись пакетной службы:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Идентификатор приложения становится доступен после регистрации приложения на портале Azure:

```python
CLIENT_ID = "<application-id>";
```

Укажите секретный ключ, скопированный на портале Azure:

```python
SECRET = "<secret-key>";
```

Создайте объект **ServicePrincipalCredentials**.

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Используйте учетные данные субъекта-службы, чтобы открыть объект **BatchServiceClient**. Используйте этот объект **BatchClient** для всех последующих операций в пакетной службе.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о службе Azure AD см. в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Подробные примеры, показывающие, как использовать ADAL, доступны в библиотеке [примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

* Дополнительные сведения о субъекте-службе см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Дополнительные сведения о создании субъекта-службы с помощью портала Azure см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../resource-group-create-service-principal-portal.md). Вы также можете создать субъект-службу с помощью PowerShell или Azure CLI.

* Дополнительные сведения об аутентификации решений по управлению пакетной службой с помощью Azure AD см. в [этой статье](batch-aad-auth-management.md).

* Пример Python для создания клиента пакетной службы, подлинность которого проверяется с помощью маркера Azure AD, см. в примере [Deploying Azure Batch Custom Image with a Python Script](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) (Развертывание пользовательского образа пакетной службы Azure с помощью скрипта Python).

[aad_about]: ../active-directory/active-directory-whatis.md "Что такое Microsoft Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Сценарии аутентификации в Azure Active Directory"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Интеграция приложений с Azure Active Directory"
[azure_portal]: http://portal.azure.com
