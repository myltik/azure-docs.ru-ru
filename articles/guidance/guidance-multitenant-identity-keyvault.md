<properties
   pageTitle="Использование хранилища ключей для защиты секретов приложения | Microsoft Azure"
   description="Использование службы хранилища ключей для хранения секретов приложения"
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

# Использование хранилища ключей Azure для защиты секретов приложения

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

## Обзор

Довольно часто используются параметры приложения, которые являются конфиденциальными и должны быть защищены, например:

- строки подключения к базе данных;
- Пароли
- Криптографические ключи

Из соображений безопасности не следует размещать эти секреты в системе управления версиями. Слишком высок риск их утечки, даже если репозиторий исходного кода является закрытым. И вопрос не только в защите секретов от общего доступа. В больших проектах может потребоваться ограничить доступ к производственным секретам, предоставив его только определенным разработчикам и операторам. (Для сред тестирования и разработки используются разные параметры.)

Наиболее безопасный вариант — хранить секреты в [хранилище ключей Azure][KeyVault]. Хранилище ключей является облачной службой для управления криптографическими ключами и другими секретными данными. В этой статье описано, как использовать хранилище ключей для хранения параметров конфигурации приложения.

В приложении [Tailspin Surveys][Surveys] секретными являются такие параметры:

- строка подключения к базе данных;
- строка подключения к Redis;
- секрет клиента для веб-приложения.

Для хранения секретов конфигурации в хранилище ключей приложение Surveys реализует настраиваемый поставщик конфигурации, который подключается к [системе конфигурации][configuration] ASP.NET Core 1.0. При запуске настраиваемый поставщик считывает параметры конфигурации из хранилища ключей.

Приложение Surveys загружает параметры конфигурации из следующих расположений:

- файл appsettings.json;
- [хранилище секретов пользователя][user-secrets] (только для тестирования в среде разработки);
- среда размещения (параметры веб-приложений Azure);
- хранилище ключей;

Каждое из этих расположений переопределяет предыдущее. Поэтому все параметры, размещенные в хранилище ключей, будут приоритетными.

> [AZURE.NOTE] По умолчанию поставщик конфигурации хранилища ключей отключен. Он не требуется для локального запуска приложения. Его можно будет активировать в рабочей среде.

> [AZURE.NOTE] В настоящее время поставщик хранилища ключей для .NET Core не поддерживается, так как для его использования требуется пакет [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault].

При запуске приложение считывает настройки каждого зарегистрированного поставщика конфигурации и использует их для заполнения объекта со строго типизированными параметрами. Дополнительные сведения см. в разделе [Using Options and configuration objects][options] (Использование параметров и объектов конфигурации).

## Реализация

Класс [KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] — это поставщик конфигурации, который встраивается в [систему конфигурации][configuration] ASP.NET Core 1.0.

Чтобы использовать `KeyVaultConfigurationProvider`, в классе запуска вызовите метод расширения `AddKeyVaultSecrets`:

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Обратите внимание, что для `KeyVaultConfigurationProvider` требуются определенные параметры конфигурации, которые должны храниться в одном из других источников конфигурации.

Когда приложение запускается, в `KeyVaultConfigurationProvider` перечисляются все секреты из хранилища ключей. Для каждого секрета выполняется поиск по тегу ConfigKey. Значение тега — это имя параметра конфигурации.

> [AZURE.NOTE] [Tags][Теги ключей] являются дополнительными метаданными, которые хранятся вместе с ключом. Здесь используются теги, так как имена ключей не могут содержать двоеточия (:).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] См. файл [KeyVaultConfigurationProvider.cs].

## Настройка хранилища ключей в приложении Surveys

Предварительные требования:

- Установите [командлеты диспетчера ресурсов Azure][azure-rm-cmdlets].
- Настройте приложение Surveys, как описано в разделе о [запуске приложения Surveys][readme].

Основные действия:

1. Настройте в клиенте учетную запись пользователя с правами администратора.
2. Настройте сертификат клиента.
3. Создать хранилище ключей.
4. Добавьте параметры конфигурации в хранилище ключей.
5. Раскомментируйте код, который активирует хранилище ключей.
6. Обновите секреты пользователя приложения.

### Настройка учетной записи пользователя с правами администратора

