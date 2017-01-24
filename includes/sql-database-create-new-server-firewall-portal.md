
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Создание правила брандмауэра на уровне сервера с помощью портала Azure

1. В колонке сервера SQL Server в разделе "Параметры" щелкните **Брандмауэр**, чтобы открыть колонку брандмауэра для SQL Server.

    ![Брандмауэр SQL Server](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png)
    
2. Просмотрите IP-адрес клиента и убедитесь, что это именно ваш IP-адрес в Интернете. Для этого в любом браузере введите поисковый запрос "мой IP-адрес". Иногда адреса могут не совпадать по тем или иным причинам.

    ![Ваш IP-адрес](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. Для удобства предположим, что IP-адреса совпадают. Далее на панели инструментов нажмите кнопку **Добавить IP-адрес клиента**.

    ![Кнопка "Добавить IP-адрес клиента"](../articles/sql-database/media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Брандмауэр базы данных SQL на сервере можно открыть для одного IP-адреса или целого диапазона IP-адресов. Открыв брандмауэр, вы дадите возможность администраторам и пользователям SQL входить в любую базу данных на сервере, для которой у них есть действительные учетные данные.
    >

4. На панели инструментов нажмите кнопку **Сохранить**, чтобы сохранить это правило брандмауэра на уровне сервера, а затем нажмите кнопку **ОК**.

    ![Кнопка "Добавить IP-адрес клиента"](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

> [!Tip]
> Подробное руководство см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../articles/sql-database/sql-database-get-started.md).    
>


<!--HONumber=Jan17_HO1-->


