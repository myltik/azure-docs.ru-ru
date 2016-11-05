
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### Сравнение строки подключения
В следующей таблице сравниваются строки подключения, необходимые программе на C# для подключения к локальному SQL Server по сравнению с Базой данных SQL Azure в облаке. Различия выделены полужирным шрифтом.

| Строка подключения для<br/>Базы данных SQL Azure | Строка подключения для<br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{имя\_вашего\_сервера}**.database.windows.net,1433**;<br/>User ID={ваше\_имя\_для\_входа}**@{имя\_вашего\_сервера}**;<br/>Password={ваш\_пароль};<br/>**Database={имя\_вашей\_базы\_данных};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={имя\_вашего\_сервера};<br/>User ID={ваше\_имя\_для\_входа};<br/>Password={ваш\_пароль}; |

Параметр **Database=** является необязательным для SQL Server, но он необходим для Базы данных SQL.

Все параметры подробно рассматриваются в разделе [Свойство .NET ADO SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx).

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->