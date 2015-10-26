<properties
	pageTitle="Настройка проверки подлинности учетной записи Майкрософт для приложения служб приложений"
	description="Настройка проверки подлинности учетной записи Майкрософт для приложения служб приложений."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Настройка приложения для использования входа по учетной записи Майкрософт

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

В этом разделе показано, как настроить мобильные приложения Azure для использования учетной записи Майкрософт в качестве поставщика проверки подлинности.

## <a name="register"> </a>Регистрация приложения с помощью учетной записи Майкрософт

1. Войдите на [портал управления Azure] и перейдите к своему мобильному приложению.

2. Щелкните **Параметры**, **Проверка подлинности пользователя** и **Учетная запись Майкрософт**. Скопируйте значение **URL-адрес перенаправления**. Оно понадобится при настройке нового приложения для учетной записи Майкрософт.

3. Перейдите на страницу [Мои приложения] в центре разработки для учетной записи Майкрософт и войдите по учетной записи Майкрософт, если это требуется.

4. Щелкните элемент **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Я принимаю**.

5. Щелкните **Параметры API**. Выберите **Да** для пункта **Мобильное или классическое клиентское приложение**. В поле **URL-адрес перенаправления** введите **URL-адрес перенаправления**, скопированный ранее. Это шлюз мобильного приложения плюс _/signin-microsoft_. Пример: `https://contosogateway.azurewebsites.net/signin-microsoft`. Убедитесь, что используете схему HTTPS. После ввода URL-адреса перенаправления нажмите кнопку **Сохранить**.

	![][0]

	>[AZURE.NOTE]Для существующей регистрации приложения учетной записи Майкрософт может потребоваться сначала включить **расширенную безопасность перенаправления**.

4. Щелкните **Параметры приложений** и запишите значения **Идентификатор клиента** и **Секрет клиента**.

    > [AZURE.NOTE]Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.


## <a name="secrets"> </a>Добавление сведений учетной записи Майкрософт в мобильное приложение

1. На [портале управления Azure] в колонке настройки учетной записи Майкрософт для вашего мобильного приложения вставьте идентификатор клиента и секрет клиента, значения которых были получены ранее. Нажмите кнопку **Сохранить**.

    ![][1]

Теперь вы можете использовать учетную запись Майкрософт для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[портал управления Azure]: https://portal.azure.com/
[портале управления Azure]: https://portal.azure.com/
 

<!---HONumber=Oct15_HO3-->