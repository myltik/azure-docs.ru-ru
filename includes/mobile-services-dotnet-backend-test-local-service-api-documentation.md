
1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект службы и в контекстном меню **Отладка** выберите пункт **Запустить новый экземпляр**.

    ![start mobile service project locally](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Visual Studio по умолчанию размещает мобильную службу локально в IIS Express.

2. Щелкните правой кнопкой мыши значок IIS Express на панели задач Windows и проверьте, запущена ли мобильная служба.

	 ![verify the mobile service in the taskbar](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. На начальной странице мобильной службы щелкните **Попробуйте в деле**.

    ![mobile service start up page](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Откроется страница документации по API, с помощью которой можно протестировать мобильную службу.

	>[AZURE.NOTE]При локальном запуске для доступа к этой странице аутентификация не требуется. При запуске в Azure для доступа к этой странице вам понадобится указать ключ приложения в качестве пароля (без имени пользователя).

4. Щелкните ссылку **Таблицы GET/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Откроется страница ответа GET для API.

5. Щелкните ссылку **Попробуйте в деле**, а затем щелкните **Отправить**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	В локальную мобильную службу будет отправлен запрос GET, который должен вернуть все строки из таблицы TodoItem. Так как таблица заполнена инициализатором, в ответном сообщении возвращаются два объекта TodoItem. Дополнительную информацию об инициализаторах см. в разделе [Изменение модели данных в мобильной службе серверной части .NET](./ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)


<!--HONumber=42-->
