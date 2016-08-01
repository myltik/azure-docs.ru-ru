<properties
   pageTitle="Создание субъекта-службы с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Использование командной строки Azure для создания приложения Active Directory и субъекта-службы с последующим предоставлением доступа к ресурсам с управлением доступом на основе ролей. В статье показано, как выполнять проверку подлинности приложения с помощью пароля или сертификата."
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
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)

При наличии приложения или сценария, которому требуется доступ к ресурсам, вы, вероятно, не захотите, чтобы этот процесс осуществлялся под вашими учетными данными. Учетная запись может иметь другие разрешения, которые можно назначить процессу, что, в свою очередь, может повлечь за собой изменение обязанностей, предусмотренных заданием. Вместо этого для приложения можно создать удостоверение, содержащее учетные данные проверки подлинности и назначения ролей. Эта учетная запись будет использоваться при каждом запуске приложения. В этой статье показано, как настроить использование собственных учетных данных и удостоверения для приложения с помощью [интерфейса командной строки Azure (Azure CLI) для Mac, Linux и Windows](xplat-cli-install.md).

Кроме этого, в этой статье вы создадите два объекта — приложение Active Directory (AD) и субъект-службу. Приложение AD содержит учетные данные (идентификатор приложения и пароль или сертификат), а субъект-служба — назначение роли. Приложение AD позволяет создать множество субъектов-служб. В этой статье описывается однотенантное приложение, используемое в пределах одной организации. Обычно однотенантная архитектура используется для создания бизнес-приложений в рамках организации. Если приложение будет использоваться несколькими организациями, необходимо применить мультитенантную архитектуру. Обычно такая архитектура используется для создания приложений "программное обеспечение как услуга" (SaaS). Сведения о настройке мультитенантного приложения см. в [руководстве разработчика по авторизации с помощью API Azure Resource Manager](resource-manager-api-authentication.md).

При работе с Active Directory необходимо разобраться с многими понятиями. Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](./active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](./active-directory/active-directory-authentication-scenarios.md).

В Azure CLI доступно два варианта проверки подлинности для приложения AD:

 - пароль
 - на основе сертификата.

Если после настройки приложения AD понадобится выполнять вход в Azure с другой платформы программирования (например, Python, Ruby или Node.js), рекомендуется использовать проверку подлинности на основе пароля. Прежде чем решить, какой тип проверки подлинности использовать (сертификат или пароль), просмотрите примеры проверки подлинности на разных платформах в разделе [Примеры приложений](#sample-applications).

## Получение идентификатора клиента

При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Active Directory. Идентификатор клиента понадобится нам при проверке подлинности, поэтому мы получим это значение сейчас.

1. Войдите в свою учетную запись.

        azure config mode arm
        azure login

1. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, не нужно указывать идентификатор подписки в качестве параметра. Параметр **-r** позволяет получить значение без кавычек.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

Теперь перейдите к разделу ниже, чтобы использовать проверку подлинности на основе [пароля](#create-service-principal-with-password) или [сертификата](#create-service-principal-with-certificate).


## Создание субъекта-службы с использованием пароля

В этом разделе описывается создание субъекта-службы с использованием пароля и назначение ему роли.

1. Создайте субъект-службу для своего приложения. Укажите отображаемое имя для вашего приложения, URI страницы, описывающей его (ссылка не проверяется), коды URI, идентифицирующие приложение, и пароль для его удостоверения. Эта команда создает приложение AD и субъект-службу.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта. Имя субъекта-службы требуется для входа в систему.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md). Чтобы назначить роль, требуется доступ `Microsoft.Authorization/*/Write`, который предоставляется с помощью роли [Владелец](./active-directory/role-based-access-built-in-roles.md#owner) или [Администратор доступа пользователей](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как с помощью Azure CLI выполнить вход, используя учетные данные. Если вы хотите использовать учетные данные в коде приложения, выполнять действия, описанные ниже, не требуется. Вы можете перейти к разделу [Примеры приложений](#sample-applications), где приведены примеры входа с использованием идентификатора приложения и пароля.

### Предоставление учетных данных через Azure CLI

1. В качестве имени пользователя необходимо использовать имя субъекта-службы, возвращенное при создании этого субъекта-службы. Чтобы получить идентификатор приложения, используйте указанную ниже команду. Укажите имя приложения Active Directory в параметре **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Выполните вход как субъект-служба.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    Появится запрос на ввод пароля. Введите пароль, который вы задали при создании приложения AD.

        info:    Executing command login
        Password: ********

Теперь вы прошли проверку подлинности от имени субъекта-службы для созданного вами приложения.

## Создание субъекта-службы с использованием сертификата

В этом разделе описывается создание субъекта-службы, использующего сертификат для проверки подлинности. Для выполнения этих действий в системе должен быть установлен [OpenSSL](http://www.openssl.org/).

1. Создать самозаверяющий сертификат.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Объедините открытый и закрытый ключи.

        cat privkey.pem cert.pem > examplecert.pem

3. Откройте файл **examplecert.pem** и скопируйте данные сертификата. Найдите длинную последовательность знаков между тегами **-----BEGIN CERTIFICATE-----** и **-----END CERTIFICATE-----**.

1. Создайте субъект-службу для своего приложения. Укажите идентификатор приложения, который был возвращен в предыдущем шаге.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере вы предоставите субъекту-службе разрешение на чтение всех ресурсов в подписке. Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md). Чтобы назначить роль, требуется доступ `Microsoft.Authorization/*/Write`, который предоставляется с помощью роли [Владелец](./active-directory/role-based-access-built-in-roles.md#owner) или [Администратор доступа пользователей](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Предоставление сертификата с помощью автоматизированного сценария для интерфейса командной строки Azure

Для проверки подлинности в интерфейсе командной строки Azure укажите отпечаток сертификата, файл сертификата, идентификатор приложения и идентификатор клиента.

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

После этого субъект-служба для созданного вами приложения Active Directory пройдет проверку подлинности.

Чтобы получить отпечаток сертификата и удалить ненужные знаки, используйте следующую команду:

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
Возвращается значение отпечатка следующего вида:

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

Чтобы получить идентификатор приложения, используйте следующую команду:

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

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
- Дополнительные сведения об использовании сертификатов и интерфейса командной строки Azure см. в статье [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Проверка подлинности для субъектов-служб Azure на основе сертификатов из командной строки Linux).

<!---HONumber=AcomDC_0720_2016-->