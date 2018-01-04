
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Получение строки подключения на портале Azure
Используйте [портал Azure](https://portal.azure.com/) получить строку соединения, необходимо, чтобы программа клиента для взаимодействия с базой данных SQL Azure. 

1. Выберите **ПРОСМОТРЕТЬ все** > **баз данных SQL**.

2. Введите имя базы данных в текстовое поле фильтра рядом в верхнем левом углу **баз данных SQL** колонку.

3. Выберите строку для базы данных.

4. После появления колонке базы данных, для удобства visual select **свернуть** кнопки, чтобы свернуть стоечные модули, используемые для просмотра и фильтрации базы данных. 
   
    ![Фильтр для определения базы данных][10-FilterDatabase]
5. В колонке базы данных, выберите **Показать строки подключения базы данных**.

6. Если вы планируете использовать библиотеку подключений ADO.NET, скопируйте строку с меткой **ADO**. 
   
    ![Копирование строки подключения ADO для базы данных][20-CopyAdoConnectionString]
7. Вставьте строку подключения в код клиентской программы в одном или другом формате.

Дополнительные сведения см. в разделе [строки соединения и файлы конфигурации](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
