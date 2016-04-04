<properties
   pageTitle="Проверка подлинности субъекта-службы в диспетчере ресурсов Azure | Microsoft Azure"
   description="В статье объясняется, как предоставить доступ субъекту-службе с помощью управления доступом на основе ролей, а также проверить его подлинность. Показано, как выполнять эти задачи с помощью PowerShell и CLI Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Проверка подлинности субъекта-службы в диспетчере ресурсов Azure

В этом разделе показано, как разрешить субъекту-службе (например, автоматизированному процессу, приложению или службе) получать доступ к другим ресурсам в вашей подписке. Диспетчер ресурсов Azure позволяет использовать управление доступом на основе ролей для предоставления разрешений субъекту-службе и его проверки подлинности.

В этой статье рассказывается, как создавать приложение и субъект-службу, назначать субъекту-службе роль и проходить проверку подлинности в качестве субъекта-службы, используя Azure PowerShell или интерфейс командной строки Azure для Mac, Linux и Windows. Если вы еще не установили Azure PowerShell, см. статью [Как установить и настроить Azure PowerShell](./powershell-install-configure.md). Если у вас не установлен интерфейс CLI Azure для Mac, Linux или Windows, см. статью [Установка и настройка CLI Azure](xplat-cli-install.md). Подробнее об использовании портала для выполнения этих шагов см. в разделе [Создание приложения Active Directory и субъекта-службы с помощью портала](resource-group-create-service-principal-portal.md).

## Основные понятия
1. Azure Active Directory — это служба управления удостоверениями и доступом, созданная для облака. Дополнительные сведения см. в статье [Что такое Azure Active Directory](active-directory/active-directory-whatis.md).
2. Субъект-служба — это экземпляр приложения в каталоге, которому требуется доступ к другим ресурсам.
3. Приложение Active Directory — запись в каталоге, которая связывает некоторое приложение со службой AAD.

Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](active-directory/active-directory-authentication-scenarios.md).

В этом разделе описываются 4 пути для создания приложения Active Directory и проверки подлинности таких приложений. Выбор пути зависит от предпочитаемого вами инструмента (PowerShell или интерфейс командной строки Azure) и того, планируете ли вы использовать для проверки подлинности пароль или сертификат. Вот эти пути:

