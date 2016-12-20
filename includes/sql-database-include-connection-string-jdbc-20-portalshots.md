<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Получение строки подключения на портале Azure
Для получения строки подключения, необходимой для взаимодействия клиентской программы с Базой данных SQL Azure, используйте [портал Azure](https://portal.azure.com/) .

1. Выберите **ОБЗОР** > **Базы данных SQL**.
   
    ![Выбор SQL][1-select-sql]
2. Введите имя базы данных в текстовое поле фильтра рядом с верхней левой частью колонки **базы данных SQL** .
   
    ![Выбор базы данных][2-select-database]
3. Щелкните строку с вашей базой данных.
4. Для наглядности после появления колонки для базы данных можно щелкнуть стандартный элемент управления, чтобы свернуть колонки, используемые для просмотра и фильтрации базы данных.
5. На колонке для базы данных нажмите **Показать строки подключения к базе данных**.
6. Если вы планируете использовать библиотеку подключений JDBC, скопируйте строку с меткой **JDBC**.
   
    ![Копирование строки подключения JDBC для базы данных][3-get-connection-string]
7. Вставьте данные строки подключения в код клиентской программы.  Текст {your_password_here} необходимо заменять на фактический пароль.

Дополнительные сведения можно найти в разделе <br/>[Строки подключения и файлы конфигурации](https://msdn.microsoft.com/library/ms378428.aspx).

<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png


[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-jdbc.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Dec16_HO2-->


