<properties
   pageTitle="Создание приложения AD с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Описывает, как использовать командную строку Azure для создания приложения Active Directory и предоставления ему доступа к ресурсам с управлением доступом на основе ролей. В статье показано, как выполнять проверку подлинности приложения с помощью пароля или сертификата."
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
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Создание приложения Active Directory для доступа к ресурсам с помощью Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)

В этой статье показано, как использовать [Azure CLI для Mac, Linux и Windows](xplat-cli-install.md) для создания приложения Active Directory (AD) (например, автоматизированного процесса, приложения или службы) которое сможет обращаться к другим ресурсам в вашей подписке. Azure Resource Manager позволяет использовать управление доступом на основе ролей для предоставления приложению разрешений на выполнение действий.

В этой статье вы создадите два объекта — приложение AD и субъект-службу. Приложение AD находится в клиенте, в котором зарегистрировано приложение. Оно определяет, какие процессы следует выполнять. Субъект-служба содержит удостоверение приложения AD и используется для назначения разрешений. Приложение AD позволяет создать множество субъектов-служб. Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](./active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Существует два варианта проверки подлинности для приложения:

 - пароль — используйте, если пользователь выполняет интерактивный вход;
 - сертификат — используйте для автоматических сценариев, в которых проверка подлинности выполняется без участия пользователя.
 
## Создание приложения AD с паролем

В этом разделе вы выполните действия по созданию приложения AD с паролем.

1. Переключитесь в режим диспетчера ресурсов Azure и войдите учетную запись.

        azure config mode arm
        azure login

2. Создайте новое приложение AD, выполнив команду **azure ad app create**. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Возвращается приложение AD. Свойство AppId требуется для создания субъектов-служб и получения маркеров доступа.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Создание субъекта-службы и назначение роли для него

1. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

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

2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Предоставление учетных данных вручную через интерфейс командной строки Azure

Вы создали приложение AD и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь следует войти в систему от имени приложения для выполнения операций. Учетные данные для приложения можно вводить вручную при выполнении сценариев или команд по запросу.

1. Определите значение **TenantId** для подписки, в которую входит субъект-служба. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, не нужно указывать идентификатор подписки в качестве параметра. Параметр **-r** позволяет получить значение без кавычек.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. В качестве имени пользователя используйте значение **AppId**, которое вы указали при создании субъекта-службы. Чтобы получить идентификатор приложения, используйте указанную ниже команду. Укажите имя приложения Active Directory в параметре **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Выполните вход как субъект-служба.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    Появится запрос на ввод пароля. Введите пароль, который вы задали при создании приложения AD.

        info:    Executing command login
        Password: ********

Теперь вы прошли проверку подлинности от имени субъекта-службы для созданного вами приложения AD.

## Создание приложения AD с сертификатом

В этом разделе вы создадите субъект-службу для приложения AD, которое использует сертификат для проверки подлинности. Для выполнения этих действий должен быть установлен [OpenSSL](http://www.openssl.org/).

1. Создать самозаверяющий сертификат.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Объедините открытый и закрытый ключи.

        cat privkey.pem cert.pem > examplecert.pem

3. Откройте файл **examplecert.pem** и скопируйте данные сертификата. Найдите длинную последовательность символов между тегами **-----BEGIN CERTIFICATE-----** и **-----END CERTIFICATE-----**.

4. Создайте приложение Active Directory, выполнив команду **azure ad app create**, и в качестве значения ключа укажите данные сертификата, скопированные на предыдущем шаге.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Система вернет приложения Active Directory. Свойство AppId требуется для создания субъектов-служб и получения маркеров доступа.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Создание субъекта-службы и назначение роли для него

1. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

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
        
2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Подготовка значений для сценария

В сценарии вы передадите три значения, которые нужны для входа от имени субъекта-службы. Вам потребуются следующие значения.

- Идентификатор приложения
- Идентификатор клиента 
- Отпечаток сертификата

Вы уже получили идентификатор приложения и отпечаток сертификата на предыдущих этапах. Но если вам потребуется получить эти значения позднее, вы можете использовать для этого приведенные ниже команды. Там же есть команда для получения идентификатора клиента.

1. Чтобы получить отпечаток сертификата и удалить ненужные символы, используйте такую команду:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Возвращается значение отпечатка следующего вида:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Чтобы получить идентификатор клиента, используйте команду:

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. Чтобы получить идентификатор приложения, используйте команду:

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### Предоставление сертификата с помощью автоматизированного сценария для интерфейса командной строки Azure

Вы создали приложение Active Directory и субъект-службу для этого приложения. Вы назначили субъект-службу роли. Теперь вам нужно войти в систему в качестве субъекта-службы и выполнить операции от имени субъекта-службы.

Для проверки подлинности в интерфейсе командной строки Azure укажите отпечаток сертификата, файл сертификата, идентификатор приложения и идентификатор клиента.

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

## Дальнейшие действия
  
- Примеры проверки подлинности для .NET см. в статье [Пакет SDK .NET для Azure Resource Manager](resource-manager-net-sdk.md).
- Примеры проверки подлинности для Java см. в статье [Пакет SDK Java для Azure Resource Manager](resource-manager-java-sdk.md). 
- Примеры проверки подлинности для Python см. в статье [Resource Management Authentication for Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html) (Проверка подлинности для управления ресурсами с использованием Python).
- Примеры проверки подлинности для REST см. в статье [API-интерфейсы REST Resource Manager](resource-manager-rest-api.md).
- Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).
- Дополнительные сведения об использовании сертификатов и интерфейса командной строки Azure см. в статье [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Проверка подлинности в субъекте-службе Azure из командной строки Linux с использованием сертификатов). 

<!---HONumber=AcomDC_0601_2016-->