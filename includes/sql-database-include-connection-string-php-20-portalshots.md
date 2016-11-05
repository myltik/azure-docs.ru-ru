<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Получение строки подключения на портале Azure
Для получения строки подключения, необходимой для взаимодействия клиентской программы с Базой данных SQL Azure, используйте [портал Azure](https://portal.azure.com/).

1. Последовательно выберите **ПРОСМОТРЕТЬ** > **Базы данных SQL**.
   
    ![Выбор SQL][1-select-sql]
2. Введите имя базы данных в текстовое поле фильтра рядом с верхней левой частью колонки **базы данных SQL**.
   
    ![Выбор базы данных][2-select-database]]
3. Щелкните строку с вашей базой данных.
4. Для наглядности после появления колонки для базы данных можно щелкнуть кнопки "Свернуть", чтобы свернуть колонки, используемые для просмотра и фильтрации базы данных.
5. На колонке для базы данных нажмите **Показать строки подключения к базе данных**.
6. Если вы планируете использовать библиотеку подключений PHP, скопируйте строку с меткой **PHP**.
   
    ![Копирование строки подключения PHP для базы данных][3-get-connection-string]
7. Вставьте данные строки подключения в код клиентской программы. Текст {your\_password\_here} необходимо заменять на фактический пароль.

<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-php.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0406_2016-->