### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Создание логического сервера SQL Server на портале Azure

1. Щелкните **Создать**, выполните поиск по запросу **логический сервер** и нажмите клавишу **ВВОД**.

    ![поиск логического сервера](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. Выберите **SQL Server (логический сервер)**. 

    ![выбор логического сервера](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. Щелкните **Создать**, чтобы открыть колонку создания логического сервера SQL Server.

   <kbd>![открыть колонку логический сервер](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd> ![колонке логический сервер](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png)</kbd>
  
3. В поле "Имя сервера" сервера SQL Server (логический сервер) введите допустимое имя для нового логического сервера. Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Имя нового сервера](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > Полным именем нового сервера будет <имя_вашего_сервера>.database.windows.net.
    >
    
4. В текстовом поле "Имя входа администратора сервера" укажите имя пользователя, которое будет использоваться сервером при проверке подлинности SQL Server. Это имя называется именем субъекта сервера. Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Учетные данные администратора SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. В полях **Пароль** и **Подтверждение пароля** укажите пароль для входа в учетную запись субъекта сервера. Зеленый флажок указывает, что выбран допустимый пароль.
    
    ![Пароль администратора SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. Выберите подписку, в которой у вас есть разрешение на создание объектов.

    ![Подписка](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. Над полем "Группа ресурсов" установите переключатель **Создать новую**, а затем в текстовом поле введите допустимое имя новой группы ресурсов. Также можно использовать существующую группу, если она уже создана. Зеленый флажок указывает, что выбрано допустимое имя.

    ![Создание группы ресурсов](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. В списке **Расположение** выберите центр обработки данных, соответствующий вашему расположению, например "Восточная Австралия".
    
    ![Расположение сервера](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > Параметр **Разрешить службам Azure доступ к серверу** невозможно изменить в этой колонке. Его можно изменить в колонке брандмауэра сервера. Дополнительные сведения см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](../articles/sql-database/sql-database-manage-servers-portal.md).
    >
    
9. Щелкните **Создать**.

    ![кнопка "Создать"](./media/sql-data-warehouse-create-logical-server/create.png)

