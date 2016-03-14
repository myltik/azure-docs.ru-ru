<properties
   pageTitle="Регистрация и адаптация клиентов в мультитенантных приложениях | Microsoft Azure"
   description="Адаптация клиентов в мультитенантном приложении"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Регистрация и адаптация клиентов в мультитенантном приложении

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

В этой статье описывается, как реализовать процесс _регистрации_ приложения в мультитенантном приложении, чтобы клиент смог зарегистрировать свою организацию для работы с приложением. Существует несколько причин для реализации процесса регистрации:

-	Предоставление администраторам AD возможности давать согласие на использование приложения во всей организации клиента.
-	Сбор данных об оплате кредитной картой или других сведений о клиенте.
-	Выполнение однократной настройки каждого клиента, необходимой приложению.

## Согласие администратора и разрешения Azure AD

Чтобы проверять подлинность в Azure AD, приложению требуется доступ к каталогу пользователя. Как минимум, приложению требуется разрешение на чтение профиля пользователя. При первом входе пользователя в Azure AD будет отображена страница согласия со списком запрашиваемых разрешений. Нажав кнопку **Принять**, пользователь предоставляет разрешение приложению.

По умолчанию согласие предоставляется на уровне пользователя. Страницу согласия видит каждый пользователь, выполняющий вход. Однако Azure AD также поддерживает _согласие администратора_, позволяющее администраторам AD давать согласие на использование приложения в рамках всей организации.

Когда используется поток согласия администратора, на странице согласия отображается сообщение о том, что администратор AD предоставляет разрешение от имени всего клиента.

![Запрос на согласие администратора](media/guidance-multitenant-identity/admin-consent.png)

После того как администратор нажмет кнопку **Принять**, другие пользователи в рамках того же клиента смогут выполнять вход, а Azure AD будет пропускать экран согласия.

Предоставлять согласие администратора может только администратор AD, поскольку он дает разрешение от имени всей организации. Если проверку подлинности в потоке согласия пытается пройти пользователь без прав администратора, в Azure AD выводится сообщение об ошибке.

![Ошибка согласия](media/guidance-multitenant-identity/consent-error.png)

Если приложению позднее потребуются дополнительные разрешения, клиенту будет нужно повторно зарегистрироваться и согласиться с обновленными разрешениями.

## Реализация регистрации клиента

Для приложения [Tailspin Surveys][Tailspin] мы определили несколько требований к процессу регистрации:

-	Клиент должен пройти регистрацию до того, как пользователи смогут выполнять вход.
-	В процессе регистрации используется поток согласия администратора.
-	При регистрации клиент пользователя добавляется в базу данных приложения.
-	После регистрации клиента в приложении отображается страница адаптации.

В этом разделе мы рассмотрим реализацию процесса регистрации. Важно понимать, что понятия "регистрация" и "вход" формируют концепцию приложения. Во время потока проверки подлинности службе Azure AD изначально неизвестно, что пользователь находится в процессе регистрации. Отслеживанием контекста занимается приложение.

Когда анонимный пользователь открывает страницу приложения Surveys, он видит две кнопки — одну для входа и одну для регистрации организации (регистрация).

![Страница регистрации в приложении](media/guidance-multitenant-identity/sign-up-page.png)

Эти кнопки позволяют вызывать действия в классе [AccountController].

Действие `SignIn` возвращает **ChallegeResult**, в результате чего ПО промежуточного слоя OpenID Connect выполняет перенаправление в конечную точку проверки подлинности. Это заданный по умолчанию способ запуска проверки подлинности в ASP.NET Core 1.0.

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Теперь сравните действие `SignUp`:

```csharp
[AllowAnonymous]
public IActionResult SignUp()
{
    // Workaround for https://github.com/aspnet/Security/issues/546
    HttpContext.Items.Add("signup", "true");

    var state = new Dictionary<string, string> { { "signup", "true" }};
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties(state)
        {
            RedirectUri = Url.Action(nameof(SignUpCallback), "Account")
        });
}
```

