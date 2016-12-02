
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## <a name="create-a-new-azure-sql-server-level-firewall"></a>Создание брандмауэра уровня сервера SQL Azure
Используйте следующие шаги на портале Azure, чтобы создать правило брандмауэра уровня сервера, разрешающее подключения с отдельного IP-адреса (клиентского компьютера) или всего диапазона IP-адресов логического сервера базы данных SQL.

1. Если вы еще этого не сделали, подключитесь к [порталу Azure](http://portal.azure.com).
2. В колонке по умолчанию щелкните **Серверы SQL**.
   
      ![Новый брандмауэр сервера](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)
3. В колонке **Серверы SQL** выберите сервер, для которого нужно создать правило брандмауэра.
   
     ![Новый брандмауэр сервера](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
4. Проверьте свойства сервера и щелкните **Брандмауэр**.
   
     ![Новый брандмауэр сервера](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
   
   > [!NOTE]
   > Также на панели инструментов колонки **База данных** можно открыть колонку серверного уровня **Параметры брандмауэра**.
    
    
6. Щелкните **Добавить IP-адрес клиента**, чтобы система Azure подставила ваш IP-адрес в поля правила.
   
      ![Новый брандмауэр сервера](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)
7. При необходимости щелкните IP-адрес, который был добавлен, чтобы изменить адрес брандмауэра и разрешить доступ с диапазона IP-адресов.
   
      ![Новый брандмауэр сервера](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
8. Нажмите кнопку **Сохранить** , чтобы создать правило в брандмауэре сервера.
   
     ![Новый брандмауэр сервера](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)
   
   > [!IMPORTANT]
   > IP-адрес вашего клиента, скорее всего, будет периодически меняться. В таком случае вы не сможете получить доступ к серверу, пока не создадите новое правило брандмауэра. Проверьте свой IP-адрес с помощью поисковой системы [Bing](http://www.bing.com/search?q=my%20ip%20address). Затем добавьте один IP-адрес или диапазон IP-адресов. Подробные сведения см. в разделе [Управление существующими правилами брандмауэра с помощью портала Azure](../articles/sql-database/sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal).
   > 
   > 



<!--HONumber=Nov16_HO5-->


