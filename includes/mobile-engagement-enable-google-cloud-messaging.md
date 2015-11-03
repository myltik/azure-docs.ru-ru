>[AZURE.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Перейдите на веб-сайт [Google Cloud Console](https://console.developers.google.com/project) и войдите с помощью учетной записи Google. Щелкните элемент **Create Project** (Создать проект).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. Введите имя проекта, примите условия предоставления услуг и щелкните кнопку **Создать**. Выполните требуемую проверку с помощью SMS и снова щелкните **Создать**.

3. Запишите номер проекта в разделе **Проекты**. Этот номер нужно будет вставить в файл манифеста Android ниже в данном руководстве.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. В левом столбце разверните пункт **APIs & auth** (API и аутентификация), щелкните **APIs** (Интерфейсы API), прокрутите список вниз и щелкните **Google Cloud Messaging for Android** (Google Cloud Messaging для Android). На следующей странице щелкните **Enable API** (Включить API) и примите условия предоставления услуг.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Щелкните **Credentials** (Учетные данные), а затем последовательно выберите **Add Credential** (Добавить учетные данные) и **API Key** (Ключ API).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. В окне **Create a new key** (Создание нового ключа) щелкните **Server key** (Ключ сервера). В следующем окне щелкните элемент **Create** (Создать).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. Запишите значение **API KEY** (Ключ API). Это значение ключа API будет использовано позже для настройки в разделе «Системное push-уведомление».

<!---HONumber=Nov15_HO1-->