Как и `SignIn`, действие `SignUp` также возвращает `ChallengeResult`. Но на этот раз мы добавляем в `AuthenticationProperties` в `ChallengeResult` фрагмент сведений о состоянии:

-	signup: логический флаг. Если он установлен, значит, пользователь начал процесс регистрации.

Сведения о состоянии в `AuthenticationProperties` добавляются в параметр [state] OpenID Connect для круговых обходов во время потока проверки подлинности.

![Параметр состояния](media/guidance-multitenant-identity/state-parameter.png)

После того как пользователь пройдет проверку подлинности в Azure AD и будет перенаправлен обратно в приложение, в билете проверки подлинности будет указано состояние. Мы используем этот факт, чтобы гарантировать, что значение "signup" сохраняется во всем потоке проверки подлинности.

## Добавление запроса на согласие администратора

В Azure AD поток согласия администратора запускается путем добавления параметра "prompt" в строку запроса в запросе на проверку подлинности.

```
/authorize?prompt=admin_consent&...
```

Приложение Surveys добавляет запрос во время события `RedirectToAuthenticationEndpoint`. Это событие вызывается сразу перед тем, как ПО промежуточного слоя выполнит перенаправление в конечную точку проверки подлинности.

```csharp
public override Task RedirectToAuthenticationEndpoint(RedirectContext context)
{
    if (context.IsSigningUp())
    {
        context.ProtocolMessage.Prompt = "admin_consent";
    }

    _logger.RedirectToIdentityProvider();
    return Task.FromResult(0);
}
```

> [AZURE.NOTE] См. [SurveyAuthenticationEvents.cs].

При задании ` ProtocolMessage.Prompt` ПО промежуточного слоя добавляет параметр "prompt" в запрос на проверку подлинности.

Обратите внимание, что запрос необходим только во время регистрации. Он не требуется для обычного входа. Чтобы различить их, проверим наличие значения `signup` в состоянии проверки подлинности. Для проверки этого условия используется следующий метод расширения:

```csharp
internal static bool IsSigningUp(this BaseControlContext context)
{
    Guard.ArgumentNotNull(context, nameof(context));

    string signupValue;
    object obj;
    // Check the HTTP context and convert to string
    if (context.HttpContext.Items.TryGetValue("signup", out obj))
    {
        signupValue = (string)obj;
    }
    else
    {
        // It's not in the HTTP context, so check the authentication ticket.  If it's not there, we aren't signing up.
        if ((context.AuthenticationTicket == null) ||
            (!context.AuthenticationTicket.Properties.Items.TryGetValue("signup", out signupValue)))
        {
            return false;
        }
    }

    // We have found the value, so see if it's valid
    bool isSigningUp;
    if (!bool.TryParse(signupValue, out isSigningUp))
    {
        // The value for signup is not a valid boolean, throw                
        throw new InvalidOperationException($"'{signupValue}' is an invalid boolean value");
    }

    return isSigningUp;
}
```

> [AZURE.NOTE] См. [BaseControlContextExtensions.cs].

> [AZURE.NOTE] Примечание. Этот код содержит решение для устранения известных ошибок в ASP.NET Core 1.0 RC1. В событии `RedirectToAuthenticationEndpoint` нельзя получить свойства проверки подлинности, содержащие состояние "signup". Чтобы обойти эту проблему, метод `AccountController.SignUp` также помещает состояние "signup" в `HttpContext`. Такой подход оправдан, поскольку `RedirectToAuthenticationEndpoint` происходит до перенаправления, поэтому мы все равно получим то же значение `HttpContext`.

## Регистрация клиента

Приложение Surveys хранит сведения о каждом клиенте и пользователе в базе данных приложения.

![Таблица клиентов](media/guidance-multitenant-identity/tenant-table.png)

В таблице Tenant IssuerValue является значением утверждения издателя для клиента. Для Azure AD это `https://sts.windows.net/<tentantID>` с уникальным значением для каждого клиента.

