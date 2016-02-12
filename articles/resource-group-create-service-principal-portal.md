<properties
   pageTitle="Создание приложения AD и субъекта-службы на портале | Microsoft Azure"
   description="Описывается создание нового приложения Active Directory и субъекта-службы, которые можно использовать в сочетании с контролем доступа на основе ролей в диспетчере ресурсов Azure для управления доступом к ресурсам."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/27/2016"
   ms.author="tomfitz"/>

# Создание приложения Active Directory и субъекта-службы с помощью портала

## Обзор
При наличии автоматизированного процесса или приложения, требующего доступа к ресурсам или их изменения, вы можете воспользоваться классическим порталом для создания приложения Active Directory. При создании приложения Active Directory на портале происходит фактическое создание приложения и субъекта-службы. Приложение можно выполнить с помощью его собственного удостоверения или удостоверения пользователя, вошедшего в приложение. Эти два метода проверки подлинности приложений называются интерактивным (пользователь входит в систему) и неинтерактивным (приложение предоставляет свои собственные учетные данные). В неинтерактивном режиме субъекту-службе нужно назначить роль с правильным разрешением.

В этом разделе рассматривается создание нового приложения и субъекта-службы с помощью классического портала. В настоящее время для создания приложения Active Directory необходимо использовать классический портал. Эта возможность будет добавлена на портал Azure в одном из следующих выпусков. Вы можете использовать портал для назначения приложения роли. Это также можно выполнить с помощью Azure PowerShell или Azure CLI. Дополнительные сведения об использовании PowerShell или CLI с субъектом-службой см. в статье [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md).

## Основные понятия
1. Azure Active Directory (AAD) — это служба управления удостоверениями и доступом, созданная для облачной среды. Дополнительные сведения см. в статье [Служба Azure Active Directory](active-directory/active-directory-whatis.md)
2. Субъект-служба — экземпляр приложения в каталоге.
3. Приложение AD — запись в каталоге AAD, которая связывает некоторое приложение со службой AAD. 

Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](active-directory/active-directory-application-objects.md). Дополнительные сведения об аутентификации Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Создание приложения

Для интерактивных и неинтерактивных приложений необходимо создать и настроить приложение Active Directory.

