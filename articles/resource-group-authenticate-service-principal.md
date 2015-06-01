<properties
   pageTitle="Проверка подлинности субъекта-службы в диспетчере ресурсов Azure"
   description="В статье объясняется, как предоставить доступ субъекту-службе с помощью управления доступом на основе ролей, а также проверить его подлинность. Показано, как выполнять эти задачи с помощью PowerShell и CLI Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Проверка подлинности субъекта-службы в диспетчере ресурсов Azure

В этом разделе показано, как разрешить субъекту-службе (например, автоматизированному процессу, приложению или службе) получать доступ к другим ресурсам в вашей подписке. Диспетчер ресурсов Azure позволяет использовать управление доступом на основе ролей для предоставления разрешений субъекту-службе и его проверки подлинности. В этом разделе показано, как использовать PowerShell и CLI Azure для назначения роли субъекту-службе и его проверки подлинности.


## Основные понятия
1. Azure Active Directory (AAD) — это служба управления удостоверениями и доступом, созданная для облака. Дополнительные сведения см. в статье [Служба Azure Active Directory](./active-directory-whatis.md).
2. Субъект-служба — экземпляр приложения в каталоге.
3. Приложение AD — запись в каталоге, которая связывает некоторое приложение со службой AAD. Дополнительные сведения см. в статье [Проверка подлинности в службе Azure AD: основы](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Предоставление доступа и проверка подлинности субъекта-службы с помощью PowerShell

Если вы еще не установили Azure PowerShell, см. статью [Как установить и настроить Azure PowerShell](./powershell-install-configure.md).

Для начала нужно создать субъект-службу. Для этого необходимо создать приложение в каталоге. В данном разделе приводятся инструкции по созданию приложения в каталоге.

1. Создайте приложение AAD, выполнив команду **New-AzureADApplication**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        PS C:> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

   Возвращается приложение Azure AD:

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


   [AZURE.NOTE] Свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения токенов JWT. Сохраните выходные данные или передайте их переменной.

3. Создайте субъект-службу для своего приложения.

        PS C:> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

   Вы создали субъект-службу в каталоге, но службе не назначены разрешения и область. Необходимо явным образом предоставить субъекту-службе разрешения на выполнение операций в некоторой области.

4. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](./resource-group-rbac.md).

        PS C:> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

5. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться позднее для получения **TenantId** клиента, к которому относится назначение ролей для данного субъекта-службы.

        PS C:> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

   Если вы создали назначение ролей в подписке, которая отличается от выбранной в данный момент, можно указать параметр **SubscriptoinId** или **SubscriptionName**, чтобы получить другую подписку.

6. Создайте новый объект **PSCredential**, содержащий ваши учетные данные, выполнив команду **Get-Credential**.

        PS C:> $creds = Get-Credential

   Появится запрос на ввод ваших учетных данных.

   ![][1]

   В качестве имени пользователя используйте значение **ApplicationId** или **IdentifierUris**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

7. Используйте введенные учетные данные в качестве входных данных для командлета **Add-AzureAccount**, который выполнит вход для субъекта-службы:

        PS C:> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

   После этого субъект-служба для приложения AAD, которое вы создали, пройдет проверку.


## Предоставление доступа субъекту-службе и проверка его подлинности с помощью CLI Azure

Если у вас не установлен интерфейс CLI Azure для Mac, Linux или Windows, см. статью [Установка и настройка CLI Azure](xplat-cli-install.md).

Для выполнения этих шагов требуются приложение AD и субъект-служба. Сведения о том, как настроить приложение AD и субъект-службу на классическом портале Azure, см. в статье [Создание субъекта-службы Azure с помощью классического портала Azure](./resource-group-create-service-principal-portal.md).

1. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения об управлении доступом на основе ролей см. в статье [Управление доступом к ресурсам и его аудит](./resource-group-rbac.md).

        azure role assignment create --objectId {service-principal-object-id} -o Reader -c /subscriptions/{subscriptionId}/

2. Определите **TenantId** клиента, к которому относится назначение роли субъекта-службы, путем вывода списка учетных записей и поиска в нем свойства **TenantId**.

        azure account list

3. Войдите, используя имя субъекта-службы в качестве удостоверения. В качестве имени пользователя используйте значение **ApplicationId**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

  После этого субъект-служба для приложения AAD, которое вы создали, пройдет проверку.

## Дальнейшие действия
Приступая к работе

- [Общие сведения о диспетчере ресурсов Azure](./resource-group-overview.md)  
- [Использование Azure PowerShell с диспетчером ресурсов Azure](./powershell-azure-resource-manager.md)
- [Использование CLI Azure для Mac, Linux и Windows со средствами управления ресурсами Azure](./xplat-cli-azure-resource-manager.md)  
- [Управление ресурсами Azure с помощью портала Azure](./resource-group-portal.md)  
  
Создание и развертывание приложений
  
- [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md)  
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](./resource-group-template-deploy.md)  
- [Устранение неполадок при развертывании групп ресурсов в Azure](./resource-group-deploy-debug.md)  
- [Функции шаблонов в диспетчере ресурсов Azure](./resource-group-template-functions.md)  
- [Расширенные операции с шаблонами](./resource-group-advanced-template.md)  
- [Развертывание ресурсов Azure с использованием библиотек .NET и шаблона](./arm-template-deployment.md)
  
Упорядочение ресурсов
  
- [Использование тегов для организации ресурсов в Azure](./resource-group-using-tags.md)  
  
Управление доступом и его аудит
  
- [Управление доступом к ресурсам и его аудит](./resource-group-rbac.md)  
- [Создание нового субъекта-службы Azure с помощью портала Azure](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!--HONumber=52-->
