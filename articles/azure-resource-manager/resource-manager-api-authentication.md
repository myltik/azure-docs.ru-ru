---
title: Проверка подлинности Azure Active Directory и Resource Manager | Документы Майкрософт
description: Руководство разработчика по проверке подлинности с помощью API Azure Resource Manager и Azure Active Directory для интеграции приложения с другими подписками Azure.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2017
ms.author: dugill
ms.openlocfilehash: 1dea8d173432b05a72de72e8b17db4c97ea7924d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359868"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Использование API аутентификации Resource Manager для доступа к подпискам
## <a name="introduction"></a>Введение
Если вы разрабатываете программное обеспечение и хотите создать приложение для управления ресурсами Azure клиента, эта статья предназначена для вас. Здесь вы узнаете, как выполнить проверку подлинности с помощью API-интерфейсов Azure Resource Manager и получить доступ к ресурсам в других подписках.

Приложение может получить доступ к интерфейсам API Azure Resource Manager двумя способами:

1. **Доступ от имени пользователя.** Используйте этот метод для приложений, осуществляющих доступ к ресурсам от имени выполнившего вход пользователя. Этот метод подходит для приложений, например веб-приложений и программ командной строки, которые осуществляют только интерактивное управление ресурсами Azure.
2. **Доступ только для приложений.** Используйте этот метод для приложений, на которых запущены службы управляющих программ и запланированные задания. Удостоверение приложения предоставляет непосредственный доступ к ресурсам. Этот метод подходит для приложений, требующих долгосрочного автономного (автоматического) доступа Azure.

В этой статье содержатся пошаговые инструкции по созданию приложения, поддерживающего оба эти метода авторизации. Здесь показано, как выполнить каждое действие, используя REST API или C#. Полное приложение ASP.NET MVC можно получить на странице [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>Возможности веб-приложения
Веб-приложение обеспечивает следующие возможности:

1. Вход пользователя Azure в систему.
2. Отправка запроса к пользователю на предоставление веб-приложению доступа к Resource Manager.
3. Получение маркера доступа от имени пользователя для доступа к Resource Manager.
4. Назначение субъекта-службы приложения для роли в подписке с помощью маркера (из шага 3). На этом шаге приложению предоставляется долгосрочный доступ к подписке.
5. Получение маркера доступа только для приложений.
6. Управление ресурсами в подписке через Resource Manager с помощью маркера (полученного на шаге 5).

Ниже приведены процедуры, выполняемые веб-приложением.

