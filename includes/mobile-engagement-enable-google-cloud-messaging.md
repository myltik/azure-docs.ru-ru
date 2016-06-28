
###Создание проекта Google Cloud Messaging с ключом API

>[AZURE.NOTE] Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Перейдите к [консоли Google Cloud](https://console.developers.google.com/project) и войдите в нее с помощью учетной записи Google.

2. Перейдите к разделу **Все проекты** и нажмите кнопку **Создать проект**.

3. Введите **Имя проекта** и нажмите кнопку **Создать**

4. Создав проект, не забудьте записать **номер проекта**, который представляет собой длинный набор чисел. Его можно найти в разделе **IAM и администрирования** в **параметрах** проекта. Этот номер понадобится позже.
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Теперь создадим ключ для платформы Google Cloud Messaging, который будет использоваться для отправки уведомлений на устройства Android. Откройте раздел **Диспетчер API** и щелкните **Google Cloud Messaging** в разделе **Мобильные API**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. На следующей странице щелкните **Включить**. Панель мониторинга предложит создать учетные данные. Щелкните кнопку **Перейти к учетным данным**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Выберите **Google Cloud Messaging** в первом раскрывающемся списке и **Веб-сервер** — во втором. Затем щелкните **Какие требуются учетные данные?**

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. На странице **Добавление учетных данных в проект** щелкните **Создать ключ API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Запишите значение **API KEY** (Ключ API). Это значение ключа API будет использовано позже для настройки в разделе «Системное push-уведомление». Теперь нажмите кнопку **Готово**.

<!---HONumber=AcomDC_0622_2016-->