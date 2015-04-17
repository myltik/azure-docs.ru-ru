

>[AZURE.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Перейдите на веб-сайт <a href="http://cloud.google.com/console" target="_blank">Консоль Google Cloud</a>, войдите в систему с помощью учетных данных Google и нажмите кнопку **Create Project** (Создать проект).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]Если у вас уже есть проект, после входа вы будете перенаправлены на страницу <strong>Projects</strong>. Чтобы создать на панели мониторинга новый проект, разверните <strong>API Project</strong>, щелкните <strong>Create...</strong> в разделе <strong>Other projects</strong>, введите имя проекта и нажмите кнопку <strong>Create project</strong>.

2. Введите имя проекта, примите условия предоставления услуг и щелкните **Create** (Создать). Выполните требуемую проверку с помощью SMS и снова щелкните **Create** (Создать).

3. Запишите номер проекта в разделе **Projects** (Проекты). 

	Позже в этом учебнике данное значение будет использоваться как переменная PROJECT_ID для клиента.

4. В левом столбце разверните пункт **API-интерфейсы и проверка подлинности**, щелкните **API-интерфейсы**, прокрутите список вниз и щелкните переключатель, чтобы включить **Обмен сообщениями через облачную службу Google для Android** и примите условия предоставления услуг. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Щелкните **Credentials** (Учетные данные), а затем **Create new key** (Создать новый ключ). 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. В области **Create a new key** (Создать новый ключ) щелкните элемент **Server key** (Ключ сервера). В следующем окне щелкните элемент **Create** (Создать).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Запишите значение **API key** (Ключ API).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	Этот ключ API службы Azure будут использовать для аутентификации в службе GCM и отправки push-уведомлений от имени вашего приложения.


<!--HONumber=49-->