> [AZURE.NOTE] Для создания хранилища ключей используется учетная запись, с помощью которой можно управлять подпиской Azure. Кроме того, любое приложение, авторизованное для чтения данных из хранилища ключей, должно быть зарегистрировано в том же клиенте, что и эта учетная запись.

На этом этапе следует удостовериться, что вы имеете возможность создать хранилище ключей, находясь в системе в качестве пользователя, который выполнил вход из клиента с зарегистрированным приложением Surveys.

Сначала перейдите в каталог, связанный с вашей подпиской Azure.

1. Войдите на [портал управления Azure][azure-management-portal].

2. Щелкните **Параметры**.

    ![данных](media/guidance-multitenant-identity/settings.png)

3. Выберите подписку Azure.

4. В нижней части портала выберите команду **Изменить каталог**.

    ![Параметры](media/guidance-multitenant-identity/edit-directory.png)

5. В окне "Изменить связанный каталог" выберите клиент Azure AD с зарегистрированным приложением Surveys.

    ![Параметры](media/guidance-multitenant-identity/edit-directory2.png)

6. Нажмите кнопку со стрелкой и заполните поля диалогового окна.

Создайте учетную запись пользователя с правами администратора в клиенте Azure AD, в котором зарегистрировано приложение Surveys.

1. Войдите на [портал управления Azure][azure-management-portal].

2. Выберите клиент Azure AD, в котором зарегистрировано приложение.

3. Последовательно выберите элементы **Пользователи** > **Добавить пользователя**.

4. В диалоговом окне **Добавление пользователя** назначьте пользователю роль глобального администратора.

Добавьте пользователя с правами администратора как соадминистратора подписки Azure.

1. Войдите на [портал управления Azure][azure-management-portal].

2. Щелкните **Параметры** и выберите подписку Azure.

3. Щелкните **Администраторы**.

4. В нижней части портала нажмите кнопку **Добавить**.

5. Введите адрес электронной почты, указанный для созданной ранее учетной записи пользователя с правами администратора.

6. Установите флажок для подписки.

7. Нажмите кнопку с галочкой, чтобы завершить работу с диалоговым окном.

![Добавление соадминистратора](media/guidance-multitenant-identity/co-admin.png)


### Настройка сертификата клиента

1. Запустите сценарий PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault], как показано ниже.
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Для параметра `Subject` введите любое имя, например surveysapp. Сценарий создаст самозаверяющий сертификат и сохранит его в хранилище сертификатов "Текущий пользователь/Личные".

2. Выходные данные этого сценария представлены фрагментом JSON. Добавьте его в манифест веб-приложения, как показано ниже.

    1. Войдите на [портал управления Azure][azure-management-portal] и перейдите в свой каталог Azure AD.

    2. Щелкните **Приложения**.

    3. Выберите приложение Surveys.

    4.	Щелкните **Управление манифестом** и выберите пункт **Скачать манифест**.

    5.	Откройте JSON-файл манифеста в текстовом редакторе. Вставьте выходные данные сценария в свойство `keyCredentials`. Это должно выглядеть следующим образом:
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	Сохраните изменения в JSON-файле.

    7.	Вернитесь на портал. Щелкните **Управление манифестом** > **Отправить манифест** и отправьте JSON-файл.

3. Добавьте тот же фрагмент JSON в манифест приложения веб-API (Surveys.WebAPI).

4. Выполните следующую команду, чтобы получить отпечаток сертификата:
    ```
    certutil -store -user my [subject]
    ```
    где `[subject]` — это значение, указанное для Subject в сценарии PowerShell. Отпечаток указан в разделе Cert Hash(sha1). Удалите пробелы между шестнадцатеричными значениями.

Отпечаток будет использоваться позднее.

### Создайте хранилище ключей.

1. Запустите сценарий PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault], как показано ниже.

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    При появлении запроса на ввод учетных данных войдите в систему с учетной записью пользователя Azure AD, созданной ранее. Сценарий создаст новую группу ресурсов с новым хранилищем ключей.

    Примечание. Чтобы получить список допустимых регионов для параметра -Location, можно использовать следующую команду PowerShell:

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Повторно выполните SetupKeyVault.ps со следующими параметрами:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    где:

    - key vault name — имя, присвоенное хранилищу ключей на предыдущем этапе;
    - web app client ID — идентификатор клиента для веб-приложения Surveys;
    - web api client ID — идентификатор клиента для приложения Surveys.WebAPI.

    Пример:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Идентификаторы клиента можно получить на [портале управления Azure][azure-management-portal]. Выберите клиент Azure AD, выберите приложение и нажмите кнопку **Настройка**.

    Этот сценарий авторизует веб-приложение и веб-API для получения секретов из хранилища ключей. Дополнительные сведения см. в статье [Приступая к работе с хранилищем ключей Azure][authorize-app].

