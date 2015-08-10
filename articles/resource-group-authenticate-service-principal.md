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
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Проверка подлинности субъекта-службы в диспетчере ресурсов Azure

В этом разделе показано, как разрешить субъекту-службе (например, автоматизированному процессу, приложению или службе) получать доступ к другим ресурсам в вашей подписке. Диспетчер ресурсов Azure позволяет использовать управление доступом на основе ролей для предоставления разрешений субъекту-службе и его проверки подлинности. В этом разделе показано, как использовать PowerShell и CLI Azure для назначения роли субъекту-службе и его проверки подлинности.


## Основные понятия
1. Azure Active Directory (AAD) — это служба управления удостоверениями и доступом, созданная для облака. Дополнительные сведения см. в статье [Служба Azure Active Directory](active-directory/active-directory-whatis.md).
2. Субъект-служба — это экземпляр приложения в каталоге, которому требуется доступ к другим ресурсам.
3. Приложение AD — запись в каталоге, которая связывает некоторое приложение со службой AAD. Дополнительные сведения см. в статье [Проверка подлинности в службе Azure AD: основы](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Предоставление доступа и проверка подлинности субъекта-службы с помощью PowerShell

Если вы еще не установили Azure PowerShell, см. статью [Как установить и настроить Azure PowerShell](./powershell-install-configure.md).

Для начала нужно создать субъект-службу. Для этого необходимо создать приложение в каталоге. В данном разделе приводятся инструкции по созданию приложения в каталоге.

1. Создайте приложение AAD, выполнив команду **New-AzureADApplication**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Возвращается приложение Azure AD. Свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения токенов JWT. Сохраните выходные данные или передайте их переменной.

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. Создайте субъект-службу для своего приложения.

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](azure-portal/resource-group-rbac.md).

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться позднее для получения **TenantId** клиента, к которому относится назначение ролей для данного субъекта-службы.

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     Если вы создали назначение ролей в подписке, которая отличается от выбранной в данный момент, можно указать параметр **SubscriptoinId** или **SubscriptionName**, чтобы получить другую подписку.

5. Создайте новый объект **PSCredential**, содержащий ваши учетные данные, выполнив команду **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Появится запрос на ввод ваших учетных данных.

     ![][1]

     В качестве имени пользователя используйте значение **ApplicationId** или **IdentifierUris**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

6. Используйте введенные учетные данные в качестве входных данных для командлета **Add-AzureAccount**, который выполнит вход для субъекта-службы:

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     После этого субъект-служба для приложения AAD, которое вы создали, пройдет проверку.


## Предоставление доступа субъекту-службе и проверка его подлинности с помощью CLI Azure

Если у вас не установлен интерфейс CLI Azure для Mac, Linux или Windows, см. статью [Установка и настройка CLI Azure](xplat-cli-install.md).

1. Создайте приложение AAD, выполнив команду **azure ad app create**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

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

2. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](azure-portal/resource-group-rbac.md).

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. Определите **TenantId** клиента, к которому относится назначение роли субъекта-службы, путем вывода списка учетных записей и поиска в нем свойства **TenantId**.

        azure account list

5. Войдите, используя имя субъекта-службы в качестве удостоверения. В качестве имени пользователя используйте значение **ApplicationId**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    После этого субъект-служба для приложения AAD, которое вы создали, пройдет проверку.

## Дальнейшие действия
  
- Общие сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](azure-portal/resource-group-rbac.md).  
- Инструкции по использованию портала с субъектами-службами см. в статье [Создание нового субъекта-службы Azure с помощью портала Azure](./resource-group-create-service-principal-portal.md).  
- Рекомендации по обеспечению безопасной работы с диспетчером ресурсов Azure см. в статье [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=July15_HO5-->