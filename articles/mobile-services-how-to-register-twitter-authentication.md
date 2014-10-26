<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Регистрация приложений для входа в Twitter с помощью мобильных служб

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Twitter для проверки подлинности с помощью мобильных служб Azure.

<div class="dev-callout"><b>Примечание</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div>

1.  Перейдите на веб-сайт [разработчиков Twitter][разработчиков Twitter] и войдите с помощью учетной записи Twitter. Нажмите кнопку **Create a new application** (Создать приложение).

    ![][]

2.  Введите значения **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт) для своего приложения, затем введите URL-адрес мобильной службы, к которому добавлен путь */login/twitter* в поле **Callback URL** (URL-адрес обратного вызова).

    > [WACOM.NOTE]Для серверной мобильной службы для .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем *signin-twitter* мобильной службы как службы .NET, например<code><a href="https://todolist.azure-mobile.net/signin-twitter" class="uri">https://todolist.azure-mobile.net/signin-twitter</a></code>.

    ![][1]

3.  В нижней части страницы прочитайте и примите условия соглашения, введите текст проверки CAPTCHA и нажмите кнопку **Create your Twitter application** (Создать приложение для Twitter).

    ![][2]

    После этого приложение будет зарегистрировано и появятся сведения о приложении.

4.  Запомните значения полей **Consumer key** (Ключ клиента) и **Consumer secret** (Секрет клиента).

    ![][3]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет клиента &mdash; это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе со своим приложением.</p>
</div>

5.  Откройте вкладку **Settings** (Параметры), прокрутите экран вниз и установите флажок **Allow this application to be used to sign in with Twitter** (Разрешить этому приложению вход с помощью Twitter). Затем нажмите кнопку **Update this Twitter application's settings** (Обновить параметры приложения для Twitter).

    ![][4]

Теперь вы готовы использовать имя входа Twitter для проверки подлинности в вашем приложении, предоставляя мобильным службам ключ клиента и секрет клиента.



  [twitter.com]: http://go.microsoft.com/fwlink/p/?LinkID=268287
  [разработчиков Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
