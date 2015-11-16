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
   ms.date="10/29/2015"
   ms.author="tomfitz"/>

# Создание приложения Active Directory и субъекта-службы с помощью портала

## Обзор
При наличии приложения, требующего доступ к ресурсу или изменения ресурса в подписке, можно воспользоваться порталом для создания приложения Active Directory и назначить его роли, имеющей необходимые разрешения. При создании приложения Active Directory на портале происходит фактическое создание приложения и субъекта-службы. Субъект-службы используется при установке разрешений.

В этом разделе рассматривается создание нового приложения и субъекта-службы с помощью портала Azure. В настоящее время для его создания приложения Active Directory необходимо использовать портал Microsoft Azure. Эта возможность будет добавлена на портал предварительной версии Azure в одном из следующих выпусков. Предварительную версию портала можно использовать для назначения приложения роли.

## Основные понятия
1. Azure Active Directory (AAD) — это служба управления удостоверениями и доступом, созданная для облачной среды. Дополнительные сведения см. в статье [Служба Azure Active Directory](active-directory/active-directory-whatis.md)
2. Субъект-служба — экземпляр приложения в каталоге.
3. Приложение AD — запись в каталоге AAD, которая связывает некоторое приложение со службой AAD. 

Более подробное описание приложений и субъектов-служб см. в разделе [Объекты приложений и объекты субъектов-служб](active-directory/active-directory-application-objects.md). Дополнительные сведения о проверке подлинности Active Directory см. в статье [Сценарии проверки подлинности в Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Создание объектов приложений и объектов субъектов-служб

1. Войдите в свою учетную запись Azure на [портале](https://manage.windowsazure.com/).

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

6. Введите название приложения и выберите его тип. Так как мы собираемся использовать субъект-службу этого приложения для проверки подлинности в диспетчере ресурсов Azure, мы выберем вариант **ВЕБ-ПРИЛОЖЕНИЕ И/ИЛИ ВЕБ-API** и нажмем кнопку «Далее».

     ![указание имени приложения][9]

7. Задайте свойства приложения. В поле **URL-АДРЕС ВХОДА** введите URI веб-сайта, который описывает ваше приложение. Существование этого сайта не проверяется. В поле **URI ИДЕНТИФИКАТОРА ПРИЛОЖЕНИЯ** введите URI, который идентифицирует ваше приложение. Уникальной и существование конечной точки не проверяются. Щелкните **Завершить**, чтобы создать приложение AAD.

     ![свойства приложения][4]

## Создание ключа проверки подлинности для приложения
Ваше приложение будет выбрано на портале.

1. Откройте вкладку **Настройка**, чтобы задать для него пароль.

     ![настройка приложения][3]

2. Прокрутите содержимое до раздела **Ключи** и выберите срок действия пароля.

     ![ключи][7]

3. Нажмите **Сохранить**, чтобы сохранить созданный ключ.

     ![сохранение][13]

     Сохранный ключ появится на экране, и вы сможете его скопировать.

     ![сохраненный ключ][8]

4. Теперь этот ключ можно использовать для проверки подлинности субъекта-службы. Для входа вам кроме **ключа** вам понадобится **ИДЕНТИФИКАТОР КЛИЕНТА**. Найдите поле **ИДЕНТИФИКАТОР КЛИЕНТА** и скопируйте его содержимое.
  
     ![идентификатор клиента][5]


Теперь приложение готово, и на вашем клиенте создан субъект-служба. При входе в качестве субъекта-службы используйте следующие параметры:

* **ИДЕНТИФИКАТОР КЛИЕНТА** —в качестве имени пользователя;
* **КЛЮЧ** — в качестве пароля.

## Назначение приложения роли

Можно использовать [предварительной версии портала](https://portal.azure.com) для назначения приложения Active Directory роли, имеющей доступ к требуемому ресурсу. Сведения о назначении приложения роли см. в статье [Контроль доступа на основе ролей Azure Active Directory](active-directory/role-based-access-control-configure.md).

## Получение маркера доступа в коде

При использовании .NET можно получить маркер доступа для приложения с помощью следующего кода.

Во-первых, необходимо установить библиотеку проверки подлинности Active Directory в проект Visual Studio. Для этого проще всего использовать пакет NuGet. Откройте консоль диспетчера пакетов и введите следующие команды.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

В приложении добавьте метод, аналогичный следующему, для получения маркера.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

## Дальнейшие действия

- Дополнительные сведения о настройке политик безопасности см. в разделе [Управление доступом к ресурсам и его аудит](resource-group-rbac.md).  
- Видеодемонстрация этих шагов представлена в статье [Включение программного управления ресурсов Azure с помощью Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Дополнительные сведения об использовании Azure PowerShell или Azure CLI для работы с приложениями и субъектами-служб Active Directory, включая использование сертификата для проверки подлинности, см. в статье [Проверка подлинности субъекта-службы с помощью диспетчера ресурсов Azure](./resource-group-authenticate-service-principal.md).
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

<!---HONumber=Nov15_HO2-->