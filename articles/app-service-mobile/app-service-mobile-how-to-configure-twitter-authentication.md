<properties
	pageTitle="Настройка проверки подлинности Twitter для приложения служб приложений"
	description="Узнайте, как настроить проверку подлинности Twitter для приложения служб приложений."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
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

# Настройка использования имени для входа Twitter в приложении

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как настроить мобильные приложения Azure для использования Twitter в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Регистрация приложения в Twitter


1. Войдите на [портал управления Azure] и перейдите к своему мобильному приложению. Скопируйте свой **URL-адрес**. Он будет использован для настройки приложения Twitter.

2. Щелкните **Параметры**, **Проверка подлинности пользователя** и **Twitter**. Скопируйте **URL-адрес обратного вызова**. Он будет использован для настройки приложения Twitter.

3. Перейдите на веб-сайт [Twitter Developers] (Разработчики Twitter), войдите по учетным данным учетной записи Twitter и щелкните **Создать приложение**.

4. Введите в **имя** и **описание** для нового приложения. Вставьте **URL-адрес мобильного приложения** в поле **Веб-сайт**. Затем в поле **URL-адрес обратного вызова** вставьте **URL-адрес обратного вызова**, скопированный ранее. Это шлюз мобильного приложения, дополненный путем _/signin-twitter_. Пример: `https://contosogateway.azurewebsites.net/signin-twitter`. Убедитесь, что используете схему HTTPS.

    ![][0]

3.  В нижней части страницы прочтите и примите условия. Затем щелкните **Создать приложение Twitter**. После этого приложение будет зарегистрировано и появятся сведения о приложении.

4. Откройте вкладку **Параметры**, установите флажок **Разрешить использовать это приложение для входа в Twitter**, затем нажмите **Обновить параметры**.

5. Откройте вкладку **Ключи и токены доступа**. Запишите значения полей **Пользовательский ключ (ключ API)** и **Пользовательский секрет (секрет API)**.

    > [AZURE.NOTE]Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.


## <a name="secrets"> </a>Добавление сведений Twitter в мобильное приложение

1. На [портале управления Azure] в колонке настройки Twitter для вашего мобильного приложения вставьте значения ключа API и секрета API, полученные ранее. Нажмите кнопку **Сохранить**.

    ![][1]

Теперь вы можете использовать Twitter для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[портал управления Azure]: https://portal.azure.com/
[портале управления Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
 

<!---HONumber=Nov15_HO1-->