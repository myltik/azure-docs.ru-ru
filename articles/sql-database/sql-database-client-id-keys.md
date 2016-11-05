---
title: Регистрация приложения и получение идентификатора клиента и ключа для подключения к базе данных SQL из кода | Microsoft Docs
description: Получение идентификатора клиента и ключа для доступа к базе данных SQL из кода.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/06/2016
ms.author: sstein

---
# Получение идентификатора клиента и ключа для доступа к базе данных SQL из кода
Для создания базы данных SQL и управления ею из кода необходимо зарегистрировать приложение в домене Azure Active Directory (AAD), связанном с подпиской, в которой были созданы ресурсы Azure. При регистрации приложения Azure создаст идентификатор клиента и ключ, которые потребуется указать в коде для проверки подлинности приложения. Дополнительные сведения см. в статье [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

## Регистрация собственного клиентского приложения и получение идентификатора клиента
Чтобы создать приложение и зарегистрировать его, выполните приведенные ниже действия.

1. Войдите на [классический портал](https://manage.windowsazure.com/) (в настоящее время регистрация приложений выполняется на классическом портале).
2. В меню найдите и выберите службу **Active Directory**.
   
    ![AAD][1]
3. Выберите каталог для аутентификации приложения и щелкните его **имя**.
   
    ![Каталоги][4]
4. На странице каталога выберите **ПРИЛОЖЕНИЯ**.
   
    ![Приложения][5]
5. Нажмите кнопку **ДОБАВИТЬ**, чтобы добавить новое приложение.
   
    ![Добавление приложения][6]
6. Укажите **ИМЯ** приложения и выберите **СОБСТВЕННОЕ КЛИЕНТСКОЕ ПРИЛОЖЕНИЕ**.
   
    ![Добавление приложения][7]
7. Укажите **URI ПЕРЕНАПРАВЛЕНИЯ**. Он не обязательно должен быть фактической конечной точкой, а просто допустимым URI.
   
    ![Добавление приложения][8]
8. Завершите создание приложения, нажмите кнопку **НАСТРОИТЬ** и скопируйте **ИДЕНТИФИКАТОР КЛИЕНТА** (это значение потребуется указать в коде).
   
    ![get client id][9]
9. Прокрутите вниз и щелкните **Добавить приложение**.
10. Выберите **Приложения Майкрософт**.
11. Выберите **API управления службами Microsoft Azure**, а затем завершите работу мастера.
12. В разделе **Разрешения для других приложений** найдите раздел **API управления службами Microsoft Azure** и щелкните **Делегированные разрешения**.
13. Выберите **Доступ к управлению службами Azure**.
    
     ![permissions][2]
14. В нижней части страницы нажмите кнопку **СОХРАНИТЬ**.

## Регистрация веб-приложения (или веб-api) и получение идентификатора клиента и ключа
Для создания нового приложения и его регистрации в правильном Active Directory сделайте следующие.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com/).
2. В меню найдите и выберите службу **Active Directory**.
   
    ![AAD][1]
3. Выберите каталог для аутентификации приложения и щелкните его **имя**.
   
    ![Каталоги][4]
4. На странице каталога выберите **ПРИЛОЖЕНИЯ**.
   
    ![Приложения][5]
5. Нажмите кнопку **ДОБАВИТЬ**, чтобы добавить новое приложение.
   
    ![Добавление приложения][6]
6. Укажите **ИМЯ** приложения и выберите **ВЕБ-ПРИЛОЖЕНИЕ И (ИЛИ) ВЕБ-API**.
   
    ![Добавление приложения][10]
7. Укажите **URL-адрес входа** и **URI идентификатора приложения**. Он не обязательно должен быть фактической конечной точкой, а просто допустимым URI.
   
    ![Добавление приложения][11]
8. Завершите создание приложения, а затем щелкните **Настройка**.
   
    ![configure][12]
9. Прокрутите до раздела **Ключи** и выберите **1 год** в списке **Выбрать длительность**. Значение ключа будет отображено после сохранения, поэтому вы скопируете ключ позднее.
   
    ![задание длительности ключа][13]
10. Прокрутите вниз и щелкните **Добавить приложение**.
11. Выберите **Приложения Майкрософт**.
12. Найдите и выберите **API управления службами Microsoft Azure**, а затем завершите работу мастера.
13. В разделе **Разрешения для других приложений** найдите раздел **API управления службами Microsoft Azure** и щелкните **Делегированные разрешения**.
14. Выберите **Доступ к управлению службами Azure**.
    
     ![permissions][2]
15. В нижней части страницы нажмите кнопку **СОХРАНИТЬ**.
16. После завершения сохранения найдите и сохраните ИДЕНТИФИКАТОР КЛИЕНТА и ключ.
    
     ![секреты веб-приложения][14]

## Получение имени домена
Имя домена иногда требуется для кода проверки подлинности. Простой способ идентифицировать правильное имя домена:

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Наведите указатель мыши на имя в правом верхнем углу и запомните домен, который отобразится во всплывающем окне.
   
    ![Идентификация имени домена][3]

## Пример консольного приложения
Получите нужную библиотеку управления, установив с помощью [консоли диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console) следующие пакеты, которые содержат нужные библиотеки управления. Для этого в Visual Studio последовательно выберите элементы **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**:

    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


Создайте консольное приложение с именем **SqlDbAuthSample** и замените содержимое файла **Program.cs** следующим:

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Соответствующие примеры кода, связанные с аутентификацией Azure AD, см. в [блоге о безопасности SQL Server](http://blogs.msdn.com/b/sqlsecurity/) на сайте MSDN.

## См. также
* [Создание базы данных SQL с помощью C#](sql-database-get-started-csharp.md)
* [Подключение к базе данных SQL с использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md)

<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0608_2016-->