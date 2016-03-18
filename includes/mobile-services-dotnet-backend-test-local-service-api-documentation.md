
1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект службы и выберите в контекстном меню **Отладка** пункт **Запустить новый экземпляр**.

    ![Локальный запуск проекта мобильной службы](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Visual Studio по умолчанию размещает мобильную службу локально в IIS Express.

2. Щелкните правой кнопкой мыши значок IIS Express на панели задач Windows и проверьте, запущена ли мобильная служба.

	 ![Проверка мобильной службы на панели задач](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. На начальной странице серверной части .NET нажмите кнопку **Попробовать**.

    ![Начальная страница мобильной службы](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Откроется страница документации по API, с помощью которой можно протестировать мобильную службу.

	>[AZURE.NOTE]При локальном запуске для доступа к этой странице аутентификация не требуется. При запуске в Azure для доступа к этой странице вам понадобится указать ключ приложения в качестве пароля (без имени пользователя).

4. Щелкните ссылку **GET tables/TodoItem** (Таблицы GET/TodoItem).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Откроется страница ответа GET для API.

5. Щелкните ссылку **Try this out** (Попробуйте в деле), а затем нажмите **Send** (Отправить).
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	В локальную мобильную службу будет отправлен запрос GET, который должен вернуть все строки из таблицы TodoItem. Так как таблица заполнена инициализатором, в ответном сообщении возвращаются два объекта TodoItem. Дополнительные сведения об инициализаторах см. в разделе [Изменение модели данных в мобильной службе серверной части .NET](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=Oct15_HO3-->