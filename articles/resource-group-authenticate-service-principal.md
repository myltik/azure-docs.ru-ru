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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)

При наличии приложения или сценария, которому требуется доступ к ресурсам, вы, вероятно, не захотите, чтобы этот процесс осуществлялся под вашими учетными данными. Учетная запись может иметь другие разрешения, которые можно назначить процессу, что, в свою очередь, может повлечь за собой изменение обязанностей, предусмотренных заданием. Вместо этого для приложения можно создать удостоверение, содержащее учетные данные проверки подлинности и назначения ролей. Эта учетная запись будет использоваться при каждом запуске приложения. В этой статье показано, как настроить использование собственных учетных данных и удостоверения для приложения с помощью [Azure PowerShell](powershell-install-configure.md).

Кроме этого, в этой статье вы создадите два объекта — приложение Active Directory (AD) и субъект-службу. Приложение AD содержит учетные данные (идентификатор приложения и пароль или сертификат), а субъект-служба — назначение роли. Приложение AD позволяет создать множество субъектов-служб. В этой статье описывается однотенантное приложение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации. Если приложение будет использоваться несколькими организациями, необходимо применить мультитенантную архитектуру. Обычно такая архитектура используется для создания приложений "программное обеспечение как услуга" (SaaS). Сведения о настройке мультитенантного приложения см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).

При работе с Active Directory необходимо разобраться с многими понятиями. Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](./active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](./active-directory/active-directory-authentication-scenarios.md).

В PowerShell доступно два варианта проверки подлинности для приложения AD:

 - пароль
 - на основе сертификата.

Если после настройки приложения AD понадобится выполнять вход в Azure с другой платформы программирования (например, Python, Ruby или Node.js), рекомендуется использовать проверку подлинности на основе пароля. Прежде чем решить, какой тип проверки подлинности использовать (сертификат или пароль), просмотрите примеры проверки подлинности на разных платформах в разделе [Примеры приложений](#sample-applications).

## Получение идентификатора клиента

При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Active Directory. Идентификатор клиента понадобится нам при проверке подлинности, поэтому мы получим это значение сейчас.

1. Войдите в свою учетную запись.

        Add-AzureRmAccount

2. Если у вас только одна подписка, используйте команду:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     При наличии нескольких подписок выберите подписку, которую вы хотите использовать для приложения AD. Выберите подписку Active Directory. Дополнительные сведения см. в статье [Администрирование каталога Azure AD](./active-directory/active-directory-administer.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Теперь перейдите к разделу ниже, чтобы использовать проверку подлинности на основе [пароля](#create-service-principal-with-password) или [сертификата](#create-service-principal-with-certificate).

## Создание субъекта-службы с использованием пароля

В этом разделе описано создание приложения AD и субъекта-службы с использованием пароля.

1. Создайте новое приложение Active Directory. Для этого укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Проверьте новый объект приложения.

        $azureAdApplication
        
     Обратите внимание на свойство **ApplicationId**, которое требуется для создания субъектов-служб, назначений ролей и получения маркера доступа.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


     Из приложения AD необходимо создать субъект-службу и назначить ей роль.

2. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md). Чтобы назначить роль, требуется доступ `Microsoft.Authorization/*/Write`, который предоставляется с помощью роли [Владелец](./active-directory/role-based-access-built-in-roles.md#owner) или [Администратор доступа пользователей](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как с помощью PowerShell выполнить вход, используя учетные данные. Если вы хотите использовать учетные данные в коде приложения, выполнять действия, описанные ниже, не требуется. Вы можете перейти к разделу [Примеры приложений](#sample-applications), где приведены примеры входа с использованием идентификатора приложения и пароля.

### Предоставление учетных данных с помощью PowerShell

Теперь следует войти в систему от имени приложения для выполнения операций.

1. Создайте новый объект **PSCredential**, содержащий ваши учетные данные, выполнив команду **Get-Credential**. Для выполнения следующей команды вам потребуется значение **ApplicationId** или **IdentifierUris**. Убедитесь, что они доступны.

        $creds = Get-Credential

2. Появится запрос на ввод ваших учетных данных. В качестве имени пользователя используйте значение **ApplicationId** или **IdentifierUris**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

     ![введите учетные данные](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. Выполните вход от имени субъекта-службы. Для этого укажите, что учетная запись является субъектом-службой, и предоставьте объект учетных данных. Вам потребуется идентификатор клиента, полученный при работе с разделом [Получение идентификатора клиента](#get-tenant-id).

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

5. Сохраните профиль, чтобы повторно использовать текущий маркер доступа в следующем сеансе.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Вы можете открыть профиль и изучить его содержимое. Обратите внимание, что он содержит маркер доступа.
        
6. В следующий раз, когда потребуется выполнить код от имени субъекта-службы, вместо входа с проверкой подлинности можно просто загрузить профиль.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Маркер доступа имеет ограниченный срок действия, поэтому сохраненный профиль будет работать только в пределах этого срока.
        
## Создание субъекта-службы с использованием сертификата

В этом разделе описывается создание приложения AD и субъекта-службы с использованием сертификата.

1. Создать самозаверяющий сертификат. Если используется **Windows 10 или Windows Server 2016 Technical Preview**, выполните следующую команду.

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Эта переменная содержит сведения о сертификате, в том числе отпечаток.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     В **других** версиях операционной системы Windows командлет **New-SelfSignedCertificate** недоступен. Вместо него скачайте сценарий PowerShell [генератора самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) и выполните следующие команды для создания сертификата. Если сертификат был создан с помощью командлета New-SelfSignedCertificate, выполнять это действие не нужно.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Получите из сертификата значение ключа.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

4. Создайте приложение в каталоге.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Проверьте новый объект приложения.

        $azureAdApplication

    Обратите внимание на свойство **ApplicationId**, которое требуется для создания субъектов-служб, назначений ролей и получения маркеров доступа.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


5. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md). Чтобы назначить роль, требуется доступ `Microsoft.Authorization/*/Write`, который предоставляется с помощью роли [Владелец](./active-directory/role-based-access-built-in-roles.md#owner) или [Администратор доступа пользователей](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как с помощью PowerShell выполнить вход с использованием сертификата.

### Предоставление сертификата с помощью автоматизированного сценария PowerShell

Для проверки подлинности в сценарии укажите, что учетная запись является субъектом-службой и предоставьте отпечаток сертификата, идентификатор приложения и идентификатор клиента. Эти значения уже содержатся в переменных **$azureAdApplication.ApplicationId**, **$cert.Thumbprint** и **$tenant**. Чтобы автоматизировать сценарий, эти значения можно сохранить как переменные среды, которые можно получить во время выполнения, или добавить в сценарий.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

Чтобы получить идентификатор приложения, используйте следующую команду:

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
Чтобы получить отпечаток сертификата, используйте следующую команду:

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

Сведения о получении идентификатора клиента см. в разделе [Получение идентификатора клиента](#get-tenant-id).

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

<!---HONumber=AcomDC_0720_2016-->