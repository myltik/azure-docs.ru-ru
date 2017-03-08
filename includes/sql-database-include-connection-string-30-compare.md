
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Сравнение строки подключения
В следующей таблице сравниваются строки подключения, необходимые программе на C# для подключения к локальному SQL Server по сравнению с Базой данных SQL Azure в облаке. Различия выделены полужирным шрифтом.

| Строка подключения для<br/>Базы данных SQL Azure | Строка подключения для<br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{имя_вашего_сервера}**.database.windows.net,1433**;<br/>User ID={имя_пользователя}**@{your_serverName_here}**;<br/>Password={ваш_пароль}.<br/>**Database={имя_вашей_базы_данных};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={имя_вашего_сервера};<br/>User ID={имя_пользователя};<br/>Password={ваш_пароль}. |

Параметр **Database=** является необязательным для SQL Server, но он необходим для Базы данных SQL.

[Свойство .NET ADO SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) .

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


