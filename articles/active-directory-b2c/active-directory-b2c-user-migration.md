---
title: "Azure Active Directory B2C. Подходы к миграции пользователей"
description: "В этой статье рассматриваются основные и расширенные концепции миграции пользователей с помощью API Graph и с дополнительным использованием настраиваемых политик Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C. Миграция пользователей
При планировании миграции поставщика удостоверений в Azure AD B2C также может потребоваться перенести учетную запись пользователя. В этой статье описывается процедура переноса существующих учетных записей пользователей из любого поставщика удостоверений в Azure AD B2C. Эта статья не является предписанием, а скорее описывает два из нескольких разных подходов.  За пригодность отвечает разработчик.

## <a name="user-migration-flows"></a>Потоки миграции пользователей
Azure AD B2C позволяет переносить пользователей с помощью [API Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Процесс миграции пользователей подразделяется на два потока.

* **Действия, выполняемые перед миграцией**. Этот поток подходит, когда у вас есть доступ к учетным данным пользователя (имя пользователя и пароль) в текстовом виде. Или учетные данные зашифрованы, но их можно расшифровать. Процесс включает в себя считывание пользователей старого поставщика удостоверений и создание новых учетных записей в каталоге Azure AD B2C.

* **Действия, выполняемые перед миграцией, и сброс пароля**. Этот поток подходит, когда пароль недоступен. Например:
    * Пароли сохраняются в формате хэша.
    * Пароли хранятся в поставщике удостоверений, к которому у вас нет доступа. Старый поставщик удостоверений проверяет учетные данные пользователя путем вызова веб-службы.

В обоих случаях сначала требуется запустить процесс, __выполняемый перед миграцией__, считать пользователей из старого поставщика удостоверений, а затем создать новые учетные записи в каталоге Azure AD B2C. Если у вас нет пароля, следует создать учетную запись со случайным сгенерированным вами паролем. Предложите пользователям изменить их пароли. Или, когда пользователь впервые регистрируется, B2C просит пользователя сбросить пароль.

## <a name="password-policy"></a>Политика паролей
Политика паролей Azure AD B2C (для локальных учетных записей) основана на политике Azure AD. В политиках регистрации, входа и сброса пароля Azure AD B2C предусмотрено использование надежного пароля, срок действия которого не истекает. Дополнительные сведения см. в статье [Политика паролей в Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

Если у переносимых учетных записей надежность пароля ниже, чем [высокая надежность пароля, обеспечиваемая Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), можно отключить требование надежного пароля. Чтобы изменить политику паролей по умолчанию, задайте для свойства `passwordPolicies` значение `DisableStrongPassword`. Например, можно изменить запрос создания пользователя следующим образом: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>Шаг 1. Миграция пользователей с помощью API Graph
Создается учетная запись пользователя Azure AD B2C с помощью API Graph (с паролем или со случайным паролем). В этом разделе описывается процесс создания учетных записей пользователей в каталоге Azure AD B2C с помощью API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Шаг 1.1. Регистрация приложения в клиенте
Для взаимодействия с API Graph сначала необходимо иметь учетную запись службы с правами администратора. В Azure AD зарегистрируйте приложение и выполните проверку подлинности в Azure AD. Учетные данные приложения такие: **идентификатор приложения** и **секрет приложения**. Приложение вызывает API Graph, действуя само по себе, а не под именем конкретного пользователя.

Сначала зарегистрируйте переносимое приложение в Azure AD. Затем создайте ключ приложения (секрет приложения) и настройте для приложения необходимые права.

1. Выполните вход на [портал Azure](https://portal.azure.com/).
2. Выберите клиент Azure AD **B2C**, щелкнув имя своей учетной записи в правом верхнем углу страницы.
3. На левой панели щелкните **Azure Active Directory** (не Azure AD B2C). Чтобы найти ее, возможно, потребуется выбрать пункт **Больше служб**.
4. Щелкните **Регистрация приложений**.
5. Щелкните **Регистрация нового приложения**.

    ![Регистрация нового приложения](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. Следуйте инструкциям на экране, а затем создайте новое приложение.
    * В качестве **имени** используйте **B2CUserMigratioin** или любое другое.
    * Для параметра **Тип приложения** используйте значение **Веб-приложение/API**.
    * В качестве **URL-адреса входа** используйте https://localhost (так как это не относится к этому приложению).
    * Щелкните **Создать**.
7. По завершении создания в списке приложений выберите только что созданное приложение **B2CUserMigratioin**.
Выберите **Свойства**, скопируйте **идентификатор приложения** и сохраните его для использования в будущем.

### <a name="step-12-create-application-secret"></a>Этап 1.2. Создание секрета приложения
8. На портале Azure перейдите в раздел "Зарегистрированное приложение". Щелкните **Ключи** и добавьте новый ключ (секрет клиента). Кроме того, скопируйте ключ для использования в дальнейшем.

    ![Идентификатор приложения и ключи](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Шаг 1.3. Предоставление административных разрешений для приложения
9. На портале Azure перейдите в раздел **Зарегистрированное приложение**.
10. Щелкните **Необходимые разрешения**.
11. Щелкните **Windows Azure Active Directory**.
12. В меню **Разрешить доступ** в списке **Разрешения приложений** выберите разрешение **Чтение и запись данных каталога** и нажмите кнопку **Сохранить**.
13. Наконец, в разделе **Необходимые разрешения** нажмите кнопку **Предоставить разрешения**.

    ![Разрешения приложения](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Теперь у вас есть приложение с разрешением на создание, чтение и обновление пользователей из клиента B2C.

### <a name="step-14-optional-environment-cleanup"></a>Шаг 1.4. Очистка среды (необязательно)
Чтение и запись данных каталога не предусматривают возможность выполнять удаление пользователей. Если вы хотите обеспечить приложению возможность удаления пользователей (для очистки среды), необходимо выполнить дополнительный шаг. Этот шаг заключается в запуске PowerShell для установки разрешений __администратора учетной записи пользователя__. В противном случае можно перейти к следующему разделу.


> [!IMPORTANT]
> Необходимо использовать учетную запись администратора клиента B2C, которая является **локальной** по отношению к клиенту B2C. Эти учетные записи выглядят следующим образом: admin@contosob2c.onmicrosoft.com.

>[!NOTE]
> Для следующего скрипта PowerShell требуется [Azure Active Directory PowerShell **версии 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

В следующем скрипте PowerShell:
* Подключитесь к веб-службе. Для этого выполните командлет `Connect-AzureAD` в командной строке Windows PowerShell и предоставьте ваши учетные данные. 
* Используйте **идентификатор приложения** для назначения приложению роли администратора учетной записи пользователя. Идентификаторы этих ролей известны, поэтому можно просто ввести **идентификатор приложения** в скрипт.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Измените значение `$AppId` на ваш **идентификатор приложения** Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Шаг 2. Пример приложения перед началом миграции
Скачайте и запустите пример кода. Вы можете [скачать пример кода в виде ZIP-файла](http://www.github.com).

### <a name="step-21-edit-the-migration-data-file"></a>Шаг 2.1. Изменение файла данных миграции
Пример приложения использует JSON-файл, содержащий данные фиктивных пользователей. После успешного запуска примера измените код для получения данных из собственной базы данных. Можно также экспортировать профиль пользователей в JSON-файл и настроить приложение для использования этого файла.
Чтобы изменить JSON-файл, откройте решение Visual Studio `AADB2C.UserMigration.sln`. В проекте `AADB2C.UserMigration` откройте файл `UsersData.json`.


![Файл данных о пользователях](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Как видите, файл содержит список сущностей пользователей. Каждая сущность пользователя имеет:
* email
* displayName
* firstName
* lastName
* пароль (может быть пустым).

> [!NOTE]
> Во время компиляции Visual Studio копирует файл в каталог `bin`.

### <a name="step-22-configure-the-application-settings"></a>Шаг 2.2. Настройка параметров приложения
В проекте `AADB2C.UserMigration` откройте файл App.config. Замените следующие параметры приложения собственными значениями.

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * Использование строки подключения таблицы Azure описано в следующих разделах.
> * В качестве имени вашего клиента B2C используется домен, указанный при создании клиента. Это имя отображается на портале Azure. Обычно оно заканчивается суффиксом `.onmicrosoft.com`, например `contosob2c.onmicrosoft.com`.
>

### <a name="step-23-run-the-pre-migration-process"></a>Шаг 2.3. Запуск процесса подготовки к миграции
Щелкните решение `AADB2C.UserMigration` правой кнопкой мыши и еще раз создайте пример кода. Если вам удастся выполнить это действие, в каталоге `AADB2C.UserMigration\bin\Debug` появится исполняемый файл `UserMigration.exe`. Чтобы запустить процесс миграции, используйте один из следующих параметров командной строки:

* Чтобы **перенести пользователей с паролями**, используйте команду `UserMigration.exe 1`.

* Чтобы **перенести пользователей со случайными паролями**, используйте команду `UserMigration.exe 2`. Эта операция также создает сущность таблицы Azure. Далее настройте политику для вызова службы REST API. Служба использует таблицу Azure для отслеживания процесса миграции и управления им.

![Демонстрация процесса миграции](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Шаг 2.4. Проверка процесса подготовки к миграции
Для проверки результата на портале Azure откройте **Azure AD B2C** и щелкните **Пользователи и группы**. В поле поиска введите отображаемое имя пользователя и просмотрите профиль пользователя. Кроме того, можно использовать этот пример приложения для получения данных о пользователе по **адресу электронной почты для входа**. Чтобы найти пользователя по адресу электронной почты для входа, выполните следующую команду.

```Console
UserMigration.exe 3 {email address}
```

Можно также сохранить выходные данные в JSON-файл следующим образом:
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


Откройте файл UserProfile.json в любом редакторе JSON. В Visual Studio Code можно форматировать документ JSON с помощью `Shift+Alt+F` или пункта "Форматировать документ" в контекстном меню.

![JSON-файл профиля пользователя](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

Вы также можете получить данные пользователя по **отображаемому имени** с помощью команды `UserMigration.exe 4 <Display name>`.

### <a name="step-25-optional-environment-cleanup"></a>Шаг 2.5. Очистка среды (необязательно)
Если вы хотите очистить клиент Azure AD и удалить пользователей из каталога Azure AD, выполните команду `UserMigration.exe 5`.

> [!NOTE]
> * Чтобы очистить клиент, настройте разрешения __администратора учетной записи пользователя__ для приложения.
> * Пример приложения для миграции удаляет всех пользователей, указанных в файле JSON.

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>Шаг 2.6. Выполнение входа для перенесенных пользователей (с паролем)
После выполнения процесса подготовки к миграции с паролями пользователей учетные записи готовы к использованию и пользователи могут выполнять вход в приложение с помощью Azure AD B2C. При отсутствии доступа к паролям пользователей перейдите к следующему разделу.

## <a name="step-3-password-reset"></a>Шаг 3. Сброс паролей
Если вы переносите пользователей со случайными паролями, им необходимо сбросить пароль. Чтобы сбросить пароль:
* Отправьте приветственное сообщение электронной почты со ссылкой для сброса пароля.
* [Необязательно.] Измените политику на случай, если пользователь не сбросит пароль и попытается выполнить вход. При выполнении входа политика проверяет состояние переноса. Если пользователь не изменил пароль, добавьте дружественное сообщение об ошибке, попросив пользователя щелкнуть "Забыли пароль?".

    > [!NOTE]
    > Чтобы проверить и изменить состояние миграции пользователей, необходимо использовать настраиваемую политику. Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>Шаг 3.1. Отправка приветственного сообщения электронной почты со ссылкой для сброса пароля
Чтобы получить ссылку на политику сброса пароля:

1.  Откройте **Параметры Azure AD B2C** и перейдите к свойствам политики **сброса пароля**.

2. Выберите приложение.
    >[!NOTE]
    >
    >Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).  

2.  Выберите **Запустить сейчас** и проверьте политику.
3.  **Скопируйте** URL-адрес и отправьте его пользователям.

    ![Настройка журналов диагностики](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>Шаг 4. Изменение политики для проверки и установки состояния миграции пользователя (необязательно)

При попытке пользователя войти без сброса пароля в первый раз политика должна возвращать дружественное сообщение об ошибке. Например: "Срок действия пароля истек. Чтобы сбросить пароль, щелкните ссылку сброса пароля".  Этот необязательный шаг требует использования Azure AD B2C с настраиваемыми политиками, как описано в статье [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

В этом разделе измените политику для проверки состояния миграции при выполнении входа. Если пользователь не изменил пароль, отправьте сообщение об ошибке HTTP 409, попросив пользователя щелкнуть ссылку "Забыли пароль?" . Чтобы отслеживать изменение пароля, используйте таблицу Azure. При запуске процесса подготовки к миграции с параметром командной строки `2` в таблице Azure создается пользовательский объект. Ваша служба:

* Когда выполняется вход, политика Azure AD B2C вызывает службу миграции Restful, отправляя сообщение электронной почты в виде входного утверждения. Служба ищет адрес электронной почты в таблице Azure. Если адрес существует, появляется сообщение об ошибке "Требуется изменить пароль".

* Когда пользователь успешно изменит пароль, удалите сущность из таблицы Azure.


> [!NOTE]
>Мы используем таблицу Azure для упрощения примера. Состояние миграции можно хранить в любой базе данных или как пользовательское свойство в учетной записи Azure AD B2C.

### <a name="41-application-settings"></a>4.1. Параметры приложения
Чтобы протестировать демонстрацию Restful API, выполните следующие действия. Откройте решение Visual Studio `AADB2C.UserMigration.sln` в Visual Studio. В проекте `AADB2C.UserMigration.API` откройте файл App.config. Замените три параметра приложения собственными значениями.

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>Шаг 4.2. Развертывание веб-приложения в службе приложений Azure
Опубликуйте приложение API в службе приложений Azure. Дополнительные сведения см. в статье [Развертывание приложения в службе приложений Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy).

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>Шаг 4.3. Добавление технического профиля и его проверки в политику 
1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога. 
2. Найдите раздел `<ClaimsProviders>`.
3. Добавьте следующий фрагмент XML-кода в элемент `ClaimsProviders`.
4. Измените значение `ServiceUrl` для указания URL-адреса конечной точки. 

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>

    <TechnicalProfile Id="LocalAccountSignIn">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountPasswordReset">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

Технический профиль определяет одно входящее утверждение: `signInName` (отправить по электронной почте). Когда выполняется вход, утверждение отправляется в конечную точку Restful.

После определения технического профиля для Restful API укажите политике Azure AD B2C вызвать этот технический профиль. Фрагмент XML-кода переопределяет параметр `SelfAsserted-LocalAccountSignin-Email`, который определен в базовой политике. Фрагмент XML-кода также добавляет `ValidationTechnicalProfile` с ReferenceId, указывающим на технический профиль `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Шаг 4.4. Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и щелкните **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Щелкните **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.
7.  Повторите последний шаг и отправьте файл SignUpOrSignIn.xml.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Шаг 4.5. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3. Попытайтесь войти от имени одного из перенесенных пользователей, щелкнув **Вход**. Должно появиться следующее сообщение об ошибке, поступающее от Rest API.

    ![Настройка журналов диагностики](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>Шаг 4.6. Устранение неполадок REST API (необязательно)
Можно отслеживать и просматривать данные ведения журналов практически в режиме реального времени.

1. В меню параметров приложения Restful прокрутите вниз до раздела **Мониторинг** и щелкните **Журналы диагностики**. 
2. Включите ведение журнала приложения (файловая система). 
3. Установите для параметра **Уровень** значение **Подробный**.
4. Нажмите кнопку **Сохранить**

    ![Настройка журналов диагностики](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. В меню параметров щелкните **Поток журналов**.
6. Проверьте выходные данные Restful API.

Дополнительные сведения см. в статье [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console).

> [!IMPORTANT]
> __Журналы диагностики__ следует использовать только во время разработки и тестирования. Выходные данные RESTful API могут содержать конфиденциальные сведения, которые не должны быть раскрыты в рабочей среде.
>

## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики
Необязательно. Мы советуем создать свой сценарий, используя собственные файлы пользовательской политики, а не эти примеры файлов, после того, как вы ознакомитесь с пошаговым руководством по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)