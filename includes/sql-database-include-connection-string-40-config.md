
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### Пример файла конфигурации для обеспечения безопасности строки подключения


Размещение строки подключения в коде C# в виде литералов опасно. Рекомендуется помещать ее в файл конфигурации. В нем можно отредактировать строку в любое время без необходимости повторной компиляции.

Предположим, что скомпилированная программа на C# имеет имя **ConsoleApplication1.exe** и этот EXE-файл находится в каталоге **bin\\debug**.

В этом примере большая часть строки подключения хранится в файле конфигурации с именем **ConsoleApplication1.exe.config**. Этот файл конфигурации также должен располагаться в каталоге **bin\\debug**.

В XML-коде следующего файла конфигурации вы видите строку подключения с именем **ConnectionString4NoUserIDNoPassword**. Код C# ищет эту строку.

Измените на реальные имена заполнители:

- {имя\_вашего\_сервера};
- {имя\_вашей\_базы\_данных}.



		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		
		    <connectionStrings>
		        <clear />
		        <add name="ConnectionString4NoUserIDNoPassword"
		        providerName="System.Data.ProviderName"
		
		        connectionString=
				"Server=tcp:{your_serverName_here}.database.windows.net,1433;
				Database={your_databaseName_here};
				Connection Timeout=30;
				Encrypt=True;
				TrustServerCertificate=False;" />
		    </connectionStrings>
		</configuration>



Для данного примера мы опустили два параметра:

- User ID={ваше\_имя\_пользователя};
- Password={ваш\_пароль}.


Вы можете включить их, но иногда лучше, чтобы программа получала эти значения с клавиатурного ввода от пользователя. Здесь возможны варианты в зависимости от обстоятельств.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->