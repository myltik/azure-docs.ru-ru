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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Как настроить приложение службы приложений для использования имени для входа Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Twitter в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным электронным адресом. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Регистрация приложения в Twitter


1. Войдите на [портал управления Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он будет использован для настройки приложения Twitter.

2. Перейдите на веб-сайт [Twitter Developers] (Разработчики Twitter), войдите по учетным данным учетной записи Twitter и щелкните **Создать приложение**.

3. Введите в **имя** и **описание** для нового приложения. Вставьте **URL-адрес** приложения в поле **Веб-сайт**. Затем в поле **URL-адрес обратного вызова** вставьте **URL-адрес обратного вызова**, скопированный ранее. Это шлюз мобильного приложения, дополненный путем _/.auth/login/twitter/callback_. Например, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Убедитесь, что используете схему HTTPS.

    ![][0]
	

	> [AZURE.NOTE]Если вместо функции аутентификации или авторизации службы приложений используется шлюз службы приложений, то в URL-адресе перенаправления используется URL-адрес шлюза, дополненный путем _/signin-twitter_.


3.  В нижней части страницы прочтите и примите условия. Затем щелкните **Создать приложение Twitter**. После этого приложение будет зарегистрировано и появятся сведения о приложении.

4. Откройте вкладку **Параметры**, установите флажок **Разрешить использовать это приложение для входа в Twitter**, затем нажмите **Обновить параметры**.

5. Откройте вкладку **Ключи и токены доступа**. Запишите значения полей **Пользовательский ключ (ключ API)** и **Пользовательский секрет (секрет API)**.

    > [AZURE.NOTE]Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.


## <a name="secrets"> </a>Добавление данных Twitter в приложение


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Twitter**. Paste in the values you obtained earlier and click **Save**.


13. Вернитесь на [портал управления Azure] и перейдите к своему приложению. Щелкните **Параметры**, а затем **Аутентификация или авторизация**.

14. Если функция аутентификации или авторизации не включена, установите переключатель в положение **Вкл**.

15. Щелкните **Twitter**. Вставьте значения идентификатора и секрета приложения, полученные ранее. Нажмите кнопку **ОК**.

    ![][1]
	
16. По умолчанию служба приложений предоставляет вход, но не ограничивает доступ к содержимому сайта и интерфейсов API: эти функции должны осуществляться кодом приложения. Если вы хотите полностью защитить сайт с помощью службы входа Twitter, выберите из раскрывающегося списка **Действие, выполняемое, если запрос не прошел проверку подлинности** пункт **Twitter**. Тогда аутентификация всех запросов будет обязательна. Запросы, не прошедшие аутентификацию, будут перенаправляться для входа с помощью Twitter.

17. Щелкните **Сохранить**.

Теперь вы можете использовать Twitter для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[портал управления Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=Nov15_HO4-->