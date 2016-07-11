<properties 
   pageTitle="Авторизация с помощью API Resource Manager | Microsoft Azure"
   description="Руководство разработчика по авторизации с помощью API Azure Resource Manager и Active Directory для интеграции приложения со средой Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/18/2016"
   ms.author="dugill;tomfitz" />


# Руководство разработчика по авторизации с помощью API Azure Resource Manager

Если вы являетесь разработчиком программного обеспечения и хотите интегрировать приложение со средой Azure или управлять ресурсами Azure клиента, это руководство предназначено для вас. Здесь вы узнаете, как выполнить проверку подлинности с помощью интерфейсов API Azure Resource Manager.

Приложение может получить доступ к интерфейсам API Azure Resource Manager двумя способами:

1. **Доступ от имени пользователя**. Используйте этот метод для приложений, осуществляющих доступ к ресурсам от имени выполнившего вход пользователя. Этот метод подходит для приложений, например веб-приложений и программ командной строки, которые осуществляют только "интерактивное управление" ресурсами Azure.
1. **Доступ только для приложений**. Используйте этот метод, если для непосредственного доступа к ресурсам требуется удостоверение приложения. Этот метод подходит для служб управляющих программ и запланированных заданий, требующих долгосрочного "автономного доступа" к Azure.

Здесь содержатся пошаговые инструкции по созданию приложения, которое использует оба метода авторизации.

Вы создадите веб-приложение, которое обеспечивает следующие возможности:

1. Вход пользователя Azure в систему.
2. Отправку запроса к Resource Manager от имени пользователя (доступ от имени пользователя) для получения списка принадлежащих ему подписок Azure.
3. Подключение подписок к приложению. Таким образом приложение получает непосредственный доступ к подпискам.
4. Получение доступа к Resource Manager в качестве приложения для работы вне сети (доступ только для приложений).

Ниже приведены все процедуры, выполняемые приложением, которое вы создадите.

