---
title: "Использование Azure Active Directory для проверки подлинности в пакетной службе Azure | Документация Майкрософт"
description: "Пакетная служба поддерживает Azure AD для проверки подлинности в службе и поставщике ресурсов пакетной службы."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Проверка подлинности в решениях пакетной службы с помощью Active Directory

Пакетная служба Azure поддерживает проверку подлинности для пакетной службы и службы управления пакетной службой с помощью [Azure Active Directory][aad_about] (Azure AD). Azure AD — многопользовательский облачный каталог и служба управления удостоверениями корпорации Майкрософт. Инфраструктура Azure уже использует Azure AD для проверки подлинности клиентов, администраторов служб и пользователей организации.

В этой статье рассматривается использование Azure AD для проверки подлинности в приложениях, применяющих библиотеку .NET для управления пакетной службой или библиотеку .NET для пакетной службы. В контексте интерфейсов API .NET для пакетной службы мы покажем, как использовать Azure AD для проверки подлинности администратора или соадминистратора подписки с помощью встроенной аутентификации. Пользователь, прошедший проверку подлинности, может отправлять запросы к пакетной службе Azure.

Кроме того, можно использовать Azure AD для проверки подлинности доступа к приложению, выполняющемуся в автоматическом режиме. В этой статье мы рассмотрим использование встроенной проверки подлинности Azure AD и приведем ссылки на другие ресурсы, содержащие сведения о проверке подлинности автоматических приложений.

## <a name="use-azure-ad-with-batch-management-solutions"></a>Использование Azure AD в решениях по управлению пакетной службой

Библиотека .NET для управления пакетной службой предоставляет типы для работы с учетными записями пакетной службы, ключами учетной записи, приложениями и пакетами приложений. Данная библиотека является клиентом поставщика ресурсов Azure и используется совместно с [Azure Resource Manager][resman_overview] для программного управления этими ресурсами. 

Служба Azure AD необходима для проверки подлинности запросов, выполненных с помощью любого клиента поставщика ресурсов Azure, а также библиотеки .NET для управления пакетной службой и [Azure Resource Manager][resman_overview].

В этом разделе мы используем пример проекта [AccountManagment][acct_mgmt_sample], который доступен на сайте GitHub, чтобы изучить использование Azure AD с библиотекой .NET для управления пакетной службой. Пример AccountManagement — это консольное приложение, которое получает доступ к подписке программным способом, создает группу ресурсов и учетную запись пакетной службы, а также выполняет некоторые операции в учетной записи. 

Дополнительные сведения об использовании библиотеки .NET для управления пакетной службой и примера AccountManagement см. в статье [Управление учетными записями и квотами пакетной службы с помощью клиентской библиотеки .NET для управления пакетной службой](batch-management-dotnet.md).

### <a name="register-your-application-with-azure-ad"></a>Регистрация приложения в Azure AD

[Библиотека проверки подлинности Azure Active Directory][aad_adal] (ADAL) предоставляет программный интерфейс Azure AD для использования в приложениях. Чтобы вызвать ADAL в приложении, необходимо зарегистрировать приложение в клиенте Azure AD. При регистрации приложения в клиенте Azure AD нужно предоставить Azure AD сведения о приложении, включая его имя. После этого служба Azure AD предоставит идентификатор приложения, позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе приложения см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Чтобы зарегистрировать пример приложения AccountManagement, выполните инструкции, приведенные в разделе [Добавление приложения](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) статьи [Интеграция приложений с Azure Active Directory][aad_integrate]. Укажите в качестве типа приложения **собственное клиентское приложение**. В качестве **URI перенаправления** можно указать любой допустимый URI (например, `http://myaccountmanagementsample`). Реальную конечную точку указывать необязательно.

![](./media/batch-aad-auth/app-registration-management-plane.png)

После завершения процесса регистрации отобразится указанный для вашего приложения идентификатор приложения и объекта (субъекта-службы).  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>Обновление кода для ссылки на идентификатор приложения 

Клиентское приложение использует идентификатор приложения (также называемый идентификатором клиента) для доступа к Azure AD во время выполнения. После регистрации приложения на портале Azure обновите код, указав в нем идентификатор приложения, предоставленный Azure AD для зарегистрированного приложения. В примере приложения AccountManagement скопируйте идентификатор приложения из портала Azure в соответствующую константу:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Скопируйте также URI перенаправления, который вы указали во время процесса регистрации.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Предоставление доступа API Azure Resource Manager к приложению

