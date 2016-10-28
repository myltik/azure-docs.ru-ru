<properties
   pageTitle="Создание субъекта-службы Azure с помощью Azure PowerShell | Microsoft Azure"
   description="Использование Azure PowerShell для создания приложения Active Directory и субъекта-службы с последующим предоставлением доступа к ресурсам с управлением доступом на основе ролей. В статье показано, как выполнять проверку подлинности приложения с помощью пароля или сертификата."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)

При наличии приложения или скрипта, которому требуется доступ к ресурсам, вы, вероятно, не захотите, чтобы этот процесс осуществлялся под вашими учетными данными. Ваша учетная запись может иметь другие разрешения, необходимые для приложения, и использование ваших учетных данных приложением при изменении обязанностей будет нежелательным. Вместо этого для приложения можно создать удостоверение, содержащее учетные данные проверки подлинности и назначения ролей. При каждом запуске приложения выполняется автоматическая проверка подлинности с этими учетными данными. В этой статье показано, как настроить использование собственных учетных данных и удостоверения для приложения с помощью [Azure PowerShell](powershell-install-configure.md).

В PowerShell доступно два варианта проверки подлинности для приложения AD:

 - пароль
 - на основе сертификата.

В этой статье показано, как применить оба варианта в PowerShell. Если вам нужно выполнять вход в Azure с платформы для программирования (например, Python, Ruby или Node.js), мы советуем использовать проверку подлинности на основе пароля. Прежде чем решить, какой тип проверки подлинности использовать (сертификат или пароль), просмотрите примеры проверки подлинности на разных платформах в разделе [Примеры приложений](#sample-applications).

## Основные понятия Active Directory

В этой статье вы создадите два объекта: приложение Active Directory (AD) и субъект-службу. Приложение AD является глобальным представлением вашего приложения. Оно содержит учетные данные (идентификатор приложения, а также пароль или сертификат). Субъект-служба — это локальное представление вашего приложения в Active Directory. Она содержит назначение роли. В этой статье описывается однотенантное приложение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации. В однотенантном приложении содержится одно приложение AD и один субъект-служба.

Вы, возможно, зададитесь вопросом, зачем нужно использовать оба объекта. Этот подход более рационален, когда речь идет о мультитенантных приложениях. Обычно в качестве мультитенантного приложения используется приложение на базе модели "программное обеспечение как услуга" (SaaS). Такое приложение работает в нескольких разных подписках. Мультитенантное приложение содержит одно приложение AD и несколько субъектов-служб (по одной на каждую учетную запись Active Directory, предоставляющую доступ к приложению). Сведения о настройке мультитенантного приложения см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).

## Необходимые разрешения

Для работы с этой статьей у вас должен быть достаточный уровень разрешений в подписке в Azure Active Directory и Azure. В частности, вы должны иметь право на создание приложения в Active Directory и назначение роли субъекту-службе.

В Active Directory ваша учетная запись должна иметь права администратора (например, **глобального администратора** или **администратора пользователей**). Если вашей учетной записи назначена роль **пользователя**, вам нужно отправить запрос администратору на повышение уровня ваших разрешений.

