
Последний необязательный шаг этого учебника состоит в проверке данных, которые хранятся в базе данных SQL, связанной с мобильной службой. 

1. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.
 
	![sign-in to manage SQL Database](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет похож на показанный ниже запрос, но вместо <code>todolist</code> укажите имя своей базы данных.</p>

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Обратите внимание: таблица содержит столбцы Id, __createdAt, __updatedAt и __version. Эти столбцы поддерживают синхронизацию автономных данных и реализованы в базовом классе [EntityData](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Дополнительную информацию см. в разделе [Приступая к работе с синхронизацией автономных данных].<!--HONumber=42-->
