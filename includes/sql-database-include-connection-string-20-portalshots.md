
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Получение строки подключения на портале Azure


Для получения строки подключения, необходимой для взаимодействия клиентской программы с Базой данных SQL Azure, используйте [портал предварительной версии Azure](http://portal.azure.com/):


1. Последовательно выберите **ПРОСМОТРЕТЬ** > **Базы данных SQL**.

2. Введите имя базы данных в текстовое поле фильтра рядом с верхней левой частью колонки **Базы данных SQL**.

3. Щелкните строку с вашей базой данных.

4. Для наглядности после появления колонки для базы данных можно щелкнуть кнопки "Свернуть", чтобы свернуть колонки, используемые для просмотра и фильтрации базы данных.
 
	![Фильтр для определения базы данных][10-FilterDatabase]

5. На колонке для базы данных нажмите **Показать строки подключения к базе данных**.

6. Если вы планируете использовать библиотеку подключений ADO.NET, скопируйте строку с меткой **ADO**.
 
	![Копирование строки подключения ADO для базы данных][20-CopyAdoConnectionString]
 
7. Вставьте строку подключения в код клиентской программы в одном или другом формате.



Дополнительные сведения можно найти в разделе <br/>[Строки подключения и файлы конфигурации](http://msdn.microsoft.com/library/ms254494.aspx).



<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->