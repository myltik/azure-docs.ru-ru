<properties
	pageTitle="Регистрация для проверки подлинности Google | Microsoft Azure"
	description="Узнайте, как зарегистрировать приложения, чтобы использовать Google для проверки подлинности с помощью мобильных служб Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/15/2015"
	ms.author="glenga"/>

# Регистрация приложений для входа в мобильные службы с помощью Google

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Google для проверки подлинности с помощью мобильных служб Azure.

>[AZURE.NOTE]Это учебник о [мобильных службах Azure](http://azure.microsoft.com/services/mobile-services/) — решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница дополняет статью [Добавление проверки подлинности в существующее приложение мобильных служб Azure](mobile-services-ios-get-started-users.md), в которой рассказано, как настроить пользовательский вход в приложение. <br/>Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь со статье [Приступая к работе с мобильными службами](mobile-services-ios-get-started.md).

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

3. Перейдите на веб-сайт [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

4. На панели навигации слева щелкните **API и авторизация**, а затем в разделе **API социальных сетей** щелкните **API Google+** > **Включить API**.

5. Щелкните **API и авторизация** > **Учетные данные** > **Экран согласия OAuth**, а затем выберите **электронный адрес**, введите **имя продукта** и нажмите кнопку **Сохранить**.

6. На вкладке **Учетные данные** щелкните **Добавить учетные данные** > **Идентификатор клиента OAuth 2.0**, а затем выберите **Веб-приложение**.

7. Введите URL-адрес мобильной службы в поле **Авторизованные источники JavaScript**, замените созданный URL-адрес в поле **Авторизованный универсальный код ресурса (URI) перенаправления** на URL-адрес в одном из следующих форматов, а затем щелкните **Создать идентификатор клиента**:

	+ **Серверная служба .NET**: `https://<mobile_service>.azure-mobile.net/signin-google`;
	+ **Серверная служба JavaScript**: `https://<mobile_service>.azure-mobile.net/login/google`.

	 >[AZURE.NOTE]Убедитесь, что для типа интерфейса мобильных служб используется правильный формат пути URL-адреса перенаправления. Если он неправильный, проверка подлинности не будет успешной.

8. На следующем экране запишите идентификатор клиента и секрет клиента.

    > [AZURE.IMPORTANT]Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.

Теперь вы можете настроить в своей мобильной службе использование проверки подлинности Google для входа в приложение.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_1203_2015-->