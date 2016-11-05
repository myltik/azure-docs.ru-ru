
1. Перейдите к [консоли Google Cloud](https://console.developers.google.com/project) и войдите в нее с помощью учетной записи Google. 
2. Щелкните **Create Project** (Создание проекта), введите имя проекта и щелкните **Create** (Создать). Выполните требуемую проверку с помощью SMS и снова щелкните **Create** (Создать).
   
       ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)
   
     В поле **Project name** (Имя проекта) введите новое имя проекта и щелкните **Create project** (Создать проект).
3. Щелкните **Utilities and More** (Служебные программы и другое) и **Project Information** (Сведения о проекте). Запишите **номер проекта**. В клиенте это значение нужно будет указать как переменную `SenderId`.
   
       ![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. На панели мониторинга проекта в разделе **Mobile APIs** (API мобильных служб) выберите **Google Cloud Messaging**, на следующей странице щелкните **Enable** (Включить) и примите условия предоставления услуг.
   
    ![Включение GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Включение GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png)
5. На панели мониторинга проекта щелкните **Credentials** > **Create Credential** > **API Key** (Учетные данные > Создать учетные данные > Ключ API).
   
       ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. В окне **Create a new key** (Создание нового ключа) щелкните **Server key** (Ключ сервера), введите имя ключа и щелкните **Create** (Создать).
7. Запишите значение **API KEY** (Ключ API).
   
    Этот ключ API службы Azure будут использовать для аутентификации в службе GCM и отправки push-уведомлений от имени вашего приложения.

<!---HONumber=AcomDC_0608_2016-->