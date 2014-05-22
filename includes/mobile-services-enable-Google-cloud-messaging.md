

<p></p>

<div class="dev-callout"><b>Примечание.</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Перейдите на веб-сайт <a href="http://cloud.google.com/console" target="_blank">Консоль Google Cloud</a> и войдите с помощью учетной записи Google. Щелкните элемент **CREATE PROJECT** (СОЗДАТЬ ПРОЕКТ).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	<div class="dev-callout"><b>Примечание.</b>
	<p>При наличии существующего проекта после входа в систему открывается страница <strong>Projects</strong> (Проекты). Чтобы создать новый проект из панели мониторинга, откройте <strong>Проект API</strong>, нажмите <strong>Создать...</strong> в разделе <strong>Другие проекты</strong>, введите имя проекта и нажмите кнопку <strong>Создать проект</strong>.</p>
    </div>

2. Введите имя проекта, примите условия предоставления услуг и щелкните элемент **Create** (Создать). Выполните требуемую проверку с помощью SMS и снова щелкните элемент **Create** (Создать).

3. Запишите номер проекта в разделе **Projects** (Проекты). 

	Позже в этом учебнике данное значение будет использоваться как переменная PROJECT_ID для клиента.

4. В левом столбце щелкните элемент **APIs & auth** (API и проверка подлинности), выполните прокрутку вниз и щелкните переключатель, чтобы включить **Google Cloud Messaging for Android**, и примите условия соглашения. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Щелкните элемент **Credentials** (Учетные данные), а затем **CREATE NEW KEY** (СОЗДАТЬ НОВЫЙ КЛЮЧ). 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. В области **Create a new key** (Создать новый ключ) щелкните элемент **Server key** (Ключ сервера). В следующем окне щелкните элемент **Create** (Создать).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Запишите значение **API key** (Ключ API).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


