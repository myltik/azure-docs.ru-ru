<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Регистрация приложений для входа в мобильные службы с помощью Google

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Google для проверки подлинности с помощью мобильных служб Azure.

<div class="dev-callout"><b>Примечание</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Перейдите на веб-сайт [Google apis][Google apis], войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

    ![][]

2.  Щелкните **API & Auth** (API и авторизация), затем **Credentials** (Учетные данные), а потом **Create new Client ID** (Создать новый идентификатор клиента).

    ![][1]

3.  Выберите **Web application** (Веб-приложение), введите URL-адрес мобильной службы в поле **Authorized JavaScript Origins** (Авторизованные источники JavaScript), замените созданный URL-адрес в поле **Authorized Redirect URI** (Авторизованный универсальный код ресурса (URI) перенаправления) на URL-адрес мобильной службы, к которому добавлен путь */login/google*, а затем нажмите кнопку **Create client ID** (Создать идентификатор клиента).

    > [WACOM.NOTE]Для серверной мобильной службы для .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем *signin-google* мобильной службы как службы .NET, например <code><a href="https://todolist.azure-mobile.net/signin-google" class="uri">https://todolist.azure-mobile.net/signin-google</a></code>.

    ![][2]

4.  В разделе **Client ID for web applications** (Идентификатор клиента для веб-приложений) запишите значения полей **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента).

    ![][3]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет клиента &mdash; это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе со своим приложением.</p>
</div>

Теперь вы готовы использовать имя входа Google для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.



  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