- [Проверка подлинности по паролю: PowerShell](#authenticate-with-password---powershell)
- [Проверка подлинности по сертификату: PowerShell](#authenticate-with-certificate---powershell)
- [Проверка подлинности по паролю: интерфейс командной строки Azure](#authenticate-with-password---azure-cli)
- [Проверка подлинности по сертификату: интерфейс командной строки Azure](#authenticate-with-certificate---azure-cli)

## Проверка подлинности по паролю: PowerShell

В этом разделе вы выполните действия, необходимые для создания субъекта-службы Azure Active Directory, назначите роль субъекту-службе и пройдете проверку подлинности, используя идентификатор приложения и пароль.

1. Войдите в свою учетную запись.

        PS C:\> Login-AzureRmAccount

1. Создайте новое приложение Active Directory, выполнив команду **New-AzureRmADApplication**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Проверьте новый объект приложения. Свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения токенов доступа.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь вам нужно войти в систему в качестве субъекта-службы и выполнить операции от имени субъекта-службы. В этом разделе представлены три варианта:

- [Предоставление учетных данных вручную с помощью PowerShell](#manually-provide-credentials-through-powershell)
- [Предоставление учетных данных с помощью автоматизированного сценария PowerShell](#provide-credentials-through-automated-powershell-script)
- [Предоставление учетных данных с помощью кода в приложении](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### Предоставление учетных данных вручную с помощью PowerShell

Учетные данные для субъекта-службы можно ввести вручную при выполнении сценария или команд по запросу.

1. Создайте новый объект **PSCredential**, содержащий ваши учетные данные, выполнив команду **Get-Credential**.

        PS C:\> $creds = Get-Credential

2. Появится запрос на ввод ваших учетных данных. В качестве имени пользователя используйте значение **ApplicationId** или **IdentifierUris**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

     ![введите учетные данные][1]

3. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться для получения **TenantId** клиента, к которому относится назначение ролей для данного субъекта-службы.

        PS C:\> $subscription = Get-AzureRmSubscription

     Если вы создали назначение ролей в подписке, которая отличается от выбранной в данный момент, можно указать параметр **SubscriptoinId** или **SubscriptionName**, чтобы получить другую подписку.

4. Войдите в систему как субъект-служба с помощью командлета **Login-AzureRmAccount**, но предоставьте объект учетных данных и укажите, что учетная запись относится к субъекту-службе.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

<a id="provide-credentials-through-automated-powershell-script" />
### Предоставление учетных данных с помощью автоматизированного сценария PowerShell

В этом разделе показано, как войти в систему в качестве субъекта-службы без ручного ввода учетных данных. Вам не придется вводить пароль вручную, поскольку он извлекается из хранилища ключей.

> [AZURE.NOTE] Включать пароль непосредственно в сценарий PowerShell небезопасно, поскольку в этом случае он будет представлен в форме простого текста. Храните пароль в хранилище ключей, откуда он будет извлекаться при выполнении сценария.

Эти действия предполагают, что вы настроили хранилище ключей и секрет, в котором хранится пароль. Инструкции по развертыванию хранилища ключей и секрета с использованием шаблона см. в статье [Формат шаблона хранилища ключей](). Сведения о хранилище ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).

1. Извлеките пароль (в приведенном ниже примере он хранится в виде секрета с именем **appPassword**) из хранилища ключей.

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Получите приложение Active Directory. При входе нужно будет указать идентификатор приложения.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. Создайте новый объект **PSCredential**, указав в качестве учетных данных идентификатор приложения и пароль.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться позднее для получения **TenantId** клиента, к которому относится назначение ролей для данного субъекта-службы.

        PS C:\> $subscription = Get-AzureRmSubscription

     Если вы создали назначение ролей в подписке, которая отличается от выбранной в данный момент, можно указать параметр **SubscriptoinId** или **SubscriptionName**, чтобы получить другую подписку.

5. Войдите в систему как субъект-служба с помощью командлета **Login-AzureRmAccount**, но предоставьте объект учетных данных и укажите, что учетная запись относится к субъекту-службе.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

<a id="provide-credentials-through-code-in-an-application" />
### Предоставление учетных данных с помощью кода в приложении

Для проверки подлинности из приложения .NET добавьте следующий код: Вам потребуется идентификатор приложения Active Directory, пароль и идентификатор клиента для подписки. Получив токен доступа, вы сможете использовать ресурсы в подписке.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## Проверка подлинности по сертификату: PowerShell

В этом разделе вы выполните действия, необходимые для создания субъекта-службы Azure Active Directory, назначите роль субъекту-службе и пройдете проверку подлинности, используя сертификат. В этом разделе предполагается, что вы уже выдали сертификат.

Здесь демонстрируются два способа работы с сертификатами: учетные данные ключа и значения ключа. Вы можете использовать любой из этих подходов.

Для начала задайте в PowerShell определенные значения, которые потребуются вам позднее при создании приложении.

1. Войдите в свою учетную запись.

        PS C:\> Login-AzureRmAccount

1. Для обоих подходов создайте объект X509Certificate2 из сертификата и извлеките значение ключа. Используйте путь сертификата и соответствующий пароль.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Если вы используете учетные данные ключа, создайте объект учетных данных ключа и присвойте ему значение `$keyValue` из предыдущего шага. Приведенный ниже пример включает вызов метода `Add-Type` для добавления типа из сборки. Показанный в этом примере путь должен быть аналогичен вашему, но может несколько отличаться.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Создайте приложение в каталоге. Первая команда показывает, как использовать значения ключа.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Второй пример демонстрирует назначение учетных данных ключа.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    Проверьте новый объект приложения. Свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения токенов доступа.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

5. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь вам нужно войти в систему в качестве субъекта-службы и выполнить операции от имени субъекта-службы. В этом разделе представлены два варианта:

- [Предоставление сертификата с помощью автоматизированного сценария PowerShell](#provide-certificate-through-automated-powershell-script)
- [Предоставление сертификата с помощью кода в приложении](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### Предоставление сертификата с помощью автоматизированного сценария PowerShell

1. Получите приложение Active Directory. При входе нужно будет указать идентификатор приложения.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться позднее для получения **TenantId** клиента, к которому относится назначение ролей для данного субъекта-службы.

        PS C:\> $subscription = Get-AzureRmSubscription

     Если вы создали назначение ролей в подписке, которая отличается от выбранной в данный момент, можно указать параметр **SubscriptoinId** или **SubscriptionName**, чтобы получить другую подписку.

3. Получите сертификат, который будет использоваться для проверки подлинности.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. Для проверки подлинности в PowerShell укажите отпечаток сертификата, идентификатор приложения и идентификатор клиента.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

<a id="provide-certificate-through-code-in-an-application" />
### Предоставление сертификата с помощью кода в приложении

Для проверки подлинности из приложения .NET добавьте следующий код: После извлечения клиента вы сможете получить доступ к ресурсам в подписке.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
        

## Проверка подлинности по паролю: интерфейс командной строки Azure

Для начала нужно создать субъект-службу. Для этого необходимо создать приложение в каталоге. В данном разделе приводятся инструкции по созданию приложения в каталоге.

1. Переключитесь в режим диспетчера ресурсов Azure и войдите учетную запись.

        azure config mode arm
        azure login

2. Создайте приложение Active Directory, выполнив команду **azure ad app create**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Система вернет приложения Active Directory. Свойство AppId требуется для создания субъектов-служб, назначения ролей и получения токенов доступа.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

4. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь вам нужно войти в систему в качестве субъекта-службы и выполнить операции от имени субъекта-службы. В этом разделе представлены три варианта:

- [Предоставление учетных данных вручную через интерфейс командной строки Azure](#manually-provide-credentials-through-azure-cli)
- [Предоставление учетных данных с помощью автоматизированного сценария для интерфейса командной строки Azure](#provide-credentials-through-automated-azure-cli-script)
- [Предоставление учетных данных с помощью кода в приложении](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Предоставление учетных данных вручную через интерфейс командной строки Azure

Если вы хотите выполнить вход как субъект-служба вручную, воспользуйтесь командой **azure login**. При этом нужно указать идентификатор клиента, идентификатор приложения и пароль. Включать пароль непосредственно в сценарий небезопасно, поскольку в этом случае он сохраняется в файл. Более удачный вариант выполнения автоматизированного сценария см. в следующем разделе.

1. Определите значение **TenantId** для подписки, в которую входит субъект-служба. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, не нужно указывать идентификатор подписки в качестве параметра. Параметр **-r** позволяет получить значение без кавычек.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. В качестве имени пользователя используйте значение **AppId**, которое применялось при создании субъекта-службы. Чтобы получить идентификатор приложения, используйте указанную ниже команду. Укажите имя приложения Active Directory в параметре **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Выполните вход как субъект-служба.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

По запросу укажите пароль, который вы задали при создании учетной записи.

    info:    Executing command login
    Password: ********

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

<a id="provide-credentials-through-automated-azure-cli-script" />
### Предоставление учетных данных с помощью автоматизированного сценария для интерфейса командной строки Azure

В этом разделе показано, как войти в систему в качестве субъекта-службы без ручного ввода учетных данных.

> [AZURE.NOTE] Включать пароль в сценарий интерфейса командной строки Azure небезопасно, поскольку в этом случае он будет представлен в форме простого текста. Храните пароль в хранилище ключей, откуда он будет извлекаться при выполнении сценария.

Эти действия предполагают, что вы настроили хранилище ключей и секрет, в котором хранится пароль. Инструкции по развертыванию хранилища ключей и секрета с использованием шаблона см. в статье [Формат шаблона хранилища ключей](). Сведения о хранилище ключей см. в разделе [Приступая к работе с хранилищем ключей Azure](./key-vault/key-vault-get-started.md).

1. Извлеките пароль (в приведенном ниже примере он хранится в виде секрета с именем **appPassword**) из хранилища ключей. Добавьте параметр **-r**, чтобы удалить открывающие и закрывающие двойные кавычки из полученных выходных данных JSON.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq -r '.value')
    
2. Определите значение **TenantId** для подписки, в которую входит субъект-служба. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, не нужно указывать идентификатор подписки в качестве параметра.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. В качестве имени пользователя используйте значение **AppId**, которое применялось при создании субъекта-службы. Чтобы получить идентификатор приложения, используйте указанную ниже команду. Укажите имя приложения Active Directory в параметре **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Войдите в систему как субъект-служба, указав идентификатор приложения, пароль из хранилища ключей и идентификатор клиента.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

<a id="provide-credentials-through-code-in-an-application-cli" />
### Предоставление учетных данных с помощью кода в приложении

Для проверки подлинности из приложения .NET добавьте следующий код: Вам потребуется идентификатор приложения Active Directory, пароль и идентификатор клиента для подписки. Получив токен доступа, вы сможете использовать ресурсы в подписке.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## Проверка подлинности по сертификату: интерфейс командной строки Azure

В этом разделе вы создадите субъект-службу для приложения Azure Active Directory, использующего для аутентификации сертификат. В этом разделе предполагается, что вы уже выдали сертификат и установили [OpenSSL](http://www.openssl.org/).

1. Создайте **PEM**-файл:

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. Откройте **PEM**-файл и скопируйте данные сертификата. Найдите длинную последовательность символов между тегами **-----BEGIN CERTIFICATE-----** и **-----END CERTIFICATE-----**.

3. Создайте приложение Active Directory, выполнив команду **azure ad app create**, и в качестве значения ключа укажите данные сертификата, скопированные на предыдущем шаге.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Система вернет приложения Active Directory. Свойство AppId требуется для создания субъектов-служб, назначения ролей и получения токенов доступа.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь вам нужно войти в систему в качестве субъекта-службы и выполнить операции от имени субъекта-службы. В этом разделе представлены два варианта:

- [Предоставление сертификата с помощью автоматизированного сценария для интерфейса командной строки Azure](#provide-certificate-through-automated-azure-cli-script)
- [Предоставление сертификата с помощью кода в приложении](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### Предоставление сертификата с помощью автоматизированного сценария для интерфейса командной строки Azure

1. В этом случае необходимо получить отпечаток сертификата и удалить ненужные символы.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     Возвращается значение отпечатка следующего вида:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Определите значение **TenantId** для подписки, в которую входит субъект-служба. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, не нужно указывать идентификатор подписки в качестве параметра. Параметр **-r** позволяет получить значение без кавычек.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. В качестве имени пользователя используйте значение **AppId**, которое применялось при создании субъекта-службы. Чтобы получить идентификатор приложения, используйте указанную ниже команду. Укажите имя приложения Active Directory в параметре **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Для проверки подлинности в интерфейсе командной строки Azure укажите отпечаток сертификата, файл сертификата, идентификатор приложения и идентификатор клиента.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

<a id="provide-certificate-through-code-in-an-application-cli" />
### Предоставление сертификата с помощью кода в приложении

Для проверки подлинности из приложения .NET добавьте следующий код: После извлечения клиента вы сможете получить доступ к ресурсам в подписке.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
       
Дополнительные сведения об использовании сертификатов и интерфейса командной строки Azure см. в статье [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx).

## Дальнейшие действия
  
- Указания по использованию портала с субъектами-службами см. в статье [Создание нового субъекта-службы Azure с помощью портала Azure](./resource-group-create-service-principal-portal.md).  
- Рекомендации по обеспечению безопасной работы с диспетчером ресурсов Azure см. в статье [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0323_2016-->