При регистрации нового клиента приложение Surveys вносит запись клиента в базу данных. Это происходит внутри события `AuthenticationValidated`. (Не выполняйте это действие до события, так как маркер идентификатора пока не прошел проверку и значениям утверждения доверять нельзя.) См. статью [Проверка подлинности].

Ниже приведен соответствующий код из приложения Surveys:

```csharp
public override async Task AuthenticationValidated(AuthenticationValidatedContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var userId = principal.GetObjectIdentifierValue();
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    _logger.AuthenticationValidated(userId, issuerValue);

    // Normalize the claims first.
    NormalizeClaims(principal);
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue)
        .ConfigureAwait(false);

    if (context.IsSigningUp())
    {
        // Originally, we were checking to see if the tenant was non-null, however, this would not allow
        // permission changes to the application in AAD since a re-consent may be required.  Now we just don't
        // try to recreate the tenant.
        if (tenant == null)
        {
            tenant = await SignUpTenantAsync(context, tenantManager)
                .ConfigureAwait(false);
        }

        // In this case, we need to go ahead and set up the user signing us up.
        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
    else
    {
        if (tenant == null)
        {
            _logger.UnregisteredUserSignInAttempted(userId, issuerValue);
            throw new SecurityTokenValidationException($"Tenant {issuerValue} is not registered");
        }

        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
}
```

> [AZURE.NOTE] См. [SurveyAuthenticationEvents.cs].

Этот код делает следующее:

1.	Проверяет наличие значения издателя клиента в базе данных. Если клиент не зарегистрирован, `FindByIssuerValueAsync` возвращает значение NULL.
2.	Если пользователь проходит регистрацию:
  1.	Добавляет клиента в базу данных (`SignUpTenantAsync`).
  2.	Добавляет прошедшего проверку подлинности пользователя в базу данных (`CreateOrUpdateUserAsync`).
3.	В противном случае выполняется обычный поток входа:
  1.	Если издатель клиента не найден в базе данных, это значит, что клиент не зарегистрирован, и заказчику нужно зарегистрироваться. В этом случае создает исключение для сбоя проверки подлинности.
  2.	В противном случае создает запись в базе данных для этого пользователя, если ее еще нет (`CreateOrUpdateUserAsync`).

Далее приведен метод [SignUpTenantAsync], который добавляет клиента в базу данных.

```csharp
private async Task<Tenant> SignUpTenantAsync(BaseControlContext context, TenantManager tenantManager)
{
    Guard.ArgumentNotNull(context, nameof(context));
    Guard.ArgumentNotNull(tenantManager, nameof(tenantManager));

    var principal = context.AuthenticationTicket.Principal;
    var issuerValue = principal.GetIssuerValue();
    var tenant = new Tenant
    {
        IssuerValue = issuerValue,
        Created = DateTimeOffset.UtcNow
    };

    try
    {
        await tenantManager.CreateAsync(tenant)
            .ConfigureAwait(false);
    }
    catch(Exception ex)
    {
        _logger.SignUpTenantFailed(principal.GetObjectIdentifierValue(), issuerValue, ex);
        throw;
    }

    return tenant;
}
```

Далее приведено краткое описание всего потока регистрации в приложении Surveys:

1.	Пользователь нажимает кнопку **Регистрация**.
2.	Действие `AccountController.SignUp` возвращает ChallengeResult. Состояние проверки подлинности содержит значение "signup".
3.	В событие `RedirectToAuthenticationEndpoint` добавляется запрос `admin_consent`.
4.	ПО промежуточного слоя OpenID Connect осуществляет перенаправление в Azure AD, и пользователь проходит проверку подлинности.
5.	В событии `AuthenticationValidated` выполняется поиск состояния "signup".
6.	Клиент добавляется в базу данных.


<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[цикл статей]: guidance-multitenant-identity.md
[AccountController]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Controllers/AccountController.cs
[state]: http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest
[SurveyAuthenticationEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[BaseControlContextExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/BaseControlContextExtensions.cs
[Проверка подлинности]: guidance-multitenant-identity-authenticate.md
[SignUpTenantAsync]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->