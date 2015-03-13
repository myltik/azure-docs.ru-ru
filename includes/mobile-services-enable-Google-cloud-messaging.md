
1. Перейдите на веб-сайт <a href="http://cloud.google.com/console" target="_blank">консоли Google Cloud,</a> войдите через учетную запись Google, затем щелкните **Создание проекта**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]При наличии существующего проекта после входа в систему открывается страница <strong>Projects</strong> (Проекты). Чтобы создать новый проект из панели мониторинга, разверните <strong>Проект API</strong>, щелкните <strong>Создать...</strong>  в пункте <strong>Другие проекты</strong>, а затем введите имя проекта и нажмите кнопку <strong>Создать проект</strong>.

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

	Значение ключа API будет использоваться для предоставления мобильным службам возможности выполнять аутентификацию с помощью GCM и отправки push-уведомлений от имени вашего приложения.


<!--HONumber=45--> 