![Авторизация ARM. Регистрация приложения 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

Код, используемый в этом разделе, выполняется в качестве веб-приложения, которое можно испытать на странице [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense).

Выберите тип учетной записи для входа от имени пользователя:

![выбор входа](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

Укажите свои учетные данные.

![предоставление учетных данных](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

Предоставьте приложению доступ к своим подпискам Azure:
 
 ![Предоставление доступа](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
Подключите свои подписки к приложению для мониторинга:

![Подключение подписки](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

Отключите приложение или восстановите подключение к нему:

![Отключение подписки](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Регистрация приложения в Azure Active Directory

Сначала нужно зарегистрировать веб-приложение в Azure Active Directory (AD). При регистрации приложения для него создается центральное удостоверение в Azure AD. Оно содержит основные сведения о приложении, например идентификатор клиента OAuth, URL-адреса ответа и учетные данные, которые приложение будет использовать для проверки подлинности и доступа к интерфейсам API Azure Resource Manager. Кроме того, при регистрации приложения записываются различные делегированные разрешения, необходимые приложению при получении доступа к интерфейсам API корпорации Майкрософт от имени пользователя.

В статье [Создание приложения Active Directory и субъекта-службы с помощью портала](resource-group-create-service-principal-portal.md) содержатся все шаги, необходимые для настройки приложения. Прочтите этот раздел при создании приложения со следующими свойствами:

- Веб-приложение с именем **CloudSense**.
- URL-адрес для входа и URI идентификатора приложения в формате **http://{domain_name_of_your_directory}/{name_of_the_app}**.
- Ключ проверки подлинности для входа в приложение.
- Делегированное разрешение **Доступ к управлению службами Azure** для **API управления службами Azure**. Оставьте значение по умолчанию **Включить единый вход и читать профиль пользователя** для **Azure Active Directory**.
- Приложение с несколькими клиентами.

### Дополнительная настройка. Учетные данные сертификата

Azure AD также поддерживает учетные данные сертификата для приложения. При этом вам нужно создать самозаверяющий сертификат, сохранить закрытый ключ и добавить открытый ключ для регистрации приложения Azure AD. Для проверки подлинности приложение отправляет в Azure AD небольшой объем полезных данных, подписанных с помощью закрытого ключа, а Azure AD проверяет подпись, используя зарегистрированный открытый ключ.

Сведения о настройке сертификата см. в статье [Build service and daemon apps in Office 365](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365) (Создание приложений служб и управляющих программ в Office 365). В разделе Configuring a X.509 public cert for your application (Настройка общего сертификата X.509 для приложения) содержатся пошаговые инструкции по настройке сертификата. Кроме того, можно просмотреть статью [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md), в которой содержатся примеры настройки сертификата с помощью Azure PowerShell или интерфейса командной строки Azure.

## Проверка подлинности пользователей и получение маркера доступа

У вас есть все, что нужно для программирования приложения. В этом разделе приводятся примеры REST API и ссылки на соответствующий код C# для каждого шага процедуры. Полный пример приложения ASP.NET MVC можно получить на странице [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Сначала пользователю нужно подключить свою подписку Azure к вашему приложению.

Пользователь должен сообщить следующее:

1. **Имя домена каталога** — имя домена Azure Active Directory, связанного с подпиской Azure пользователя. На этот домен Azure AD отправляется запрос на проверку подлинности OAuth 2.0. Пользователь может узнать имя домена Azure AD, перейдя на портал Azure и выбрав учетную запись в правом верхнем углу. Пользователю можно предоставить наглядные инструкции, например такие:

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Учетная запись Майкрософт или рабочая учетная запись**. Определите, какую учетную запись пользователь использует для управления подпиской Azure: учетную запись Майкрософт (Live ID) или рабочую учетную запись (учетную запись организации). Если пользователь использует учетную запись Майкрософт, приложение перенаправит его на страницу входа Azure Active Directory с параметром строки запроса (&domain\_hint=live.com), который даст Azure AD команду перенаправления пользователя непосредственно на страницу входа в учетную запись Майкрософт. Код авторизации и токены, полученные для каждого типа учетной записи, будут обрабатываться одинаково.

Затем приложение перенаправляет пользователя в Azure AD с помощью запроса на авторизацию OAuth 2.0, применяемого для проверки подлинности учетных данных пользователя и возвращения кода авторизации. Приложение использует код авторизации, чтобы получить маркер доступа для Resource Manager.

### Запрос проверки подлинности (OAuth 2.0)

Выполните запрос на авторизацию Open ID Connect или OAuth 2.0 к конечной точке авторизации Azure AD:

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

Параметры строки запроса, доступные для этого запроса, описаны в статье [Authorization Code Grant Flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Предоставление кода авторизации).

В следующем примере показано, как запросить авторизацию OAuth 2.0:

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Служба Azure AD проверяет подлинность пользователя и при необходимости запрашивает у пользователя разрешение на доступ к приложению. Она возвращает код авторизации на URL-адрес ответа приложения. В зависимости от запрошенного параметра response\_mode Azure AD отправляет данные в строке запроса или в качестве данных POST.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Запрос проверки подлинности (Open ID Connect)

Если требуется не только получить доступ к Azure Resource Manager от имени пользователя, но и разрешить пользователю входить в ваше приложение с помощью его учетной записи Azure AD, отправьте запрос на авторизацию Open ID Connect. С помощью Open ID Connect приложение также получит токен id\_token из Azure AD, который приложение может использовать для выполнения входа пользователя.

В строке запроса на авторизацию OAuth 2.0 используются следующие параметры:

| Параметр строки запроса | Значение
|----|----
| client\_id | Идентификатор клиента приложения
| response\_mode | **form\_post** или **query**
| response\_type | **code и id\_token**
| redirect\_uri | URL-адрес ответа приложения в кодировке URL. Например, http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | Идентификатор интерфейсов API управления службами Azure в кодировке URL: https://management.core.windows.net/ |
| scope | openid и profile
| nonce | Часть данных, которая связывает запрос на авторизацию с возвращаемым токеном id\_token. Таким образом, ответ на авторизацию запрашивается, а не используется повторно
| domain\_hint | live.com <br />**Примечание**. Используйте параметр domain\_hint, только если пользователь управляет подпиской Azure с использованием учетной записи Майкрософт.
| state | При необходимости укажите любые данные о состоянии, которые Azure AD должен возвращать с ответом.

Пример запроса Open ID Connect:

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Служба Azure AD проверяет подлинность пользователя и при необходимости запрашивает у пользователя разрешение на доступ к приложению. Она возвращает код авторизации на URL-адрес ответа приложения. В зависимости от запрошенного параметра response\_mode Azure AD отправляет данные в строке запроса или в качестве данных POST.

Пример ответа Open ID Connect:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### Проверка токена id\_token

Перед выполнением входа для пользователя приложению необходимо проверить токен id\_token. Проверка токена — сложный процесс. Советуем использовать стандартную библиотеку обработчика веб-токенов JSON для платформы разработки (см. [исходный код обработчика веб-токенов JSON Azure для .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs)). Тем не менее вы отвечаете за безопасность своего приложения. Поэтому убедитесь, что библиотека, используемая для обработки токена id\_token, должным образом проверяет следующие свойства:

- **Время создания токена**. Проверьте утверждения nbf и exp, чтобы убедиться, что токен не новый и не устаревший. Обычно нужно немного подождать (5 минут), чтобы устранить разницу во времени.
- **Издатель**. Проверьте утверждение iss, чтобы убедиться, что издателем токена выступает Azure Active Directory: https://sts.windows.net/{tenant_id_of_the_directory}
- **Аудитория**. Проверьте утверждение aud, чтобы убедиться, что токен создан для вашего приложения. В качестве значения должен использоваться идентификатор клиента приложения.
- **Nonce**. Проверьте утверждение nonce относительно данных nonce, отправленных в запросе на авторизацию, и убедитесь, что приложение затребовало ответ и что токен не используется повторно.
- **Подпись**. Приложение должно проверить, подписан ли токен Azure Active Directory. Azure AD часто выполняет подписку ключей, поэтому приложению нужно запрашивать обновленные ключи ежедневно или отклонять их при сбое проверки подписи. Дополнительные сведения см. в статье [Важная информация о смене ключей подписывания в Azure AD](active-directory/active-directory-signing-key-rollover.md).

После проверки **id\_token** используйте значение утверждения oid в качестве неизменяемого и однократно используемого идентификатора пользователя. Используйте утверждение **unique\_name** или upn/email в качестве понятного отображаемого имени пользователя. Кроме того, для отображения можно воспользоваться утверждениями given\_name и family\_name.

### Запрос токена (предоставление кода OAuth 2.0)

Теперь, когда приложение получило код авторизации из Azure AD, необходимо получить маркер доступа для Azure Resource Manager. Выполните запрос на токен предоставления кода OAuth 2.0 к конечной точке токена Azure AD с помощью метода POST:

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Параметры строки запроса, доступные для этого запроса, описаны в статье [Authorization Code Grant Flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Предоставление кода авторизации).

В следующем примере показан запрос на токен предоставления кода с использованием пароля:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

При работе с учетными данными сертификата создайте веб-токен JSON (JWT) и подпишите его (RSA-SHA256) с помощью закрытого ключа сертификата приложения. Типы утверждений для токена содержатся в статье [Authorization Code Grant Flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Предоставление кода авторизации). Справочную информацию по подписыванию веб-токенов JSON утверждений клиента см. на странице с [кодом библиотеки проверки подлинности Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs).

Дополнительные сведения о проверке подлинности клиента см. на странице с [характеристиками Open ID Connect](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). Пример веб-токена JSON утверждений клиента находится [здесь](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA).

В следующем примере показан запрос на токен предоставления кода с использованием учетных данных сертификата:

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Пример ответа для токена предоставления кода:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### Обработка ответа токена предоставления кода

Успешный ответ токена будет содержать маркер доступа (при доступе от имени пользователя) для Azure Resource Manager. Приложение использует этот маркер для доступа к Resource Manager от имени пользователя. Срок действия маркеров доступа, выданных Azure AD, составляет один час. Маловероятно, что веб-приложению понадобится обновить маркер доступа (при доступе от имени пользователя). Однако, если обновление потребуется, можно воспользоваться токеном обновления, который приложение получает в ответе маркера. Выполните запрос токена OAuth 2.0 к конечной точке токена Azure AD с помощью метода POST:

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

Параметры для запроса обновления описаны в статье [Authorization Code Grant Flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Предоставление кода авторизации).

В следующем примере показано, как использовать токен обновления:

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Обратите внимание, что хотя с помощью токенов обновления и можно получать новые маркеры доступа для Azure Resource Manager, они не подходят для автономного доступа приложения. Срок действия токенов обновления ограничен, и они привязаны к пользователю. Если пользователь покидает организацию, приложение, использующее токен обновления, потеряет доступ. Этот способ не подходит для приложений, используемых группами для управления ресурсами Azure.

## Предоставление списка доступных подписок

Теперь у приложения есть маркер доступа к Azure Resource Manager от имени пользователя.

Следующий шаг — разрешить пользователю подключать свою подписку Azure к вашему приложению, чтобы оно могло управлять подписками, даже если пользователь отсутствует (долгосрочный автономный доступ). Покажите пользователю список подписок Azure, в которых он может управлять доступом, и разрешите ему назначить роли RBAC непосредственно удостоверению вашего приложения.

![Авторизация ARM. Пример интерфейса приложения 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### Предоставление списка подписок, в которых у пользователя есть право доступа

Сначала нужно вызвать API [подписок в списке Resource Manager](https://msdn.microsoft.com/library/azure/dn790531.aspx), чтобы получить список всех подписок, в которых у пользователя есть какие-либо права доступа. Затем мы определим те, в которых пользователь может управлять доступом.

Этот вызов осуществляет метод [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) примера приложения ASP.NET MVC.

Пример запроса для вывода списка подписок:

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Пример ответа на вывод списка подписок:

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### Получение разрешений пользователя для подписки

Действие подключения и отключения должно отображаться только для подписок, доступом в которых пользователь может управлять. Для каждой подписки потребуется вызвать API [разрешений списка Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx), чтобы определить, есть ли у пользователя права управления доступом для подписки.

Этот вызов осуществляет метод [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) примера приложения ASP.NET MVC.

В следующем примере показано, как запросить разрешения пользователя для подписки. 83cfe939-2402-4581-b761-4f59b0a041e4 — это идентификатор подписки.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Пример ответа на получение разрешений пользователя на подписку:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

API разрешений возвращает несколько разрешений. Каждое разрешение состоит из разрешенных действий (actions) и запрещенных действий (notActions). Если действие есть в списке разрешенных действий любого разрешения и отсутствует в списке запрещенных действий этого же разрешения, пользователь может его выполнять. **microsoft.authorization/roleassignments/write** — действие, предоставляющее права на управление доступом. Приложение должно проанализировать результат разрешений, чтобы найти соответствующее регулярное выражение в этой строке действия в разрешенных и запрещенных действиях всех разрешений.

### Предоставление списка каталогов, в которых находится учетная запись пользователя (необязательно)

Учетная запись пользователя может находиться в нескольких каталогах Azure Active Directory. Вполне возможно, что пользователь изначально указал неправильное имя каталога. В этом случае нужная подписка не отобразится в списке.

С помощью API [клиентов списка Resource Manager](https://msdn.microsoft.com/library/azure/dn790536.aspx) можно получить список идентификаторов всех каталогов, в которых есть учетная запись пользователя. Можно вызвать API, чтобы определить, входит ли учетная запись пользователя в несколько каталогов, и при необходимости отобразить для пользователя такое сообщение: "Не удалось найти нужную подписку? Она может быть в другом вашем каталоге Azure Active Directory. Щелкните здесь, чтобы перейти в другой каталог".

Этот вызов осуществляет метод [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) примера приложения ASP.NET MVC.

Пример запроса для вывода списка каталогов:

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

Пример ответа на вывод списка каталогов:

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## Подключение подписки к приложению

Теперь у вас есть список подписок Azure, которые пользователь может подключить к приложению. Следующий шаг — дать пользователю команду создать подключение. Когда пользователь устанавливает **подключение**, приложение выполняет следующие действия.

1. Назначает удостоверению приложения в подписке соответствующую роль RBAC.
2. Проверяет назначение доступа, запрашивая разрешение приложения для подписки или получив доступ к Resource Manager с помощью маркера только для приложений.
1. Записывает подключение в структуре данных подключенных подписок приложения, сохраняя идентификатор подписки.

Давайте подробнее рассмотрим первый шаг. Чтобы назначить соответствующую роль RBAC удостоверению приложения, необходимо определить следующее:

- Идентификатор объекта для удостоверения приложения в каталоге Azure Active Directory пользователя.
- Идентификатор роли RBAC, требуемой приложению для подписки.

Теперь разберемся в первой части. Когда приложение впервые проверяет подлинность пользователя из Azure AD, в этом экземпляре Azure AD создается объект субъекта-службы для приложения. Azure позволяет назначать роли RBAC субъектам-службам, чтобы предоставить прямой доступ к соответствующим приложениям для ресурсов Azure. Нам нужно сделать именно это. Поэтому сначала мы отправляем запрос к API Graph Azure AD, чтобы определить идентификатор объекта субъекта-службы приложения в подключенном экземпляре Azure AD пользователя.

У вас есть только маркер доступа для Azure Resource Manager. Поэтому вам понадобится новый маркер доступа для вызова API Graph Azure AD. У каждого приложения в Azure AD есть разрешение на запрос собственного объекта субъекта-службы. Поэтому нам не нужен маркер доступа от имени пользователя. Маркера доступа только для приложений будет достаточно.

<a id="app-azure-ad-graph">
### Получение маркера доступа только для приложений для API Graph Azure AD

Чтобы выполнить проверку подлинности приложения и получить токен для API Graph Azure AD, выполните запрос на токен предоставления учетных данных клиента OAuth 2.0 к конечной точке токена Azure AD (**https://login.microsoftonline.com/{directory\_domain\_name}/OAuth2/Token**).

В строках 73–77 метода [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) пример приложения ASP.NET MVC получает маркер доступа для API Graph (только для приложений) с использованием библиотеки проверки подлинности Active Directory для .NET.

Данные запроса на токен предоставления учетных данных клиента:

| Элемент | Значение
|----|----
| grant\_type | **client\_credentials**
| client\_id | Идентификатор клиента приложения
| resource | Идентификатор с кодировкой URL ресурса, для которого запрашивается маркер доступа. В этом случае идентификатор API Azure AD Graph выглядит так: **https://graph.windows.net/**
| client\_secret или client\_assertion\_type + client\_assertion | Если приложение использует пароль, используйте client\_secret. Если приложение использует сертификат, используйте client\_assertion.

Пример запроса на токен предоставления учетных данных клиента:

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Пример ответа токена предоставления учетных данных клиента:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### Получение идентификатора объекта субъекта-службы приложения в Azure AD пользователя

Теперь используйте маркер доступа только для приложений для запроса API [субъектов-служб Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ServicePrincipalEntity), чтобы определить идентификатор объекта субъекта-службы приложения в каталоге.

Этот вызов осуществляет метод [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) примера приложения ASP.NET MVC.

В следующем примере показано, как запросить субъект-службу приложения. a0448380-c346-4f9f-b897-c18733de9394 — это идентификатор клиента приложения.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

В следующем примере показан ответ на запрос субъекта-службы приложения.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Получение идентификатора роли RBAC Azure

Необходимо назначить соответствующую роль RBAC субъекту-службе приложения для выбранной подписки. Для этого нужно определить идентификатор роли RBAC Azure.

Выбор роли RBAC для приложения:

- Если приложение только выполняет мониторинг подписки и не вносит никаких изменений, ему требуются разрешения только на чтение для подписки. Назначьте роль **Читатель**.
- Если приложение управляет подпиской Azure, а также создает, изменяет или удаляет сущности, ему понадобится разрешение участника.
  - Для управления определенным типом ресурсов назначьте роли участников для конкретных ресурсов (участник виртуальных машин, участник виртуальных сетей, участник учетных записей хранения и т. д.).
  - Для управления ресурсами любого типа назначьте роль **Участник**.

Роль, назначенная для приложения, будет видна пользователям, поэтому выбирайте наименее востребованные привилегии.

Вызовите [API определения ролей Resource Manager](https://msdn.microsoft.com/library/azure/dn906879.aspx), чтобы получить список всех ролей RBAC Azure и перебрать результаты. Это нужно, чтобы найти определение требуемой роли по имени.

Этот вызов осуществляет метод [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) примера приложения ASP.NET MVC.

В следующем примере запроса показано, как получить идентификатор роли RBAC Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb — это идентификатор подписки.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Запрос имеет следующий формат:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Обратите внимание, что не нужно вызывать этот API постоянно. Определив известный GUID определения роли, можно создать идентификатор определения роли следующего вида:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Ниже представлены известные идентификаторы GUID часто используемых встроенных ролей:

| Роль | Guid |
| ----- | ------ |
| читатель. | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Участник | b24988ac-6180-42a0-ab88-20f7382dd24c
| Участник виртуальной машины | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Участник виртуальной сети | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Участник учетной записи хранения | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Участник веб-сайта | de139f84-1756-47ae-9be6-808fbbe84772
| Участник веб-плана | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| Участник SQL Server | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Участник БД SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### Назначение роли RBAC для приложения

Теперь все готово, чтобы назначить соответствующую роль RBAC субъекту-службе приложения для выбранной подписки с помощью API [создания назначения роли Resource Manager](https://msdn.microsoft.com/library/azure/dn906887.aspx).

Этот вызов осуществляет метод [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) примера приложения ASP.NET MVC.

Пример запроса на назначение роли RBAC для приложения:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

В запросе используются следующие значения:

| Guid | Описание |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | Идентификатор подписки
| c3097b31-7309-4c59-b4e3-770f8406bad2 | Идентификатор объекта субъекта-службы приложения
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | Известный GUID роли модуля чтения RBAC
| 4f87261d-2816-465d-8311-70a27558df4c | Новый GUID, созданный для нового назначения роли

Запрос имеет следующий формат:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Получение маркера доступа только для приложений для Azure Resource Manager

Далее необходимо проверить, есть ли у приложения требуемый доступ для подписки. Для этого следует выполнить тестовое задание для подписки, используя маркер только для приложений для Azure Resource Manager. В рамках тестового задания следует проверить, действительно ли у приложения есть необходимый доступ для подписки, который позволит выполнять мониторинг и управление в автономном режиме.

Чтобы получить маркер доступа только для приложений для Azure Resource Manager, следуйте инструкциям из раздела [Получение маркера доступа только для приложений для API Graph Azure AD](#app-azure-ad-graph), но используйте другое значение для параметра ресурсов:

    https://management.core.windows.net/

В строках 210–214 метода [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) пример приложения ASP.NET MVC получает маркер доступа для Azure Resource Manager (только для приложений) с использованием библиотеки проверки подлинности Active Directory для .NET.

#### Получение разрешений приложений для подписки

Чтобы проверить, есть ли у приложения необходимый доступ на подписку Azure, можно также вызвать API [разрешений Resource Manager](https://msdn.microsoft.com/library/azure/dn906889.aspx) (который используется, чтобы определить наличие у пользователя права на управление доступом для подписки). Однако на этот раз вызовите API разрешений с помощью маркера доступа только для приложений, полученного на предыдущем шаге.

Этот вызов осуществляет метод [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) примера приложения ASP.NET MVC.

## Управление подключенными подписками

После назначения соответствующей роли RBAC субъекту-службе приложения для подписки приложение может и дальше выполнять мониторинг и управление с помощью маркеров доступа только для приложений для Azure Resource Manager.

Если владелец подписки удаляет назначение роли приложения, используя портал управления Azure или программы командной строки, приложение больше не сможет получить доступ к этой подписке. В этом случае следует уведомить пользователей, что подключение к подписке прервано вне приложения, и предоставить им возможность восстановить его. При восстановлении назначение роли, удаленное в автономном режиме, будет создано заново.


## Отключение подписок

Помимо предоставления возможности подключения подписок к вашему приложению, пользователю также необходимо разрешить отключать подписки. С точки зрения управления доступом при отключении удаляется назначение роли субъекта-службы приложения для подписки. Кроме того, любое состояние в приложении для подписки может быть удалено. Отключить подписку могут только пользователи с разрешением на управление доступом для этой подписки.

Этот вызов осуществляет метод [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303) примера приложения ASP.NET MVC.

Теперь пользователи могут легко подключать подписки Azure к приложениям и управлять ими с помощью этих подписок.

<!---HONumber=AcomDC_0629_2016-->