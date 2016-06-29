<properties
   pageTitle="Создание приложения AD с помощью PowerShell | Microsoft Azure"
   description="Описывает, как использовать Azure PowerShell для создания приложения Active Directory и предоставления ему доступа к ресурсам с управлением доступом на основе ролей. В статье показано, как выполнять проверку подлинности приложения с помощью пароля или сертификата."
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
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Создание приложения Active Directory для доступа к ресурсам с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)


В этой статье показано, как использовать [Azure PowerShell](powershell-install-configure.md) для создания приложения Active Directory (AD) (например, автоматизированного процесса, приложения или службы), которое сможет обращаться к другим ресурсам в вашей подписке. Azure Resource Manager позволяет использовать управление доступом на основе ролей для предоставления приложению разрешений на выполнение действий.

В этой статье вы создадите два объекта — приложение AD и субъект-службу. Приложение AD находится в клиенте, в котором зарегистрировано приложение. Оно определяет, какие процессы следует выполнять. Субъект-служба содержит удостоверение приложения AD и используется для назначения разрешений. Приложение AD позволяет создать множество субъектов-служб. Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](./active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Существует два варианта проверки подлинности для приложения:

 - пароль — используйте, если пользователь выполняет интерактивный вход;
 - сертификат — используйте для автоматических сценариев, в которых проверка подлинности выполняется без участия пользователя.

## Создание приложения AD с паролем

В этом разделе вы выполните действия по созданию приложения AD с паролем.

1. Войдите в свою учетную запись.

        Add-AzureRmAccount

1. Создайте новое приложение Active Directory. Для этого укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Проверьте новый объект приложения.

        $azureAdApplication
        
     Обратите внимание, свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения маркеров доступа.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### Создание субъекта-службы и назначение роли для него

Из приложения AD необходимо создать субъект-службу и назначить ей роль.

1. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Предоставление учетных данных вручную с помощью PowerShell

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь следует войти в систему от имени приложения для выполнения операций. Учетные данные для субъекта-службы можно вводить вручную при выполнении сценария или команд по запросу.

1. Создайте новый объект **PSCredential**, содержащий ваши учетные данные, выполнив команду **Get-Credential**.

        $creds = Get-Credential

2. Появится запрос на ввод ваших учетных данных. В качестве имени пользователя используйте значение **ApplicationId** или **IdentifierUris**, которое применялось при создании приложения. Укажите пароль, который вы задали при создании учетной записи.

     ![введите учетные данные](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. Получите подписку, в которой было создано назначение ролей. Эта подписка будет использоваться для получения **TenantId** клиента, к которому относится роль, назначенная для этого субъекта-службы.

        $subscription = Get-AzureRmSubscription

     Если ваша учетная запись связана с несколькими подписками, укажите имя или идентификатор подписки, чтобы гарантированно получить нужную подписку.
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. Выполните вход от имени субъекта-службы. Для этого укажите, что учетная запись является субъектом-службой, и предоставьте объект учетных данных.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

5. Сохраните профиль, чтобы повторно использовать текущий маркер доступа в следующем сеансе.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Вы можете открыть профиль и изучить его содержимое. Обратите внимание, что он содержит маркер доступа.
        
6. В следующий раз, когда потребуется выполнить код от имени субъекта-службы, можно просто загрузить профиль вместо входа с проверкой подлинности.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Маркер доступа имеет ограниченный срок действия, поэтому сохраненный профиль будет работать только в пределах этого срока. Чтобы сценарии всегда выполнялись без участия пользователя, используйте сертификат.
        
## Создание приложения AD с сертификатом

В этом разделе вы выполните действия по созданию приложения AD с сертификатом.

1. Создать самозаверяющий сертификат. Если используется Windows 10 или Windows Server 2016 Technical Preview выполните следующую команду. 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Вы получите сведения о сертификате, в том числе отпечаток.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Если у вас не используется Windows 10 или Windows Server 2016 Technical Preview, скачайте сценарий PowerShell [генератора самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6). Выполните следующие команды для создания сертификата.
     
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Получите из сертификата значение ключа.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Войдите в учетную запись Azure.

        Add-AzureRmAccount

4. Создайте приложение в каталоге.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Проверьте новый объект приложения.

        $azureAdApplication

    Обратите внимание: свойство **ApplicationId** требуется для создания субъектов-служб, назначения ролей и получения маркеров доступа.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### Создание субъекта-службы и назначение роли для него

1. Создайте субъект-службу для приложения, передав идентификатор приложения Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Для параметра **ServicePrincipalName** укажите значение **ApplicationId** или **IdentifierUris**, которое использовалось при создании приложения. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Подготовка значений для сценария

В сценарии вы передадите три значения, которые нужны для входа от имени субъекта-службы. Вам потребуются следующие значения.

- Идентификатор приложения
- Идентификатор клиента 
- Отпечаток сертификата

Вы уже получили идентификатор приложения и отпечаток сертификата на предыдущих этапах. Но если вам потребуется получить эти значения позднее, вы можете использовать для этого приведенные ниже команды. Там же есть команда для получения идентификатора клиента.

1. Чтобы получить идентификатор клиента, используйте команду:

        (Get-AzureRmSubscription).TenantId 

    А если у вас несколько подписок, введите имя подписки:

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. Чтобы получить идентификатор приложения, используйте команду:

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. Чтобы получить отпечаток сертификата, используйте команду:

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### Предоставление сертификата с помощью автоматизированного сценария PowerShell

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь вам нужно войти в систему в качестве субъекта-службы и выполнить операции от имени субъекта-службы.

Для проверки подлинности в сценарии укажите, что учетная запись является субъектом-службой и предоставьте отпечаток сертификата, идентификатор приложения и идентификатор клиента.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

## Дальнейшие действия
  
- Примеры аутентификации для .NET см. в статье [Пакет SDK .Net для диспетчера ресурсов Azure](resource-manager-net-sdk.md).
- Примеры аутентификации для Java см. в статье [Пакет SDK Java для диспетчера ресурсов Azure](resource-manager-java-sdk.md). 
- Примеры аутентификации для Python см. в статье [Resource Management Authentication](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html) (Аутентификация для управления ресурсами).
- Примеры аутентификации для REST см. в статье [API-интерфейсы REST диспетчера ресурсов](resource-manager-rest-api.md).
- Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0615_2016-->