
###Создание проекта Google Cloud Messaging с ключом API

>[AZURE.NOTE] Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Перейдите к [консоли Google Cloud](https://console.developers.google.com/project) и войдите в нее с помощью учетной записи Google.

2. Щелкните **Go to project** (Перейти к проекту), а затем нажмите кнопку **Create Project** (Создать проект).
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Введите имя проекта.

4. Запишите номер проекта, который отображается под текстовым полем **Project name** (Имя проекта). Этот номер нужно будет вставить в файл манифеста Android далее в данном учебнике. ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. Щелкните **Создать**.

6. Убедитесь, что в левом столбце выбран параметр **Overview** (Обзор), и в области "Mobile APIs" (Мобильные API) щелкните **Google Cloud Messaging**. Затем на следующей странице щелкните **Enable** (Включить).

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. На следующей странице щелкните **Go to Credentials** (Перейти к учетным данным), затем на следующей странице выберите **Google Cloud Messaging** из первого раскрывающегося списка и **Web server** (Веб-сервер) — из второго. Щелкните **What credentials do I need?** (Какие требуются учетные данные?).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. На странице **Add credentials to your project** (Добавление учетных данных в проект) щелкните **Create API key** (Создать ключ API).

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Запишите значение **API KEY** (Ключ API). Это значение ключа API будет использовано позже для настройки в разделе «Системное push-уведомление». Теперь нажмите кнопку **Done** (Готово).

<!---HONumber=AcomDC_0608_2016-->