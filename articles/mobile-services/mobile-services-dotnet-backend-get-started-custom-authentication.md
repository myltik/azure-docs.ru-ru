<properties
	pageTitle="Приступая к работе с настраиваемой проверкой подлинности | Microsoft Azure"
	description="Узнайте, как аутентифицировать пользователей по имени и паролю."
	documentationCenter="Mobile"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/28/2015"
	ms.author="mahender"/>

# Начало работы с настраиваемой проверкой подлинности

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## Обзор
В этом разделе показывается, как проверять подлинности пользователей в серверной части .NET мобильных служб Azure путем выдачи токена проверки подлинности мобильных служб. В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с использованием имени пользователя и пароля для приложения.

>[AZURE.NOTE]В этом руководстве показан расширенный способ проверки подлинности мобильных служб по пользовательским учетным данным. Во многих приложениях больше подходит использование встроенных поставщиков удостоверений, что дает пользователям возможность входить с помощью Facebook, Twitter, Google, учетной записи Майкрософт и Azure Active Directory. Если вы впервые имеете дело с проверкой подлинности в мобильных службах, сначала ознакомьтесь с учебником [Добавление проверки подлинности в приложение].

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

>[AZURE.IMPORTANT]Цель данного учебника заключается в том, чтобы показать, каким образом происходит выдача маркера проверки подлинности для мобильных служб. Оно не предназначено для использования в качестве руководства по безопасности. При разработке приложений необходимо помнить о последствиях, которые влечет за собой нарушение безопасности хранения паролей, и иметь стратегию управления на случай атак методом подбора.

## Настройка таблицы учетных записей

Так как используется настраиваемая проверка подлинности, не связанная с другим поставщиком удостоверений, вам потребуется хранить сведения для входа пользователей. В этом разделе создается таблица для учетных записей и настраиваются базовые механизмы безопасности. Таблица учетных записей будет содержать имена пользователей, а также дополненные случайными данными и хэшированные пароли; при необходимости в нее можно включить дополнительные сведения о пользователях.

1. В папке **DataObjects** проекта серверной части добавьте новую сущность `Account`:

2. Добавьте следующий оператор `using`:

		using Microsoft.WindowsAzure.Mobile.Service;

3. Замените определение класса следующим кодом:

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }

    Это представляет строку в новой таблице учетных записей, содержащую имя пользователя, его соль и безопасно хранящийся пароль.

2. В папке **Модели** можно найти производный класс **DbContext** с именем, соответствующим вашей мобильной службе. Откройте контекст и добавьте таблицу учетных записей в модель данных, включив следующий код:

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]Во фрагментах кода этого учебника в качестве имени контекста используется `todoContext`. Вам необходимо обновить фрагменты кода для контекста своего проекта. Далее вы настроите функции безопасности для работы с этими данными.

5. Создайте класс с именем `CustomLoginProviderUtils` и добавьте в него следующий оператор `using`:

		using System.Security.Cryptography;

6. Добавьте следующие метод кода в новый класс:


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

	Это позволят создавать длинную соль, обеспечивает возможность хэширования пароля с солью, а также предоставляет безопасный способ сравнения двух хэшей.

## Создание конечной точки регистраций

На этот момент все готово для создания учетных записей пользователей. В этом разделе будет настраиваться конечная точка регистраций для обработки новых запросов на регистрацию. В этой точке будут осуществляться новые политики имен и паролей пользователей и обеспечиваться отклонение имен пользователей. Затем будет выполняться безопасное сохранение сведений о пользователях в базе данных.

1. Создайте следующий новый класс, который будет представлять входящую попытку регистрации:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Если необходимо собирать и хранить другие данные во время регистрации, следует сделать это здесь.