Теперь необходимо делегировать доступ к приложению для API Azure Resource Manager. Идентификатором Azure AD для API Resource Manager является **API управления службами Windows Azure**.

На портале Azure сделайте следующее:

1. В левой области навигации портала Azure выберите **Больше служб**, щелкните **Регистрация приложений**, а затем нажмите кнопку **Добавить**.
2. Найдите имя приложения в списке зарегистрированных приложений:

    ![Поиск имени приложения](./media/batch-aad-auth/search-app-registration.png)

3. Откройте колонку **Параметры**. В разделе **Доступ через API** выберите **Необходимые разрешения**.
4. Щелкните **Добавить** для добавления нового необходимого разрешения. 
5. На шаге 1 введите **API управления службами Windows Azure**, выберите этот API в списке результатов и нажмите кнопку **Выбрать**.
6. На шаге 2 установите флажок рядом с параметром **Access Azure classic deployment model as organization users** (Доступ к классической модели развертывания Azure от имени пользователей организации) и нажмите кнопку **Выбрать**.
7. Нажмите кнопку **Готово**.

Теперь в колонке **Необходимые разрешения** показано, что разрешения на доступ к приложению предоставлены и интерфейсам API Resource Manager, и ADAL. Разрешения ADAL предоставляются по умолчанию во время регистрации приложения в Azure AD.