1. Войдите в свою учетную запись Azure через [классическую версию портала](https://manage.windowsazure.com/).

2. Выберите **Active Directory** на левой панели.

     ![выбор Active Directory][1]

3. Выберите каталог, который хотите использовать для создания нового приложения.

     ![выбор каталога][2]

3. Чтобы просмотреть приложения в каталоге, щелкните **Приложения**.

     ![просмотр приложений][11]

4. Если ранее вы не создавали приложений в этом каталоге, вы увидите примерно следующее: Щелкните **ДОБАВИТЬ ПРИЛОЖЕНИЕ**

     ![добавление приложения][6]

     Также можно щелкнуть **Добавить** на нижней панели.

     ![добавление][12]

5. Выберите тип приложения, которое вы хотите создать. В этом руководстве мы не будем использовать приложения из коллекции.

     ![новое приложение][10]

6. Введите название приложения и выберите его тип. Выберите тип приложения, которое вы хотите создать. В этом руководстве предполагается, что вы выберете вариант **Веб-приложение и/или веб-API**. Нажмите кнопку "Далее".

     ![указание имени приложения][9]

7. Задайте свойства приложения. В поле **URL-АДРЕС ВХОДА** введите URI веб-сайта, который описывает ваше приложение. Существование этого сайта не проверяется. В поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ** введите URI, который идентифицирует ваше приложение. Уникальной и существование конечной точки не проверяются. Если вы выбрали тип **Собственное клиентское приложение**, потребуется указать значение **URI перенаправления**. Щелкните **Завершить**, чтобы создать приложение AAD.

     ![свойства приложения][4]

Приложение создано.

## Получение идентификаторов клиента

При программном доступе к приложению вам потребуется идентификатор для приложения. Выберите вкладку **Настройка** и скопируйте **Идентификатор клиента**.
  
   ![идентификатор клиента][5]

В некоторых случаях в запросе аутентификации необходимо передать идентификатор клиента. Чтобы получить такой идентификатор, внизу экрана нужно выбрать **Просмотр конечных точек** и найти идентификатор, как показано ниже.

   ![Идентификатор клиента](./media/resource-group-create-service-principal-portal/save-tenant.png)

## Создание ключа проверки подлинности

Если приложение будет выполняться с помощью собственных учетных данных, для него необходимо создать ключ.

1. Откройте вкладку **Настройка**, чтобы задать для него пароль.

     ![настройка приложения][3]

2. Прокрутите содержимое до раздела **Ключи** и выберите срок действия пароля.

     ![ключи][7]

3. Нажмите **Сохранить**, чтобы сохранить созданный ключ.

     ![сохранение][13]

     Сохранный ключ появится на экране, и вы сможете его скопировать. Позже получить ключ будет невозможно, поэтому стоит скопировать его сейчас.

     ![сохраненный ключ][8]

Теперь приложение готово, и на вашем клиенте создан субъект-служба. При входе в качестве субъекта-службы используйте следующие параметры:

* **ИДЕНТИФИКАТОР КЛИЕНТА** —в качестве имени пользователя;
* **КЛЮЧ** — в качестве пароля.

## Настройка делегированных разрешений

Если приложение получает доступ к ресурсам от имени выполнившего вход пользователя, необходимо предоставить приложению делегированное разрешение для доступа к другим приложениям. Это можно сделать на вкладке **Настройка** в разделе **Разрешения для других приложений**. По умолчанию делегированное разрешение для Azure Active Directory уже включено. Оставьте это делегированное разрешение без изменений.

1. Выберите **Добавить приложение**.

2. В списке выберите пункт **Интерфейс API управления службами Azure**.

      ![выбор приложения](./media/resource-group-create-service-principal-portal/select-app.png)

3. Добавьте делегированное разрешение **Доступ к управлению службами Azure (предварительная версия)** к API управления службами.

       ![выбор разрешения](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Сохраните изменения.

## Настройка мультитенантного приложения

Если пользователи из других каталогов Active Directory могут давать согласие на использование приложения и входить в него, необходимо включить мультитенантность. На вкладке **Настройка** для параметра **Приложение является мультитенантным** задайте значение **Да**.

![мультитенантность](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Назначение роли приложению

Если приложение не работает с удостоверением пользователя, выполнившего вход, необходимо назначить приложению роль, чтобы предоставить ему разрешения на выполнение действий. Чтобы назначить приложение роли, переключитесь с классического портала на [портал Azure](https://portal.azure.com). Необходимо решить, в какую роль добавить приложение и с какой областью действия. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](./active-directory/role-based-access-built-in-roles.md). Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуются на более низких уровнях области действия (например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы).

1. На портале перейдите на уровень области, которому вы хотите назначить приложение. В рамках данной темы вы можете перейти в группу ресурсов, а затем в колонке группы ресурсов выбрать значок **Доступ**.

     ![Выбор пользователей](./media/resource-group-create-service-principal-portal/select-users.png)

2. Выберите **Добавить**.

     ![выбрать "добавить"](./media/resource-group-create-service-principal-portal/select-add.png)

3. Выберите роль **Читатель** (или любую другую роль, которой вы хотите назначить приложение).

     ![выбрать роль](./media/resource-group-create-service-principal-portal/select-role.png)

4. Когда вы увидите список пользователей, которых можно добавить к роли, приложения будут отсутствовать. Вы увидите только группы и пользователей.

     ![показать пользователей](./media/resource-group-create-service-principal-portal/show-users.png)

5. Чтобы найти приложение, необходимо выполнить его поиск. Начните вводить имя приложения, и список доступных параметров изменится. Выберите приложение, когда увидите его в списке.

     ![назначить роли](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Нажмите кнопку **ОК** для завершения назначения роли. Теперь вы должны видеть свое приложение в списке пользователей, назначенных роли для группы ресурсов.

     ![показать](./media/resource-group-create-service-principal-portal/show-app.png)

Подробнее о назначении пользователям и приложениям ролей с помощью портала см. в разделе [Управление доступом с помощью портала управления Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Получение маркера доступа в коде

При использовании .NET можно получить маркер доступа для приложения с помощью следующего кода.

Во-первых, необходимо установить библиотеку проверки подлинности Active Directory в проект Visual Studio. Для этого проще всего использовать пакет NuGet. Откройте консоль диспетчера пакетов и введите следующие команды.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Чтобы войти с использованием идентификатора и секрета клиента, используйте следующий метод для получения токена.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Чтобы войти от имени пользователя, используйте следующий метод для получения токена.

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Токен можно передать в заголовке запроса с помощью следующего кода:

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## Дальнейшие действия

- Дополнительные сведения о настройке политик безопасности см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).  
- Видеодемонстрация этих шагов представлена в статье [Включение программного управления ресурсов Azure с помощью Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Подробнее об использовании Azure PowerShell или Azure CLI для работы с приложениями и субъектами-службами Active Directory, включая использование сертификата для проверки подлинности, см. в статье [Проверка подлинности субъекта-службы с помощью диспетчера ресурсов Azure](./resource-group-authenticate-service-principal.md).
- Рекомендации по обеспечению безопасной работы с диспетчером ресурсов Azure см. в статье [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0204_2016-->