### Добавьте параметры конфигурации в хранилище ключей.

1. Запустите SetupKeyVault.ps следующим образом:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    где:

    - key vault name — имя, присвоенное хранилищу ключей на предыдущем этапе;
    - Redis DNS name — DNS-имя экземпляра кэша Redis;
    - Redis access key — ключ доступа для экземпляра кэша Redis.

    Эта команда добавляет секрет в хранилище ключей. Секрет представляет собой пару имя-значение и тег:

    -	Имя ключа не используется приложением, но оно должно быть уникальным в хранилище ключей.
    -	Это значение параметра конфигурации — в данном случае строка подключения Redis.
    -	Тег ConfigKey содержит имя ключа конфигурации.

2. На этом этапе рекомендуется проверить, успешно ли сохранены секреты в хранилище ключей. Выполните следующую команду PowerShell:

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    Выходные данные должны представлять секретное значение и некоторые метаданные:

    ![Вывод PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Повторно запустите SetupKeyVault.ps, чтобы добавить строку подключения к базе данных:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    где `<<DB connection string>>` — значение строки подключения к базе данных.

    Для тестирования с локальной базой данных скопируйте строку подключения из файла Tailspin.Surveys.Web/appsettings.json. При этом обязательно замените двойную обратную косую черту (\\\) одиночной. Двойная обратная косая черта является escape-символом в JSON-файле.

    Пример:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Раскомментирование кода, который активирует хранилище ключей

1. Откройте решение Tailspin.Surveys.

2. В [Tailspin.Surveys.Web/Startup.cs][web-startup] найдите указанный ниже блок кода и раскомментируйте его.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. В [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup] найдите указанный ниже блок кода и раскомментируйте его.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. В [Tailspin.Surveys.Web/Startup.cs][web-startup] найдите код, который регистрирует `ICredentialService`. Раскомментируйте строку, в которой используется `CertificateCredentialService`, и закомментируйте строку, в которой используется `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Это изменение позволяет веб-приложению использовать [утверждение клиента][client-assertion] для получения маркеров доступа OAuth. Утверждение клиента устраняет необходимость в секрете клиента OAuth. Либо же секрет клиента можно сохранить в хранилище ключей. Однако и хранилище ключей, и утверждение клиента используют сертификат клиента, поэтому при активации хранилища ключей рекомендуется также включить утверждение клиента.

### Обновление секретов пользователя

В обозревателе решений щелкните правой кнопкой мыши проект Tailspin.Surveys.Web и выберите пункт **Управление секретами пользователей**. В файле secrets.json удалите существующий код JSON и вставьте следующий:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Замените записи в [квадратных скобках] надлежащими значениями.

- `AzureAd:ClientId`: идентификатор клиента приложения Surveys.
- `AzureAd:WebApiResourceId`: код URI идентификатора приложения, указанный при создании приложения Surveys.WebAPI в Azure AD.
- `Asymmetric:CertificateThumbprint`: отпечаток сертификата, полученный ранее при создании сертификата клиента.
- `KeyVault:Name`: имя хранилища ключей.

> [AZURE.NOTE] `Asymmetric:ValidationRequired` имеет значение False, так как созданный ранее сертификат не был подписан в корневом центре сертификации. В рабочей среде используйте сертификат, подписанный в центре сертификации, и задайте для `ValidationRequired` значение True.

Сохраните обновленный файл secrets.json.

Затем в обозревателе решений щелкните правой кнопкой мыши проект Tailspin.Surveys.WebApi и выберите пункт **Управление секретами пользователей**. Удалите существующий код JSON и вставьте следующий:

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Замените записи в [квадратных скобках] и сохраните файл secrets.json.

> [AZURE.NOTE] В веб-API обязательно должен использоваться идентификатор клиента для приложения Surveys.WebAPI, а не для приложения Surveys.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[Теги ключей]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[цикл статей]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->