![Делегирование разрешений для API Azure Resource Manager](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Получение маркера проверки подлинности Azure AD

Пример приложения AccountManagement определяет константы, предоставляющие конечную точку для Azure AD и Azure Resource Manager. Приложение использует эти константы для запроса сведений о подписке в Azure AD. Оставьте их без изменений:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

После регистрации приложения AccountManagement в клиенте Azure AD и указания необходимых значений в примере исходного кода оно готово к проверке подлинности с помощью Azure AD. При запуске примера ADAL пытается получить маркер проверки подлинности. На этом этапе предлагается ввести учетные данные Майкрософт: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

После указания учетных данных пример приложения может отправлять запросы, прошедшие проверку подлинности, к службе управления пакетами. 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Использование Azure AD в решениях пакетной службы

Библиотека .NET пакетной службы предоставляет типы для создания рабочих процессов параллельной обработки с помощью пакетной службы. Пакетная служба поддерживает проверку подлинности с помощью [общего ключа](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) и Azure AD. В этом разделе мы рассмотрим выполнение проверки подлинности с помощью Azure AD.

>[!NOTE]
>При создании учетной записи пакетной службы можно указать, где будут выделяться пулы: в подписке, управляемой пакетной службой, или в подписке пользователя. Если учетная запись выделяет пулы в подписке пользователя, необходимо использовать Azure AD для проверки подлинности запросов к ресурсам в этой учетной записи.
>
>

Проверка подлинности приложений .NET пакетной службы через Azure AD аналогична проверке подлинности приложений .NET для управления пакетной службой, за исключением нескольких отличий, которые приведены в этом разделе.

### <a name="batch-service-endpoints"></a>Конечные точки пакетной службы

Конечные точки пакетной службы отличаются от конечных точек, используемых в библиотеке .NET для управления пакетной службой.

Конечная точка Azure AD для пакетной службы:

`https://login.microsoftonline.com/common`

Конечная точка ресурсов для пакетной службы:

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>Предоставление доступа к приложению для API пакетной службы

Прежде чем выполнять проверку подлинности приложения пакетной службы с помощью Azure AD, необходимо зарегистрировать его в Azure AD и предоставить ему доступ к API пакетной службы. Идентификатором Azure AD для API пакетной службы является **пакетная служба Microsoft Azure (MicrosoftAzureBatch)**.

1. Чтобы зарегистрировать приложение пакетной службы, выполните инструкции, приведенные в разделе [Добавление приложения](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) статьи [Интеграция приложений с Azure Active Directory][aad_integrate]. Вы можете указать любой допустимый URI в параметре **URI перенаправления**. Реальную конечную точку указывать необязательно.

    После регистрации приложения отобразится идентификатор приложения и объекта:

    ![Регистрация приложения пакетной службы в Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

2. Откройте колонку **Параметры**. В разделе **Доступ через API** выберите **Необходимые разрешения**.
3. В колонке **Необходимые разрешения** нажмите кнопку **Добавить**.
4. На шаге 1 найдите **MicrosoftAzureBatch**, выберите **Microsoft Azure Batch (MicrosoftAzureBatch)** (Пакетная служба Microsoft Azure (MicrosoftAzureBatch)) и нажмите кнопку **Выбрать**.
5. На шаге 2 установите флажок рядом с параметром **Access Azure Batch Service** (Доступ к пакетной службе Azure) и нажмите кнопку **Выбрать**.
6. Нажмите кнопку **Готово**.

Теперь в колонке **Необходимые разрешения** показано, что доступ к приложению Azure AD предоставлен и интерфейсам API пакетной службы, и службе Azure AD. 

![Разрешения API](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>Проверка подлинности учетных записей пакетной службы в подписке пользователя

При создании учетной записи пакетной службы можно выбрать подписку, в которой будут выделяться пулы. От вашего выбора зависит способ проверки подлинности запросов к ресурсам в этой учетной записи.

По умолчанию пулы выделяются в подписке пакетной службы. При выборе этого варианта можно выполнять проверку подлинности запросов к ресурсам в этой учетной записи с помощью общего ключа или Azure AD.

Вы также можете указать, что пулы пакетной службы должны выделяться в указанной подписке пользователя. Если выбран этот вариант, нужно выполнять проверку подлинности с помощью Azure AD.

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Рекомендации по использованию Azure AD с пакетной службой

Срок действия маркера проверки подлинности Azure AD истекает через час. При использовании долговременного объекта **BatchClient** мы советуем получать маркер из ADAL при каждом запросе, чтобы у вас всегда был допустимый маркер. 

Чтобы достичь этого в .NET, напишите метод, который получает маркер из Azure AD, и передайте этот метод в качестве делегата в объект **BatchTokenCredentials**. Чтобы гарантировать предоставление допустимого маркера, при каждом запросе к пакетной службе вызывается метод делегата. По умолчанию ADAL кэширует маркеры, поэтому новый маркер извлекается из Azure AD только при необходимости. С примером можно ознакомиться в разделе [Пример кода. Использование Azure AD с помощью пакетной службы для .NET](#code-example-using-azure-ad-with-batch-net). Дополнительные сведения о маркерах в Azure AD см. в статье [Сценарии аутентификации в Azure Active Directory][aad_auth_scenarios].

### <a name="code-example-using-azure-ad-with-batch-net"></a>Пример кода. Использование Azure AD с помощью пакетной службы для .NET

Для написания кода пакетной службы для .NET, выполняющего проверку подлинности с помощью Azure AD, укажите ссылки на [пакетную службу Azure для .NET](https://www.nuget.org/packages/Azure.Batch/) и на [пакет ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Добавьте в код следующие инструкции `using`:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Укажите в коде ссылки на общую конечную точку Azure AD и конечную точку Azure AD для пакетной службы:  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Укажите ссылку на конечную точку учетной записи пакетной службы:

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

Укажите идентификатор приложения (идентификатор клиента) для вашего приложения. Вы получаете идентификатор приложения после регистрации приложения на портале Azure. См. инструкции в разделе [Предоставление доступа к приложению для API пакетной службы](#grant-the-batch-service-api-access-to-your-application). 

```csharp
private const string ClientId = "<application-id>";
```

Необходимо также указать URI перенаправления, который может быть любым допустимым URI.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Напишите метод обратного вызова, чтобы получить маркер проверки подлинности из Azure AD. Метод **AcquireTokenAsync** предлагает пользователю ввести учетные данные и использует их для получения нового маркера.

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

Создайте объект **BatchTokenCredentials**, который принимает делегат в качестве параметра. Используйте эти учетные данные, чтобы открыть объект **BatchClient**. Затем вы можете использовать объект **BatchClient** для последующих операций в пакетной службе.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

Приведенный выше метод обратного вызова **GetAuthenticationTokenAsync** использует Azure AD для встроенной проверки подлинности пользователя, взаимодействующего с приложением. При вызове метода **AcquireTokenAsync** пользователь получает запрос на ввод учетных данных. Как только пользователь введет их, приложение продолжит работу. Вы также можете использовать Azure AD для проверки подлинности автоматического приложения с помощью субъекта-службы Azure AD. Дополнительные сведения см. в статьях [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) и [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../resource-group-create-service-principal-portal.md).  
 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о выполнении [примера приложения AccountManagement][acct_mgmt_sample] см. в статье [Управление учетными записями и квотами пакетной службы с помощью клиентской библиотеки .NET для управления пакетной службой](batch-management-dotnet.md).

Дополнительные сведения о службе Azure AD см. в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Подробные примеры, показывающие, как использовать ADAL, доступны в библиотеке [примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).


[aad_about]: ../active-directory/active-directory-whatis.md "Что такое Microsoft Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Сценарии аутентификации в Azure Active Directory"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Интеграция приложений с Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

