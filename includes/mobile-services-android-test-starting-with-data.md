Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1. В меню **Выполнить** выберите **Выполнить** для запуска приложения.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

    You can restart the app to see that the changes were persisted to the database in Azure. You can also examine the database using the Azure Management portal:  the next two steps do this to view the changes in your database.


4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

На этом изучение учебника для Android **Начало работы с данными** завершено. <!--HONumber=52-->
