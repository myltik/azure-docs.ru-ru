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

5. Запишите имя **базы данных SQL** и **имя сервера**. Именем пользователя будет yourusername@yourserver.

	![Получение сведений о подключении][3-get-connection-details]

7.  Вставьте данные подключения в код клиентской программы. Текст {your\_password\_here} необходимо заменять на фактический пароль.


<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-details]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-details.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0406_2016-->