
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Войдите на портал [Azure](https://portal.azure.com/) по адресу http://portal.azure.com/.
2. В заголовке слева щелкните **ПРОСМОТРЕТЬ ВСЕ**. Отобразится колонка **Обзор**.
3. Найдите и выберите **Серверы SQL Server**. Отобразится колонка **Серверы SQL Server**.
   
    ![Поиск своего сервера Базы данных SQL Azure на портале][b21-FindServerInPortal]
4. Для удобства щелкните элемент управления "Свернуть" в предыдущей колонке **Обзор**.
5. В текстовом поле фильтра начните вводить имя своего сервера. Отобразится строка.
6. Щелкните строку с вашим сервером. Отобразится колонка для вашего сервера.
7. На колонке вашего сервера нажмите кнопку **Параметры**. Отобразится колонка **Параметры**.
8. Щелкните пункт **Брандмауэр**. Отобразится колонка **Параметры брандмауэра**.
   
    ![Последовательно выберите пункты "Параметры" > "Брандмауэр"][b31-SettingsFirewallNavig]
9. Щелкните **Добавить IP-адрес клиента**. В первом текстовом поле введите имя для нового правила.
10. Введите нижнее и верхнее значения IP-адресов для требуемого диапазона.
    
    * Удобными могут оказаться нижнее значение, оканчивающееся на **.0**, и верхнее — на **.255**.
    
    ![Добавьте допустимый диапазон IP-адресов][b41-AddRange]
11. Щелкните **Сохранить**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_0330_2016-->