2. В серверном проекте мобильной службы щелкните правой кнопкой мыши **Контроллеры**, выберите **Добавить** и **Контроллер**, создайте **настраиваемый контроллер мобильных служб Microsoft Azure** с именем `CustomRegistrationController`, а затем добавьте следующие операторы `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	В приведенном выше коде замените заполнитель пространством имен вашего проекта.

4. Замените определение класса следующим кодом:

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
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
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

    Не забудьте заменить переменную *todoContext* именем **DbContext** проекта. Обратите внимание на то, что этот контроллер с помощью следующего атрибута разрешает весь трафик к данной конечной точке:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]К этой конечной точке регистрации могут получать доступ любые клиенты по протоколу HTTP. Прежде чем опубликовать эту службу в рабочей среде, следует реализовывать какую-либо схему для проверки регистрации, например, проверку на основе SMS или электронной почты. Это поможет предотвратить создание мошеннических регистраций хакером.

## Создание поставщика входа в систему

Одной из основных конструкций в конвейере проверки подлинности мобильных служб является **LoginProvider**. В этом разделе вы создадите собственный `CustomLoginProvider`. Он не будет включаться в конвейер, как встроенные поставщики, но будет предоставлять некоторые удобные функциональные возможности. При использовании Visual Studio 2013 может потребоваться установка пакета `WindowsAzure.MobileServices.Backend.Security` Nuget для добавления ссылок на класс `LoginProvider`.

1. Создайте класс `CustomLoginProvider`, который является производным от **LoginProvider**, и добавьте следующие операторы `using`:

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;

3. Замените определение класса **CustomLoginProvider** следующим кодом:

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

       Попытка выполнить сборку проекта в этот момент завершится сбоем. В `LoginProvider` есть три абстрактных метода, которые необходимо реализовать, что вы сделаете позже.

2. Создайте класс с именем `CustomLoginProviderCredentials` в том же файле кода.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	Он представляет сведения о пользователе, которые будут доступными в серверной части через [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx). При добавлении настраиваемых утверждений убедитесь, что они записываются в этом объекте.

3. Добавьте следующую реализацию абстрактного метода `ConfigureMiddleware` в класс **CustomLoginProvider**.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	Этот метод не реализован, так как класс **CustomLoginProvider** не интегрируется с конвейером проверки подлинности.

4. Добавьте следующую реализацию абстрактного метода `ParseCredentials` в класс **CustomLoginProvider**.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	Этот метод будет разрешать серверной части выполнять десериализацию сведений о пользователе из входящего токена проверки подлинности.

5. Добавьте следующую реализацию абстрактного метода `CreateCredentials` в класс **CustomLoginProvider**.

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

	Этот метод преобразует [ClaimsIdentity] в объект [ProviderCredentials], который используется на этапе выдачи маркера проверки подлинности. В этом методе может снова потребоваться записывать все дополнительные утверждения.

6. После создания **ConfigOptions** откройте файл проекта WebApiConfig.cs в папке App\_Start и перейдите к следующей строке кода:

		options.LoginProviders.Add(typeof(CustomLoginProvider));



## Создание конечной точки входа

Далее необходимо создать конечную точку для входа пользователей. Имя пользователя и пароль будут проверяться по базе данных: сначала применяется соль пользователя, затем хэшируется пароль и выполняется проверка того, соответствует ли введенное значение хранящемуся в базе данных. Если проверка прошла успешно, можно создать [ClaimsIdentity] и передать его в класс **CustomLoginProvider**. Затем клиентское приложение получит идентификатор пользователя и маркер проверки подлинности для последующего доступа к мобильной службе.

1. В серверном проекте мобильной службы создайте следующий класс `LoginRequest`:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	Этот класс представляет входящую попытку входа.

2. Создайте следующий класс `CustomLoginResult`:

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }

	    }

	Этот класс представляет успешный вход с идентификатором пользователя и маркером проверки подлинности. Обратите внимание на то, что этот класс имеет такую же форму, как класс MobileServiceUser на клиенте, что упрощает передачу отклика на вход на строго типизированном клиенте.

2. Щелкните правой кнопкой мыши **Контроллеры**, выберите **Добавить** и **Контроллер**, создайте **настраиваемый контроллер мобильных служб Microsoft Azure** с именем `CustomLoginController`, а затем добавьте следующие операторы `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. Замените определение класса **CustomLoginController** следующим кодом:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }

	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);

	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       Не забудьте заменить переменную *todoContext* именем **DbContext** проекта. Обратите внимание на то, что этот контроллер с помощью следующего атрибута разрешает весь трафик к данной конечной точке:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]Для использования в рабочей среде `CustomLoginController` также должен содержать стратегию обнаружения атак методом подбора. В противном случае решение входа в систему будет уязвимо для атак.

## Настройка мобильной службы для требования проверки подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## Тестирование потока входа в систему с помощью тестового клиента

В клиентском приложении необходимо разработать настраиваемый экран для входа, который будет принимать имена и пароли пользователей и отправлять их в виде полезных данных JSON на конечные точки регистрации и входа. Для выполнения данного руководства вместо этого экрана будет просто использоваться встроенный тестовый клиент для серверной части .NET мобильных служб.

1. В Visual Studio щелкните правой кнопкой мыши проект мобильной службы, а затем выберите команды **Отладить** и **Запустить новый экземпляр**.

	Будет запущен новый экземпляр отладки для серверного проекта мобильной службы. После успешного запуска службы вы увидите начальную страницу, на которой говорится, что **мобильная служба работает**.

2. На начальной странице службы щелкните **Попробовать** и введите в диалоговом окне проверки подлинности пароль, который вы указали в файле web.config для параметра **MS\_ApplicationKey**. Оставьте имя пользователя пустым.

3. На странице справки щелкните конечную точку **CustomRegistration** и нажмите кнопку **Попробовать**.

    ![][2]

