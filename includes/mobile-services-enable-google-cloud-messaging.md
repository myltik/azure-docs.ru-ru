
1. Перейдите к [консоли Google Cloud](https://console.developers.google.com/project) и войдите в нее с помощью учетной записи Google. 
 
2. Щелкните **Create Project** (Создание проекта), введите имя проекта и щелкните **Create** (Создать). Выполните требуемую проверку с помощью SMS и снова щелкните **Create** (Создать).

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 В поле **Project name** (Имя проекта) введите новое имя проекта и щелкните **Create project** (Создать проект).

3. Запишите номер проекта в разделе **Projects** (Проекты). В клиенте это значение нужно будет указать как переменную *PROJECT\_ID*.

4. На панели мониторинга проекта щелкните **Use Google APIs** (Использовать API Google) > **Cloud Messaging for Android** (Обмен сообщениями в облаке для Android) и на следующей странице щелкните **Enable API** (Включить API).

5. В диспетчере API щелкните **Credentials** (Учетные данные) > **Add Credential** (Добавить учетные данные) > **API Key** (Ключ API).

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. В окне **Create a new key** (Создание нового ключа) щелкните **Server key** (Ключ сервера), введите имя ключа и щелкните **Create** (Создать).

7. Запишите значение **API KEY** (Ключ API).

	Этот ключ API службы Azure будут использовать для аутентификации в службе GCM и отправки push-уведомлений от имени вашего приложения.

<!---HONumber=AcomDC_1203_2015-->