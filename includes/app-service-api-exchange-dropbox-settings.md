4. В другом окне или вкладке браузера перейдите на [портал предварительной версии Azure](https://portal.azure.com).

3. Перейдите в колонку **приложения API** для вашего соединителя Dropbox. (Если вы все еще находитесь в колонке **Группа ресурсов**, просто щелкните соединитель Dropbox на диаграмме.)

4. Щелкните пункт **Параметры**, и в колонке **Параметры** выберите пункт **Проверка подлинности**.

	![Щелкните «Параметры»](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![Щелкните «Проверка подлинности».](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. В колонке «Проверка подлинности» введите идентификатор клиента и секрет клиента с сайта Dropbox и нажмите кнопку **Сохранить**.

	![Введите параметры и нажмите «Сохранить».](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. Скопируйте значение **URI перенаправления** (серое поле над идентификатором клиента и секретом клиента) и добавьте это значение на страницу, которую оставили открытой на предыдущем шаге.

	URI перенаправления соответствует следующему шаблону:

		[gatewayurl]/api/consent/redirect/[connectorname]

	Например:

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Получение URI перенаправления](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Создание приложения Dropbox](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=August15_HO6-->