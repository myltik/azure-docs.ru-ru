
Последний необязательный шаг этого учебника состоит в проверке данных, которые хранятся в базе данных SQL, связанной с мобильной службой.

1. На [классическом портале Azure](https://manage.windowsazure.com/) щелкните управление для базы данных, связанной с вашей мобильной службой.
   
    ![Вход для управления базой данных SQL](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)
2. На портале выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Запрос будет похож на показанный ниже, но укажите имя своей базы данных вместо <code>todolist</code>.</p>
   
        SELECT * FROM [todolist].[todoitems]
   
    ![Запрос к базе данных SQL для получения хранящихся в ней элементов](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)
   
    Обратите внимание: таблица содержит столбцы Id, \_\_createdAt, \_\_updatedAt и \_\_version. Эти столбцы поддерживают синхронизацию автономных данных и реализованы в базовом классе [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Дополнительные сведения см. в разделе [Приступая к работе с синхронизацией автономных данных].

<!---HONumber=AcomDC_1203_2015-->