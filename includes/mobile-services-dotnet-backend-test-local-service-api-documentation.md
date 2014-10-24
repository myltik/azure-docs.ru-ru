
1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект службы и выберите в контекстном меню **Отладка** пункт **Запустить новый экземпляр**.

    ![Локальный запуск проекта мобильной службы][Локальный запуск проекта мобильной службы]

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Visual Studio по умолчанию размещает мобильную службу локально в IIS Express.

2. Щелкните правой кнопкой мыши значок IIS Express на панели задач Windows и проверьте, запущена ли мобильная служба.

    ![Проверка мобильной службы на панели задач][Проверка мобильной службы на панели задач]

3. На начальной странице мобильной службы щелкните **Try it out** (Попробуйте в деле).

    ![Начальная страница мобильной службы][Начальная страница мобильной службы]

    Откроется страница документации по API, с помощью которой можно протестировать мобильную службу.

    >[WACOM.NOTE]При локальном запуске для доступа к этой странице проверка подлинности не требуется. При запуске в Azure для доступа к этой странице вам понадобится указать ключ приложения в качестве пароля (без имени пользователя).

4. Щелкните ссылку **GET tables/TodoItem** (Таблицы GET/TodoItem).

    ![][]

    Откроется страница ответа GET для API.

5. Щелкните ссылку **Try this out** (Попробуйте в деле), а затем нажмите **Send** (Отправить).

    ![][1]

    В локальную мобильную службу будет отправлен запрос GET, который должен вернуть все строки из таблицы TodoItem. Так как таблица заполнена инициализатором, в ответном сообщении возвращаются два объекта TodoItem. Дополнительные сведения об инициализаторах см. в разделе [Изменение модели данных в мобильной службе серверной части .NET][Изменение модели данных в мобильной службе серверной части .NET].

    ![][2]


[Локальный запуск проекта мобильной службы]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png
[Проверка мобильной службы на панели задач]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png
[Начальная страница мобильной службы]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png
[]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png
[1]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png
[Изменение модели данных в мобильной службе серверной части .NET]: ./ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
[2]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png
