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
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Интерфейс командной строки Azure](resource-group-authenticate-service-principal-cli.md)
- [Портал](resource-group-create-service-principal-portal.md)


При наличии приложения или скрипта, которому требуется доступ к ресурсам, вы, вероятно, не захотите, чтобы этот процесс осуществлялся под вашими учетными данными. Ваша учетная запись может иметь другие разрешения, необходимые для приложения, и использование ваших учетных данных приложением при изменении обязанностей будет нежелательным. Вместо этого для приложения можно создать удостоверение, содержащее учетные данные проверки подлинности и назначения ролей. При каждом запуске приложения выполняется автоматическая проверка подлинности с этими учетными данными. В этой статье показано, как настроить использование собственных учетных данных и удостоверения для приложения с помощью [интерфейса командной строки Azure (Azure CLI) для Mac, Linux и Windows](xplat-cli-install.md).

В Azure CLI доступно два варианта проверки подлинности для приложения AD:

 - пароль
 - на основе сертификата.

В этой статье показано, как применить оба варианта в Azure CLI. Если вам нужно выполнять вход в Azure с платформы для программирования (например, Python, Ruby или Node.js), мы советуем использовать проверку подлинности на основе пароля. Прежде чем решить, какой тип проверки подлинности использовать (сертификат или пароль), просмотрите примеры проверки подлинности на разных платформах в разделе [Примеры приложений](#sample-applications).

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

- создать приложение AD с паролем и субъектом-службой;
- назначить роль "Читатель" субъекту-службе.

Чтобы быстро выполнить эти шаги, используйте следующие команды:

    azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
    azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

Давайте остановимся на этих шагах подробнее, чтобы разобраться со всей процедурой.

1. Войдите в свою учетную запись.

        azure config mode arm
        azure login

1. Создайте субъект-службу для своего приложения. Укажите отображаемое имя, универсальный код ресурса (URI) на странице, описывающей приложение, коды URI, идентифицирующие приложение, а также пароль для его удостоверения. Эта команда создает приложение AD и субъект-службу.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {your-password}
        
     Для однотенантных приложений коды URI не проверяются.
     
     Если ваша учетная запись не имеет [требуемых разрешений](#required-permissions) для Active Directory, вы увидите сообщение об ошибке с текстом "Authentication\_Unauthorized" (Аутентификация отклонена) или "No subscription found in the context" (В этом контексте подписки не обнаружены).
    
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

2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере показано, как назначить субъекту-службе роль **читателя**, которая дает разрешение на чтение всех ресурсов в подписке. Сведения о других ролях см. в статье [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md). Для параметра **ServicePrincipalName** укажите значение **ObjectId**, которое использовалось при создании приложения.

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Если у вашей учетной записи нет достаточных разрешений для назначения ролей, вы получите сообщение об ошибке. В нем будет указано, что у вашей учетной записи **нет разрешения на выполнение действия Microsoft.Authorization/roleAssignments/write в области /subscriptions/{guid}**.

Вот и все! Приложение AD и субъект-служба настроены. В следующем разделе показано, как выполнить вход с использованием учетных данных с помощью Azure CLI. Если необходимо использовать учетные данные в коде приложения, выполнять действия, описанные ниже, не требуется. Вы можете перейти к разделу [Примеры приложений](#sample-applications), где приведены примеры входа с использованием идентификатора приложения и пароля.

### Предоставление учетных данных через Azure CLI

Теперь следует войти в систему от имени приложения для выполнения операций.

1. При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Active Directory. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, используйте следующую команду:

        azure account show

     Возвращаемые данные:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Если необходимо получить идентификатор клиента другой подписки, используйте следующую команду:

        azure account show -s {subscription-id}

3. Выполните вход как субъект-служба.

        azure login -u https://www.contoso.org/example --service-principal --tenant {tenant-id}

    Появится запрос на ввод пароля. Введите пароль, который вы задали при создании приложения AD.

        info:    Executing command login
        Password: ********

Теперь вы прошли проверку подлинности от имени субъекта-службы для созданного вами приложения.

## Создание субъекта-службы с использованием сертификата

В этом разделе содержатся инструкции, которые помогут вам:

- создать самозаверяющий сертификат;
- создать приложение AD с сертификатом и субъектом-службой;
- назначить роль "Читатель" субъекту-службе.

Для выполнения этих действий в системе должен быть установлен [OpenSSL](http://www.openssl.org/).

1. Создать самозаверяющий сертификат.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Объедините открытый и закрытый ключи.

        cat privkey.pem cert.pem > examplecert.pem

3. Откройте файл **examplecert.pem** и найдите длинную последовательность символов между тегами **-----BEGIN CERTIFICATE-----** и **-----END CERTIFICATE-----**. Скопируйте данные сертификата. Во время создания субъекта-службы эти данные передаются в качестве параметра.

1. Войдите в свою учетную запись.

        azure config mode arm
        azure login

1. Создайте субъект-службу для своего приложения. Укажите отображаемое имя, универсальный код ресурса (URI) на странице, описывающей приложение, коды URI, идентифицирующие приложение, а также скопированные данные сертификата. Эта команда создает приложение AD и субъект-службу.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
     Для однотенантных приложений коды URI не проверяются.
     
     Если ваша учетная запись не имеет [требуемых разрешений](#required-permissions) для Active Directory, вы увидите сообщение об ошибке с текстом "Authentication\_Unauthorized" (Аутентификация отклонена) или "No subscription found in the context" (В этом контексте подписки не обнаружены).
    
     Возвращается новый субъект-служба. При предоставлении разрешений требуется идентификатор объекта.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Предоставьте субъекту-службе разрешения на вашу подписку. В этом примере показано, как назначить субъекту-службе роль **читателя**, которая дает разрешение на чтение всех ресурсов в подписке. Сведения о других ролях см. в статье [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md). Для параметра **ServicePrincipalName** укажите значение **ObjectId**, которое использовалось при создании приложения.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Если у вашей учетной записи нет достаточных разрешений для назначения ролей, вы получите сообщение об ошибке. В нем будет указано, что у вашей учетной записи **нет разрешения на выполнение действия Microsoft.Authorization/roleAssignments/write в области /subscriptions/{guid}**.

### Предоставление сертификата с помощью автоматизированного сценария для интерфейса командной строки Azure

Теперь следует войти в систему от имени приложения для выполнения операций.

1. При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Active Directory. Чтобы получить идентификатор клиента для текущей аутентифицированной подписки, используйте следующую команду:

        azure account show

     Возвращаемые данные:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Если необходимо получить идентификатор клиента другой подписки, используйте следующую команду:

        azure account show -s {subscription-id}

1. Чтобы получить отпечаток сертификата и удалить ненужные символы, используйте такую команду:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Возвращается значение отпечатка следующего вида:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

1. Выполните вход как субъект-служба.

        azure login --service-principal --tenant {tenant-id} -u https://www.contoso.org/example --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- Дополнительные сведения об использовании сертификатов и Azure CLI см. в статье [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Проверка подлинности на основе сертификата для субъектов-служб Azure из командной строки Linux).

<!---HONumber=AcomDC_0914_2016-->