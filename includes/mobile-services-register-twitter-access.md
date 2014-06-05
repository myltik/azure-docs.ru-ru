

Новые интерфейсы API Twitter версии 1.1 нуждаются в проверке подлинности до получения доступа к ресурсам. Сначала необходимо получить учетные данные, необходимые для запроса доступа с помощью OAuth 2.0. Затем необходимо надежно сохранить эти учетные данные в настройках приложения для ваших мобильных служб.

1. Если это еще не сделано, выполните действия, описанные в разделе <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Регистрация приложений для входа в Twitter с помощью мобильных служб</a>. 
  
  	Twitter создает учетные данные, необходимые для включения доступа к интерфейсам API Twitter версии 1.1. Эти учетные данные можно получить с веб-сайта разработчиков Twitter. 

2. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">разработчиков Twitter</a>, выполните вход, используя учетные данные учетной записи Twitter, перейдите к пункту **Мои приложения** и выберите свое приложение Twitter.

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png)

3. На вкладке **Сведения** приложения обратите внимание на следующие значения:

	+ **Ключ клиента**
	+ **Секрет клиента**
	+ **Маркер доступа**
	+ **Секрет маркера доступа**

	![](./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png)

4. Выполните вход на [Портал управления Windows Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

5. Щелкните вкладку **Удостоверение**, введите значения параметров **Ключ клиента** и **Секрет клиента**, полученные из Twitter, а затем щелкните **Сохранить**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Щелкните вкладку **Настройка**, прокрутите вниз до элемента **Параметры приложения** и введите пару **Имя** и **Значение** для каждого из следующих элементов, полученных с сайта Twitter, а затем нажмите кнопку **Сохранить**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Это сохраняет маркер доступа Twitter в параметрах приложения. Как и учетные данные клиента на вкладке **Удостоверение**, учетные данные доступа также хранятся в параметрах приложения в зашифрованном виде, и вы можете получить к ним доступ в своих серверных сценариях без жесткого кодирования их в файле сценария. Дополнительные сведения см. в разделе [Параметры приложения].

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Портал управления Windows Azure]: https://manage.windowsazure.com/
[Регистрация приложений для входа в Twitter с помощью мобильных служб]: /ru-ru/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Разработчики Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Параметры приложения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