В вашей подписке учетная запись должна иметь доступ `Microsoft.Authorization/*/Write`, который предоставляется с ролью [владельца](./active-directory/role-based-access-built-in-roles.md#owner) или [администратора доступа пользователей](./active-directory/role-based-access-built-in-roles.md#user-access-administrator). Если вашей учетной записи назначена роль **участника**, вы получите сообщение об ошибке при попытке назначить роль субъекту-службе. Как и в предыдущем случае, администратор вашей подписки должен предоставить вам достаточный уровень разрешений для доступа.

Теперь перейдите к соответствующему разделу, чтобы выполнить проверку подлинности на основе [пароля](#create-service-principal-with-password) или [сертификата](#create-service-principal-with-certificate).

## Создание субъекта-службы с использованием пароля

В этом разделе содержатся инструкции, которые помогут вам:

- создать приложение AD с паролем;
- создать субъект-службу;
- назначить роль "Читатель" субъекту-службе.

Чтобы быстро выполнить эти шаги, можно использовать три следующих командлета.

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Давайте остановимся на этих шагах подробнее, чтобы разобраться со всей процедурой.

1. Войдите в свою учетную запись.

        Add-AzureRmAccount

1. Создайте новое приложение Active Directory. Для этого укажите отображаемое имя, универсальный код ресурса (URI), описывающий приложение, коды URI, идентифицирующие приложение, и пароль для его идентификации.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Для однотенантных приложений коды URI не проверяются.
     
     Если ваша учетная запись не имеет [требуемых разрешений](#required-permissions) для Active Directory, вы увидите сообщение об ошибке с текстом "Authentication\_Unauthorized" (Аутентификация отклонена) или "No subscription found in the context" (В этом контексте подписки не обнаружены).

1. Проверьте новый объект приложения.

        $app
        
     Обратите внимание на свойство **ApplicationId**, которое требуется для создания субъектов-служб, назначения ролей и получения маркера доступа.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере показано, как назначить субъекту-службе роль **читателя**, которая дает разрешение на чтение всех ресурсов в подписке. Сведения о других ролях см. в статье [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md). Для параметра **ServicePrincipalName** укажите значение **ApplicationId**, которое использовалось при создании приложения.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Если у вашей учетной записи нет достаточных разрешений для назначения ролей, вы получите сообщение об ошибке. В нем будет указано, что у вашей учетной записи **нет разрешения на выполнение действия Microsoft.Authorization/roleAssignments/write в области /subscriptions/{guid}**.

Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как выполнить вход с помощью учетных данных через PowerShell. Если вы хотите использовать свои учетные данные в коде приложения, вы можете перейти к [примерам приложений](#sample-applications).

### Предоставление учетных данных с помощью PowerShell

Теперь следует войти в систему от имени приложения для выполнения операций.

1. Создайте объект **PSCredential**, который содержит ваши учетные данные, выполнив команду **Get-Credential**. Для выполнения следующей команды требуется значение **ApplicationId**. Убедитесь, что оно доступно.

        $creds = Get-Credential

2. Появится запрос на ввод ваших учетных данных. В качестве имени пользователя используйте значение **ApplicationId**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

     ![введите учетные данные](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Active Directory. Если у вас только одна подписка, используйте команду:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     При наличии нескольких подписок выберите ту, где расположена ваша учетная запись Active Directory. Дополнительные сведения см. в статье [Связь между подписками Azure и службой Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Выполните вход от имени субъекта-службы. Для этого укажите, что учетная запись является субъектом-службой, и предоставьте объект учетных данных.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

### Сохранение маркера доступа для быстрого входа

Чтобы не вводить учетные данные субъекта-службы каждый раз при входе, вы можете сохранить маркер доступа.

1. Сохраните профиль, чтобы повторно использовать текущий маркер доступа в следующем сеансе.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Откройте профиль и изучите его содержимое. Обратите внимание, что он содержит маркер доступа.
        
2. Вместо выполнения входа вручную просто загрузите профиль.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Маркер доступа имеет ограниченный срок действия, поэтому сохраненный профиль работает только в пределах этого срока.
        
## Создание субъекта-службы с использованием сертификата

В этом разделе содержатся инструкции, которые помогут вам:

- создать самозаверяющий сертификат;
- создать приложение AD с сертификатом;
- создать субъект-службу;
- назначить роль "Читатель" субъекту-службе.

Чтобы быстро выполнить эти шаги с помощью Azure PowerShell 2.0 в Windows 10 или Windows Server 2016 Technical Preview, см. следующие командлеты.

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Давайте остановимся на этих шагах подробнее, чтобы разобраться со всей процедурой. В этой статье также показано, как выполнить задачи, используя более ранние версии Azure PowerShell или операционных систем.

### Создание самозаверяющего сертификата

Версия PowerShell, доступная в Windows 10 и Windows Server 2016 Technical Preview, включает обновленный командлет **New-SelfSignedCertificate** для создания самозаверяющего сертификата. В операционных системах более ранних версий используется командлет New-SelfSignedCertificate, но он не предоставляет параметры, необходимые для этой статьи. Вместо этого необходимо импортировать модуль для создания сертификата. В этой статье демонстрируются оба подхода к созданию сертификата на базе той версии операционной системы, которая установлена на вашем компьютере.

- Если используется **Windows 10 или Windows Server 2016 Technical Preview**, выполните следующую команду для создания самозаверяющего сертификата.

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Если вы **не используете Windows 10 или Windows Server 2016 Technical Preview**, скачайте сценарий [генератора самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) из центра сценариев Microsoft. Извлеките содержимое сценария и импортируйте требуемый командлет.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Затем создайте сертификат.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Теперь у вас есть сертификат, и вы можете приступить к созданию приложения AD.

### Создание приложения Active Directory и субъекта-службы

1. Получите из сертификата значение ключа.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Войдите в учетную запись Azure.

        Add-AzureRmAccount

3. Создайте новое приложение Active Directory. Для этого укажите отображаемое имя, универсальный код ресурса (URI), описывающий приложение, коды URI, идентифицирующие приложение, и пароль для его идентификации.

     Если у вас установлена версия Azure PowerShell 2.0 (выпуск за август 2016 г. или позднее), выполните следующий командлет:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Если у вас установлена версия Azure PowerShell 1.0, выполните следующий командлет:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Для однотенантных приложений коды URI не проверяются.
    
    Если ваша учетная запись не имеет [требуемых разрешений](#required-permissions) для Active Directory, вы увидите сообщение об ошибке с текстом "Authentication\_Unauthorized" (Аутентификация отклонена) или "No subscription found in the context" (В этом контексте подписки не обнаружены).
        
    Проверьте новый объект приложения.

        $app

    Обратите внимание на свойство **ApplicationId**, которое требуется для создания субъектов-служб, назначения ролей и получения маркеров доступа.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере показано, как назначить субъекту-службе роль **читателя**, которая дает разрешение на чтение всех ресурсов в подписке. Сведения о других ролях см. в статье [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md). Для параметра **ServicePrincipalName** укажите значение **ApplicationId**, которое использовалось при создании приложения.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Если у вашей учетной записи нет достаточных разрешений для назначения ролей, вы получите сообщение об ошибке. В нем будет указано, что у вашей учетной записи **нет разрешения на выполнение действия Microsoft.Authorization/roleAssignments/write в области /subscriptions/{guid}**.

Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как с помощью PowerShell выполнить вход с использованием сертификата.

### Предоставление сертификата с помощью автоматизированного сценария PowerShell

При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Active Directory. Если у вас только одна подписка, используйте команду:

    $tenant = (Get-AzureRmSubscription).TenantId
    
При наличии нескольких подписок выберите ту, где расположена ваша учетная запись Active Directory. Дополнительные сведения см. в статье [Администрирование каталога Azure AD](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Для проверки подлинности в сценарии укажите, что учетная запись является субъектом-службой и предоставьте отпечаток сертификата, идентификатор приложения и идентификатор клиента. Чтобы автоматизировать сценарий, эти значения можно сохранить как переменные среды, которые можно получить во время выполнения, или добавить в сценарий.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

## Примеры приложений

Следующие примеры приложений демонстрируют вход в качестве субъекта-службы.

**.NET**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона с помощью .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Приступая к работе с ресурсами: развертывание с помощью шаблона Azure Resource Manager на Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Приступая к работе с ресурсами: управление группой ресурсов на Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона на Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Управление ресурсами и группами ресурсов Azure с помощью Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## Дальнейшие действия
  
- Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).
- Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](./active-directory/active-directory-application-objects.md).
- Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](./active-directory/active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_0914_2016-->