4. В коде замените примеры строк именем пользователя и паролем, которые соответствуют заданным ранее условиям, и нажмите кнопку **Отправить**.

    ![][3]

	Ответ должен быть **201/Создано**.

5. Нажмите кнопку «Назад» в браузере и повторите шаги 2 и 3 для конечной точки **CustomLogin**, используя имя пользователя и пароль, которые вы зарегистрировали в предыдущем действии.

    ![][4]

	Должно появиться ответное сообщение с текстом, содержащим объект JSON **user**, который имеет как *userId*, так и *authenticationToken* (это маркер проверки подлинности мобильных служб, созданный в результате настраиваемой проверки подлинности). Этого маркера достаточно для предоставления клиентскому приложению доступа к конечной точке TodoItem.

	Скопируйте значение *authenticationToken*. Вы будете использовать его для доступа к ограниченной конечной точке TodoItem.

6. Нажмите кнопку «Назад» в браузере, а затем на странице документации по API щелкните **GetTables** и **Попробовать**.

7. В диалоговом окне запроса GET щелкните знак «плюс» рядом с элементом **Заголовки**, введите значение `X-ZUMO-AUTH` в левом поле, вставьте скопированное значение *authenticationToken* в поле справа, а затем нажмите кнопку **Отправить**.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	Мобильная служба должна предоставить доступ к конечной точке и возвратить статус **200/OK** вместе со списком TodoItems из таблицы.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT]Если вы хотите также опубликовать этот проект мобильной службы в Azure для тестирования, помните, что поставщики входа и проверки подлинности будут уязвимы для атак. Защитите их соответствующим образом или используйте неважные тестовые данные. При использовании настраиваемой схемы проверки подлинности для защиты рабочей службы необходимо соблюдать особую осторожность.

## Вход с клиента при помощи настраиваемой проверки подлинности

В этом разделе описываются шаги, необходимые для доступа к конечным точкам настраиваемой проверки подлинности из клиента и получения маркера проверки подлинности, требуемого для доступа к мобильной службе. Так как нужный код зависит от того, какой клиент используется, в этом разделе приведены общие рекомендации для всех платформ.

>[AZURE.NOTE]Клиентские библиотеки мобильных служб взаимодействуют со службой через HTTPS. Так как для данного решения требуется отправлять пароли в виде открытого текста, необходимо убедиться в том, что при вызове этих конечных точек с помощью прямых запросов REST используется протокол HTTPS.

1. Создайте в клиентском приложении элементы пользовательского интерфейса, необходимые для ввода имени пользователя и пароля.

2. Используйте подходящий метод **invokeApi** для **MobileServiceClient** в клиентской библиотеке, чтобы вызвать конечную точку **CustomRegistration**, передав имя пользователя и пароль, предоставленные во время выполнения, в тексте сообщения.

	Для создания учетной записи определенного пользователя потребуется вызвать конечную точку **CustomRegistration** всего один раз, если учетные данные для входа хранятся в таблице учетных записей. Примеры того, как вызывать настраиваемый API для различных поддерживаемых клиентских платформ, см. в статье [Настраиваемый API в мобильных службах Azure — клиентские пакеты SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

	> [AZURE.IMPORTANT]Так как этот шаг подготовки пользователя выполняется только один раз, рекомендуется создавать учетные записи пользователей, используя внештатный канал. Для общедоступной конечной точки регистрации также следует подумать о реализации проверки через SMS или электронную почту или принятии других мер для предотвращения создания мошеннических учетных записей. Вы можете использовать Twilio для отправки SMS из мобильных служб. Вы также можете использовать SendGrid для отправки сообщений электронной почты из мобильных служб. Дополнительные данные об использовании SendGrid см. в статье [Отправка сообщений электронной почты из мобильных служб с помощью SendGrid](store-sendgrid-mobile-services-send-email-scripts.md).

3. Снова используйте нужный метод **invokeApi**, на этот раз для вызова конечной точки **CustomLogin**, передав имя пользователя и пароль, предоставленные во время выполнения, в тексте сообщения.

	В этот раз необходимо записать значения *userId* и *authenticationToken*, возвращаемые в объекте отклика после успешного входа.

4. Используйте возвращенные значения *userId* и *authenticationToken*, чтобы создать объект **MobileServiceUser** и задать его в качестве текущего пользователя для экземпляра **MobileServiceClient**, как показано в разделе [Добавление проверки подлинности к существующему приложению](mobile-services-dotnet-backend-ios-get-started-users.md). Так как результат CustomLogin имеет такую же форму, что и объект **MobileServiceUser**, можно будет выполнить прямое приведение результата.

На этом учебник завершен.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx

<!---HONumber=AcomDC_0107_2016-->