![Поток проверки подлинности Resource Manager](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Укажите идентификатор используемой подписки от имени пользователя:

![Указание идентификатора подписки](./media/resource-manager-api-authentication/sample-ux-1.png)

Выберите учетную запись для входа в систему.

![выбор учетной записи](./media/resource-manager-api-authentication/sample-ux-2.png)

Укажите свои учетные данные.

![предоставление учетных данных](./media/resource-manager-api-authentication/sample-ux-3.png)

Предоставьте приложению доступ к своим подпискам Azure.

![Предоставление доступа](./media/resource-manager-api-authentication/sample-ux-4.png)

Управляйте своими подключенными подписками.

![Подключение подписки](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Регистрация приложения
Прежде чем приступить к программированию, сначала нужно зарегистрировать веб-приложение в Azure Active Directory (AD). При регистрации приложения для него создается центральное удостоверение в Azure AD. Оно содержит основные сведения о приложении, например идентификатор клиента OAuth, URL-адреса ответа и учетные данные, используемые приложением для проверки подлинности и доступа к интерфейсам API Azure Resource Manager. Кроме того, при регистрации приложения записываются различные делегированные разрешения, необходимые при получении доступа к интерфейсам API корпорации Майкрософт от имени пользователя.

Так как приложение получает доступ к другой подписке, его необходимо настроить как мультитенантное. Чтобы пройти проверку, укажите домен, связанный с Azure Active Directory. Сведения о доменах, связанных с Azure Active Directory, можно просмотреть на портале.

В приведенном ниже примере показано, как зарегистрировать приложение с помощью Azure PowerShell. Эта команда поддерживается только в Azure PowerShell последней версии (обновление от августа 2016 г.).

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Для входа в систему в качестве приложения AD необходимо указать идентификатор приложения и пароль. Чтобы просмотреть идентификатор приложения, который возвращается в результате выполнения предыдущей команды, используйте следующую команду:

    $app.ApplicationId

В приведенном ниже примере показано, как зарегистрировать приложение с помощью Azure CLI.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Выходные данные содержат идентификатор приложения, необходимый для проверки подлинности в качестве приложения.

### <a name="optional-configuration---certificate-credential"></a>Дополнительная настройка. Учетные данные сертификата
Azure AD также поддерживает учетные данные сертификата для приложения. При этом вам нужно создать самозаверяющий сертификат, сохранить закрытый ключ и добавить открытый ключ для регистрации приложения Azure AD. Для проверки подлинности приложение отправляет в Azure AD небольшой объем полезных данных, подписанных с помощью закрытого ключа, а Azure AD проверяет подпись, используя зарегистрированный открытый ключ.

Дополнительные сведения о создании приложения AD с использованием сертификата см. в статье [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурса](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) или [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Получение идентификатора клиента из идентификатора подписки
Чтобы получить маркер, используемый для вызова Resource Manager, в приложении необходимо указать идентификатор клиента Azure AD, в котором находится подписка Azure. Скорее всего, пользователи знают идентификаторы подписок, но идентификаторы клиентов Azure Active Directory знает не каждый. Чтобы получить идентификатор клиента, необходимо знать идентификатор подписки пользователя. Укажите этот идентификатор подписки при отправке запроса о подписке.

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Запрос завершится сбоем, так как пользователь еще не вошел в систему, но идентификатор клиента можно получить из ответа. В этом исключении идентификатор клиента указан в заголовке ответа **WWW-Authenticate**. Эта реализация используется в методе [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Получение маркера доступа от имени пользователя
Приложение перенаправляет пользователя в Azure AD с помощью запроса на авторизацию OAuth 2.0, применяемого для проверки подлинности учетных данных пользователя и возвращения кода авторизации. Приложение использует код авторизации, чтобы получить маркер доступа для Resource Manager. Метод [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) создает запрос на авторизацию.

В этой статье приводятся примеры запросов REST API, используемых для проверки подлинности пользователя. Чтобы реализовать проверку подлинности в коде, можно также использовать вспомогательные библиотеки. Дополнительные сведения об этих библиотеках см. в статье [Библиотеки проверки подлинности Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Рекомендации по интеграции функции управления удостоверениями в приложение см. в статье [Руководство разработчика по Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Запрос проверки подлинности (OAuth 2.0)
Выполните запрос на авторизацию Open ID Connect или OAuth 2.0 к конечной точке авторизации Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Параметры строки запроса, доступные для этого запроса, описаны в разделе [Запрос кода авторизации](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code).

В следующем примере показано, как запросить авторизацию OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Служба Azure AD проверяет подлинность пользователя и при необходимости запрашивает у пользователя разрешение на доступ к приложению. Она возвращает код авторизации на URL-адрес ответа приложения. В зависимости от запрошенного параметра response_mode Azure AD отправляет данные в строке запроса или в качестве данных POST.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Запрос проверки подлинности (Open ID Connect)
Если требуется не только получить доступ к Azure Resource Manager от имени пользователя, но и разрешить пользователю входить в ваше приложение с помощью его учетной записи Azure AD, отправьте запрос на авторизацию Open ID Connect. С помощью Open ID Connect приложение также получит маркер id_token из Azure AD, используемый приложением для выполнения входа пользователя.

Параметры строки запроса, доступные для этого запроса, описаны в разделе [Отправка запроса на вход](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request).

Пример запроса Open ID Connect:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Служба Azure AD проверяет подлинность пользователя и при необходимости запрашивает у пользователя разрешение на доступ к приложению. Она возвращает код авторизации на URL-адрес ответа приложения. В зависимости от запрошенного параметра response_mode Azure AD отправляет данные в строке запроса или в качестве данных POST.

Пример ответа Open ID Connect:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Запрос токена (предоставление кода OAuth 2.0)
Теперь, когда приложение получило код авторизации из Azure AD, необходимо получить маркер доступа для Azure Resource Manager.  Выполните запрос на токен предоставления кода OAuth 2.0 к конечной точке токена Azure AD с помощью метода POST:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Параметры строки запроса, доступные для этого запроса, описаны в разделе [Использование кода авторизации для запроса маркера доступа](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

В следующем примере показан запрос на токен предоставления кода с использованием пароля:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

При работе с учетными данными сертификата создайте веб-токен JSON (JWT) и подпишите его (RSA-SHA256) с помощью закрытого ключа сертификата приложения. Типы утверждений для маркера содержатся в разделе [Утверждения JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Справочную информацию по подписыванию маркеров JWT утверждений клиента см. на странице с [кодом библиотеки аутентификации Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs).

Дополнительные сведения об аутентификации клиента см. на странице [характеристик Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication).

В следующем примере показан запрос на токен предоставления кода с использованием учетных данных сертификата:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Пример ответа для токена предоставления кода:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Обработка ответа токена предоставления кода
Успешный ответ будет содержать маркер доступа (при доступе от имени пользователя) для Azure Resource Manager. Приложение использует этот маркер для доступа к Resource Manager от имени пользователя. Срок действия маркеров доступа, выданных Azure AD, составляет один час. Маловероятно, что веб-приложению понадобится обновить маркер доступа (при доступе от имени пользователя). Однако если обновление потребуется, можно воспользоваться маркером обновления, который приложение получает в ответе маркера. Выполните запрос токена OAuth 2.0 к конечной точке токена Azure AD с помощью метода POST:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Параметры для запроса обновления описаны в разделе [Обновление маркеров доступа](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

В следующем примере показано, как использовать токен обновления:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Хотя с помощью маркеров обновления и можно получать новые маркеры доступа для Azure Resource Manager, они не подходят для автономного доступа приложения. Срок действия токенов обновления ограничен, и они привязаны к пользователю. Если пользователь покидает организацию, приложение, использующее маркер обновления, теряет доступ. Этот способ не подходит для приложений, используемых группами для управления ресурсами Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Проверка назначения пользователем доступа к подписке
Теперь у приложения есть маркер доступа к Azure Resource Manager от имени пользователя. Далее нужно подключить приложение к подписке, чтобы оно могло управлять подписками, даже если пользователь отсутствует (долгосрочный автономный доступ).

Для подключения каждой подписки необходимо вызвать API [разрешений списка Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions) , чтобы определить, есть ли у пользователя права управления доступом для подписки.

Этот вызов осуществляет метод [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) примера приложения ASP.NET MVC.

В следующем примере показано, как запросить разрешения пользователя для подписки. 83cfe939-2402-4581-b761-4f59b0a041e4 — это идентификатор подписки.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Пример ответа на получение разрешений пользователя на подписку:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

API разрешений возвращает несколько разрешений. Каждое разрешение состоит из разрешенных действий (**actions**) и запрещенных действий (**notActions**). Если действие есть в списке разрешенных действий любого разрешения и отсутствует в списке запрещенных действий этого же разрешения, пользователь может его выполнять. **microsoft.authorization/roleassignments/write** — действие, предоставляющее права на управление доступом. Приложение должно проанализировать результат разрешений, чтобы найти соответствующее регулярное выражение в этой строке действия в разрешенных (**actions**) и запрещенных (**notActions**) действиях всех разрешений.

## <a name="get-app-only-access-token"></a>Получение маркера доступа только для приложений
Теперь вы знаете, что пользователь может назначить доступ к подписке Azure. Теперь нужно сделать следующее:

1. Назначить удостоверению приложения в подписке соответствующую роль RBAC.
2. Проверить назначение доступа, запрашивая разрешение приложения для подписки или получив доступ к Resource Manager с помощью маркера только для приложений.
3. Записать подключение в структуре данных подключенных подписок приложения, сохраняя идентификатор подписки.

Давайте рассмотрим первый шаг. Чтобы назначить соответствующую роль RBAC удостоверению приложения, необходимо определить следующее:

* Идентификатор объекта для удостоверения приложения в каталоге Azure Active Directory пользователя.
* Идентификатор роли RBAC, требуемой приложению для подписки.

Когда приложение впервые проверяет подлинность пользователя из Azure AD, в этом экземпляре Azure AD создается объект субъекта-службы для приложения. Azure позволяет назначать роли RBAC субъектам-службам, чтобы предоставить прямой доступ к соответствующим приложениям для ресурсов Azure. Нам нужно сделать именно это. Отправьте запрос к API Graph Azure AD, чтобы определить идентификатор объекта субъекта-службы приложения в подключенном экземпляре Azure AD пользователя.

У вас есть только маркер доступа для Azure Resource Manager. Поэтому вам понадобится новый маркер доступа для вызова API Graph Azure AD. У каждого приложения в Azure AD есть разрешение на запрос собственного объекта субъекта-службы. Поэтому нам достаточно маркера доступа только для приложений.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Получение маркера доступа только для приложений для API Graph Azure AD
Чтобы аутентифицировать приложение и получить маркер для API Graph Azure AD, выполните запрос маркера для предоставления учетных данных клиента OAuth 2.0 к конечной точке токена Azure AD (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

Метод [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) примера приложения ASP.NET MVC получает маркер доступа только для приложений для API Graph с использованием библиотеки проверки подлинности Active Directory для .NET.

Параметры строки запроса, доступные для этого запроса, описаны в разделе [Запрос маркера доступа](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token).

Пример запроса на токен предоставления учетных данных клиента:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Пример ответа токена предоставления учетных данных клиента:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Получение идентификатора объекта субъекта-службы приложения в Azure AD пользователя
Теперь используйте маркер доступа, предназначенный только для приложений, для запроса API [субъектов-служб Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity), чтобы определить идентификатор объекта субъекта-службы приложения в каталоге.

Этот вызов осуществляет метод [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) примера приложения ASP.NET MVC.

В следующем примере показано, как запросить субъект-службу приложения. a0448380-c346-4f9f-b897-c18733de9394 — это идентификатор клиента приложения.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

В следующем примере показан ответ на запрос субъекта-службы приложения.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Получение идентификатора роли RBAC Azure
Чтобы назначить соответствующую роль RBAC для субъекта-службы, необходимо определить идентификатор роли RBAC Azure.

Выбор роли RBAC для приложения:

* Если приложение только выполняет мониторинг подписки и не вносит никаких изменений, ему требуются разрешения только на чтение для подписки. Назначьте роль **Читатель** .
* Если приложение управляет подпиской Azure, а также создает, изменяет или удаляет сущности, ему понадобится разрешение участника.
  * Для управления определенным типом ресурсов назначьте роли участников для конкретных ресурсов (участник виртуальных машин, участник виртуальных сетей, участник учетных записей хранения и т. д.).
  * Для управления ресурсами любого типа назначьте роль **Участник** .

Роль, назначенная для приложения, видна пользователям, поэтому выбирайте наименее востребованные привилегии.

Вызовите [API определения ролей Resource Manager](https://docs.microsoft.com/rest/api/authorization/roledefinitions), чтобы получить список всех ролей RBAC Azure и перебрать результаты. Это нужно, чтобы найти определение требуемой роли по имени.

Этот вызов осуществляет метод [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) примера приложения ASP.NET MVC.

В следующем примере запроса показано, как получить идентификатор роли RBAC Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb — это идентификатор подписки.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Запрос имеет следующий формат:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Этот API не нужно вызывать постоянно. Узнав известный GUID определения роли, можно создать идентификатор определения роли следующего вида:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Ниже представлены идентификаторы часто используемых встроенных ролей:

| Роль | GUID |
| --- | --- |
| Читатель |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| участник; |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Участник виртуальной машины |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Участник виртуальной сети |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Участник учетной записи хранения |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Участник веб-сайта |de139f84-1756-47ae-9be6-808fbbe84772 |
| Участник веб-плана |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Участник SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Участник БД SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Назначение роли RBAC для приложения
Теперь все готово, чтобы назначить соответствующую роль RBAC субъекту-службе с помощью API [создания назначения роли Resource Manager](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

Этот вызов осуществляет метод [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) примера приложения ASP.NET MVC.

Пример запроса на назначение роли RBAC для приложения:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

В запросе используются следующие значения:

| Guid | ОПИСАНИЕ |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |Идентификатор подписки |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |Идентификатор объекта субъекта-службы приложения |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |Идентификатор роли читателя |
| 4f87261d-2816-465d-8311-70a27558df4c |Новый GUID, созданный для нового назначения роли |

Запрос имеет следующий формат:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Получение маркера доступа только для приложений для Azure Resource Manager
Чтобы проверить, есть ли у приложения требуемый доступ для подписки, выполните тестовое задание для подписки, используя маркер только для приложений.

Маркер доступа только для приложений можно получить, следуя инструкциям в разделе [Получение маркера доступа только для приложений для API Graph Azure AD](#app-azure-ad-graph). Но используйте другое значение для параметра ресурсов.

    https://management.core.windows.net/

Метод [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) примера приложения ASP.NET MVC получает маркер доступа только для приложений для Azure Resource Manager с использованием библиотеки проверки подлинности Active Directory для .NET.

#### <a name="get-applications-permissions-on-subscription"></a>Получение разрешений приложения для подписки
Чтобы проверить, есть ли у приложения необходимый доступ к подписке Azure, можно также вызвать API [разрешений Resource Manager](https://docs.microsoft.com/rest/api/authorization/permissions). Этот метод аналогичен методу определения наличия у пользователя права на управление доступом для подписки. Однако на этот раз вызовите API разрешений с помощью маркера доступа только для приложений, полученного на предыдущем шаге.

Этот вызов осуществляет метод [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) примера приложения ASP.NET MVC.

## <a name="manage-connected-subscriptions"></a>Управление подключенными подписками
После назначения соответствующей роли RBAC субъекту-службе приложения для подписки приложение может и дальше выполнять мониторинг и управление с помощью маркеров доступа только для приложений для Azure Resource Manager.

Если владелец подписки удалит назначение роли приложения с помощью портала или программ командной строки, приложение больше не сможет получить доступ к этой подписке. В этом случае следует уведомить пользователей, что подключение к подписке прервано вне приложения, и предоставить им возможность восстановить его. При восстановлении назначение роли, удаленное в автономном режиме, будет создано заново.

Помимо предоставления возможности подключения подписок к вашему приложению, пользователю также необходимо разрешить отключать подписки. С точки зрения управления доступом при отключении удаляется назначение роли субъекта-службы приложения для подписки. Кроме того, любое состояние в приложении для подписки может быть удалено.
Отключить подписку могут только пользователи с разрешением на управление доступом для этой подписки.

Этот вызов осуществляет метод [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) примера приложения ASP.NET MVC.

Теперь пользователи могут легко подключать подписки Azure к приложениям и управлять ими с помощью этих подписок.
