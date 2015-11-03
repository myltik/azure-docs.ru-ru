>[AZURE.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Перейдите на веб-сайт [Google Cloud Console](https://console.developers.google.com/project) и войдите с помощью учетной записи Google. Щелкните элемент **Create Project** (Создать проект).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]При наличии существующего проекта после входа в систему открывается страница <strong>Проекты</strong>. Чтобы создать новый проект из панели мониторинга, откройте <strong>Проект API</strong>, нажмите <strong>Создать...</strong> в разделе <strong>Другие проекты</strong>, введите имя проекта и нажмите кнопку <strong>Создать проект</strong>.

2. Введите имя проекта, примите условия предоставления услуг и щелкните кнопку **Создать**. Выполните требуемую проверку с помощью SMS и снова щелкните **Создать**.

3. Запишите номер проекта в разделе **Проекты**.

	Позже в этом учебнике данное значение будет использоваться как переменная PROJECT\_ID в клиенте.

4. В левом столбце разверните пункт **APIs & auth** (API и аутентификация), щелкните **APIs** (Интерфейсы API), прокрутите список вниз и щелкните **Google Cloud Messaging for Android** (Google Cloud Messaging для Android). На следующей странице щелкните **Enable API** (Включить API) и примите условия предоставления услуг.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Щелкните **Credentials** (Учетные данные), а затем последовательно выберите **Add Credential** (Добавить учетные данные) и **API Key** (Ключ API).


   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. В окне **Create a new key** (Создание нового ключа) щелкните **Server key** (Ключ сервера). В следующем окне щелкните элемент **Create** (Создать).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key5.png)


   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key6.png)

7. Запишите значение **API KEY** (Ключ API).


	Этот ключ API службы Azure будут использовать для аутентификации в службе GCM и отправки push-уведомлений от имени вашего приложения.

<!---HONumber=Nov15_HO1-->