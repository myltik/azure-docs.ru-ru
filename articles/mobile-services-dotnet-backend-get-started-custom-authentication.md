<properties urlDisplayName="Get started with custom authentication" pageTitle="Приступая к работе с пользовательской проверкой подлинности | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как аутентифицировать пользователей по имени и паролю." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with custom authentication" authors="mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Начало работы с настраиваемой проверкой подлинности

В этой статье показано, как проверить подлинность пользователя на стороне сервера .NET мобильных служб Azure путем выдачи токена проверки подлинности мобильных служб. В данном руководстве в проект быстрого запуска будет добавляться проверка подлинности по имени пользователя и паролю для приложения.

>[WACOM.NOTE] В этом руководстве показан расширенный способ проверки подлинности мобильных служб по пользовательским учетным данным. Во многих приложениях больше подходит использование встроенных поставщиков удостоверений, что дает пользователям возможность входить с помощью Facebook, Twitter, Google, учетной записи Майкрософт и Azure Active Directory. Если это ваш первый опыт работы с проверкой подлинности в мобильных службах, то сначала ознакомьтесь с учебником [Приступая к работе с пользователями].

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Настройка таблицы учетных записей]
2. [Создание конечной точки регистраций]
3. [Создание поставщика входа в систему]
4. [Создание конечной точки входа в систему]
5. [Настройка мобильной службы для требования проверки подлинности]
6. [Тестирование потока входа в систему с помощью тестового клиента]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами]. 

>[WACOM.NOTE] Цель данного руководства заключается в том, чтобы показать, каким образом происходит выдача токена проверки подлинности для мобильных служб. Оно не предназначено для использования в качестве руководства по безопасности. При разработке приложений необходимо помнить о последствиях, которые влечет за собой нарушение безопасности хранения паролей, и иметь стратегию управления на случай атак методом подбора.


## <a name="table-setup"></a>Настройка таблицы учетных записей

Поскольку используется настраиваемая проверка подлинности, не связанная с другим поставщиком удостоверений, придется хранить сведения для входа пользователей. В этом разделе создается таблица для учетных записей и настраиваются базовые механизмы безопасности. Таблица учетных записей будет содержать имена пользователей, а также дополненные случайными данными и хэшированные пароли; при необходимости в нее можно включить дополнительные сведения о пользователях.

1. В папке `DataObjects` проекта серверной части создайте новую сущность `Account`:

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    Он будет представлять строку в нашей новой таблице и содержать имя пользователя, случайные данные этого пользователя и безопасно сохраненный пароль.

2. В папке `Модели` можно найти класс `DbContext`, который указан после мобильной службы. В остальной части этого руководства в качестве примера будет использоваться `todoContext`, и фрагменты кода нужно будет обновить соответствующим образом. Откройте контекст и добавьте таблицу учетных записей в модель данных, включив следующий код:

        public DbSet<Account> Accounts { get; set; }

3. Далее будут настраиваться компоненты безопасности для работы с этими данными. Потребуются средства для создания новых длинных случайных данных, возможность хэширования пароля, дополненного случайными данными и безопасный способ сравнения двух хэшей. Создайте класс с именем `CustomLoginProviderUtils` и добавьте в него следующие методы:


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }


## <a name="register-endpoint"></a>Создание конечной точки регистраций

Теперь все готово для создания учетных записей пользователей. В этом разделе будет настраиваться конечная точка регистраций для обработки новых запросов на регистрацию. В этой точке будут осуществляться новые политики имен и паролей пользователей и обеспечиваться отклонение имен пользователей. Затем будет выполняться безопасное сохранение сведений о пользователях в базе данных.

1. Создайте объект, который будет представлять попытку входящей регистрации:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Если во время регистрации планируется собирать другие сведения, это можно включить здесь.

1. В проекте серверной части мобильных служб добавьте новый настраиваемый контроллер с именем CustomRegistrationController и вставьте следующий код:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomRegistrationController : ApiController
        {
            public ApiServices Services { get; set; }

            // POST api/CustomRegistration
            public HttpResponseMessage Post(RegistrationRequest registrationRequest)
            {
                if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
                }
                else if (registrationRequest.password.Length < 8)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
                }

                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
                if (account != null)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
                }
                else
                {
                    byte[] salt = CustomLoginProviderUtils.generateSalt();
                    Account newAccount = new Account
                    {
                        Id = Guid.NewGuid().ToString(),
                        Username = registrationRequest.username,
                        Salt = salt,
                        SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
                    };
                    context.Accounts.Add(newAccount);
                    context.SaveChanges();
                    return this.Request.CreateResponse(HttpStatusCode.Created);
                }
            }
        }   

    Разрешите весь трафик в эту конечную точку, дополнив контроллер следующим кодом:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>Создание поставщика входа в систему

