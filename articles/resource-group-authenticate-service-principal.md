<properties
   pageTitle="Проверка подлинности субъекта-службы в диспетчере ресурсов Azure"
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
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# Проверка подлинности субъекта-службы в диспетчере ресурсов Azure

В этом разделе показано, как разрешить субъекту-службе (например, автоматизированному процессу, приложению или службе) получать доступ к другим ресурсам в вашей подписке. Диспетчер ресурсов Azure позволяет использовать управление доступом на основе ролей для предоставления разрешений субъекту-службе и его проверки подлинности. В этом разделе показано, как использовать PowerShell и CLI Azure для назначения роли субъекту-службе и его проверки подлинности.

Здесь рассматривается осуществление проверки подлинности либо по имени пользователя и паролю, либо по сертификату.

Можно использовать Azure PowerShell или Azure CLI для Mac, Linux и Windows. Если вы еще не установили Azure PowerShell, см. статью [Как установить и настроить Azure PowerShell](./powershell-install-configure.md). Если у вас не установлен интерфейс CLI Azure для Mac, Linux или Windows, см. статью [Установка и настройка CLI Azure](xplat-cli-install.md). Подробнее об использовании портала для выполнения этих шагов см. в разделе [Создание приложения Active Directory и субъекта-службы с помощью портала](resource-group-create-service-principal-portal.md).

## Основные понятия
1. Azure Active Directory (AAD) — это служба управления удостоверениями и доступом, созданная для облака. Дополнительные сведения см. в статье [Служба Azure Active Directory](active-directory/active-directory-whatis.md).
2. Субъект-служба — это экземпляр приложения в каталоге, которому требуется доступ к другим ресурсам.
3. Приложение AD — запись в каталоге, которая связывает некоторое приложение со службой AAD. Дополнительные сведения см. в статье [Проверка подлинности в службе Azure AD: основы](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Проверка подлинности субъекта-службы по паролю: PowerShell

В этом разделе вы выполните действия, необходимые для создания субъекта-службы Azure Active Directory, назначите роль субъекту-службе и пройдете проверку подлинности, используя идентификатор приложения и пароль.

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. Войдите в свою учетную запись.

        PS C:\> Login-AzureRmAccount

1. Создайте новое приложение AAD, выполнив команду **New-AzureRmADApplication**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Проверьте новый объект приложения. Свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения токенов JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. Создайте субъект-службу для своего приложения.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](resource-group-rbac.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться позднее для получения **TenantId** клиента, к которому относится назначение ролей для данного субъекта-службы.

        PS C:\> $subscription = Get-AzureRmSubscription

     Если вы создали назначение ролей в подписке, которая отличается от выбранной в данный момент, можно указать параметр **SubscriptoinId** или **SubscriptionName**, чтобы получить другую подписку.

5. Чтобы войти в качестве субъекта-службы через PowerShell, создайте новый объект **PSCredential**, содержащий ваши учетные данные, выполнив команду **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Появится запрос на ввод ваших учетных данных.

     ![][1]

     В качестве имени пользователя используйте значение **ApplicationId** или **IdentifierUris**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

     Используйте введенные учетные данные в качестве входных данных для командлета **Login-AzureRmAccount**, который выполнит вход для субъекта-службы:

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     После этого субъект-служба для приложения AAD, которое вы создали, пройдет проверку.

6. Для проверки подлинности из приложения добавьте следующий код .NET. После получения токена вы сможете использовать ресурсы в подписке.

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



## Проверка подлинности субъекта-службы по сертификату: PowerShell

В этом разделе вы выполните действия, необходимые для создания субъекта-службы Azure Active Directory, назначите роль субъекту-службе и пройдете проверку подлинности, используя сертификат. В этом разделе предполагается, что вы уже выдали сертификат.

Здесь демонстрируются два способа работы с сертификатами: учетные данные ключа и значения ключа. Вы можете использовать любой из этих подходов.

Для начала задайте в PowerShell определенные значения, которые потребуются вам позднее при создании приложении.

1. Войдите в свою учетную запись.

        PS C:\> Login-AzureRmAccount

1. Для обоих подходов создайте объект X509Certificate из сертификата и извлеките значение ключа. Используйте путь сертификата и соответствующий пароль.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Если вы используете учетные данные ключа, создайте объект учетных данных ключа и присвойте ему значение `$keyValue` из предыдущего шага.

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

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Второй пример демонстрирует назначение учетных данных ключа.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    Проверьте новый объект приложения. Свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения токенов JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. Создайте субъект-службу для своего приложения.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

5. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](resource-group-rbac.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. Для проверки подлинности из приложения добавьте следующий код .NET. После извлечения клиента вы сможете получить доступ к ресурсам в подписке.

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
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
        

## Проверка подлинности субъекта-службы по паролю: CLI Azure

Для начала нужно создать субъект-службу. Для этого необходимо создать приложение в каталоге. В данном разделе приводятся инструкции по созданию приложения в каталоге.

1. Переключитесь в режим диспетчера ресурсов Azure и войдите учетную запись.

        azure config mode arm
        azure login

2. Создайте приложение AAD, выполнив команду **azure ad app create**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Возвращается приложение Azure AD. Свойство ApplicationId требуется для создания субъектов-служб, назначения ролей и получения токенов JWT.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

4. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](resource-group-rbac.md).

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. Определите **TenantId** клиента, к которому относится назначение роли субъекта-службы, путем вывода списка учетных записей и поиска в нем свойства **TenantId**.

        azure account list --json

6. Войдите, используя имя субъекта-службы в качестве удостоверения. В качестве имени пользователя используйте значение **ApplicationId**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    После этого субъект-служба для приложения AAD, которое вы создали, пройдет проверку.

## Аутентификация субъекта-службы по сертификату: Azure CLI

В этом разделе вы создадите субъект-службу для приложения Azure Active Directory, использующего для аутентификации сертификат. В этом разделе предполагается, что вы уже выдали сертификат и установили [OpenSSL](http://www.openssl.org/).

1. Создайте **PEM**-файл:

        openssl.exe pkcs12 -in examplecert.pfx -out examplecert.pem -nodes

2. Откройте **PEM**-файл и скопируйте данные сертификата.

3. Создайте новое приложение AAD, выполнив команду **azure ad app create**, и в качестве значения ключа укажите данные сертификата, скопированные на предыдущем шаге.

        azure ad app create -n "<your application name>" --home-page "<https://YourApplicationHomePage>" -i "<https://YouApplicationUri>" --key-value <certificate data>

## Дальнейшие действия
  
- Общие сведения о контроле доступа на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](resource-group-rbac.md).  
- Указания по использованию портала с субъектами-службами см. в статье [Создание нового субъекта-службы Azure с помощью портала Azure](./resource-group-create-service-principal-portal.md).  
- Рекомендации по обеспечению безопасной работы с диспетчером ресурсов Azure см. в статье [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_1217_2015-->