
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Получение строки подключения на портале Azure
Для получения строки подключения, необходимой для взаимодействия клиентской программы с Базой данных SQL Azure, используйте [портал Azure](https://portal.azure.com/). 

1. Щелкните **Все службы** > **Базы данных SQL**.

2. Введите имя базы данных в текстовое поле фильтра рядом с верхней левой частью колонки **Базы данных SQL**.

3. Выберите строку со своей базой данных.

4. Для удобства после появления колонки для базы данных нажмите кнопку **Свернуть**, чтобы свернуть колонки, используемые для просмотра и фильтрации базы данных. 
   
5. В колонке для базы данных выберите **Показать строки подключения к базе данных**.

6. Если вы планируете использовать библиотеку подключений ADO.NET, скопируйте строку с меткой **ADO**. 
   
    ![Копирование строки подключения ADO для базы данных][20-CopyAdoConnectionString]
7. Вставьте строку подключения в код клиентской программы в одном или другом формате.

Дополнительные сведения см. в описании [строк подключения и файлов конфигурации](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