Одной из основных конструкций в конвейере проверки подлинности мобильных служб является `LoginProvider`. В этом разделе будет создаваться собственный `CustomLoginProvider`. Он не будет включаться в конвейер, как встроенные поставщики, но будет предоставлять некоторые удобные функциональные возможности.

1. Создайте новый класс `CustomLoginProvider`, производный от `LoginProvider`:

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       `LoginProvider` has three other abstract methods which you will implement later.

2. Создайте новый класс с именем `CustomLoginProviderCredentials`. Он представляет сведения о пользователе, которые будут доступными в серверной части через `ServiceUser.getIdentitiesAsync()`. При добавлении настраиваемых утверждений убедитесь, что они записываются в этом объекте.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. Добавьте реализацию абстрактного метода `ConfigureMiddleware` в класс `CustomLoginProvider`. Этот метод представляет собой пустышку, так как класс `CustomLoginProvider` не интегрируется с конвейером проверки подлинности.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. Добавьте реализацию абстрактного метода `ConfigureMiddleware` в класс `CustomLoginProvider`. Этот метод будет разрешать серверной части выполнять десериализацию сведений о пользователе из входящего токена проверки подлинности.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. Добавьте реализацию абстрактного метода `ConfigureMiddleware` в класс `CustomLoginProvider`. Этот метод преобразует `ClaimsIdentity` в объект `ProviderCredentials`, который используется на этапе выдачи токена проверки подлинности. Здесь может снова потребоваться перехват всех дополнительных утверждений.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

## <a name="login-endpoint"></a>Создание конечной точки входа в систему

Теперь настало время создать конечную точку для входа пользователей. Имя пользователя и пароль будут проверяться в базе данных: сначала применяются случайные данных пользователя, затем хэшируется пароль и выполняется проверка, соответствует ли введенное значение хранящемуся в базе данных. Если проверка прошла успешно, можно создать `ClaimsIdentity` и передать его в класс `CustomLoginProvider`. Затем клиентское приложение будет получать идентификатор пользователя и токен проверки подлинности для последующего доступа к мобильной службе.

1. В проекте серверной части мобильных служб создайте объект, который будет представлять поступающую попытку входа:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. Добавьте новый настраиваемый контроллер с именем `CustomLoginController` и вставьте следующий код:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    Разрешите весь трафик в эту конечную точку, дополнив контроллер следующим кодом:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[WACOM.NOTE] `CustomLoginController` для использования в рабочей среде также должен содержать стратегию на случай атак методом подбора. В противном случае решение входа в систему будет уязвимо для атак.

## <a name="require-authentication"></a>Настройка мобильной службы для требования проверки подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>Тестирование потока входа в систему с помощью тестового клиента

В клиентском приложении необходимо разработать настраиваемый экран для входа, который будет принимать имена и пароли пользователей и отправлять их в виде полезных данных JSON в конечные точки регистрации и входа. Для выполнения данного руководства вместо этого экрана будет просто использоваться встроенный тестовый клиент для серверной части .NET мобильных служб.

>[WACOM.NOTE] Пакеты SDK мобильных служб будут взаимодействовать со службой через HTTPS. Если планируется доступ к этой конечной точки прямым вызовом REST, то необходимо убедиться в том, что для вызова мобильной службы используется протокол HTTPS, поскольку пароли отправляются в виде открытого текста.

1. В Visual Studio запустите новый экземпляр отладки проекта серверной части мобильных служб, щелкнув этот проект правой кнопкой мыши и выбрав в меню пункты **Отладка->Запустить новый экземпляр**

    ![][0]

2. Нажмите **Try it out** (Испытать).

    ![][1]

3. Выберите конечную точку регистрации. Можно просмотреть базовую документацию для используемого API. Нажмите **Try this out** (Испытать).

    ![][2]

4. В коде тексте замените образцы строк именем пользователя и паролем, которые соответствуют заданным ранее условиям. Нажмите кнопку **Отправить**. Ответ должен быть следующим: **201/Создано**.

    ![][3]

5. Повторите этот процесс для конечной точки входа. После отправки имени пользователя и пароля, зарегистрированных ранее, будет возвращен идентификатор пользователя и токен проверки подлинности.

    ![][4]


<!-- Anchors. -->
[Настройка таблицы учетных записей]: #table-setup
[Создание конечной точки регистраций]: #register-endpoint
[Создание поставщика входа в систему]: #login-provider
[Создание конечной точки входа в систему]: #login-endpoint
[Настройка мобильной службы для требования проверки подлинности]: #require-authentication
[Тестирование потока входа в систему с помощью тестового клиента]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Приступая к работе с пользователями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
