<properties
	pageTitle="Запросы к базе данных SQL с помощью C# | Microsoft Azure"
	description="Сведения об IP-адресах, строках подключения, файле конфигурации для безопасного входа и о бесплатной версии Visual Studio, которые позволят вашей программе C# подключиться к базе данных SQL Azure в облаке с помощью ADO.NET."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="genemi"/>


# Подключение к базе данных SQL и отправка запросов в нее с помощью языка C&#x23;

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Вы хотите написать программу на языке C#, в которой используется технология ADO.NET для подключения к базе данных SQL Azure в облаке.

В этом разделе приводится подробное описание всех действий для пользователей без опыта работы с базой данных SQL Azure и языком C#. Пользователи с опытом работы с Microsoft SQL Server и C# могут пропускать некоторые шаги, останавливаясь на тех, которые относятся к базам данных SQL.


## Предварительные требования


Чтобы запустить пример кода C#, вам потребуется следующее.


- Учетная запись и подписка Azure. Вы можете зарегистрироваться, чтобы получить [бесплатную пробную версию](http://azure.microsoft.com/pricing/free-trial/).


- Демонстрационная база данных **AdventureWorksLT** в службе базы данных SQL Azure.
 - Быстрое [создание демонстрационной базы данных](sql-database-get-started.md).


- Visual Studio 2013 с обновлением 4 или более поздняя версия. Майкрософт предлагает *бесплатную* версию Visual Studio Community.
 - [Загрузка Visual Studio Community](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Дополнительные варианты бесплатного использования Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Кроме того, [далее](#InstallVSForFree) в этом разделе описывается установка Visual Studio с помощью [портала предварительной версии Azure](http://portal.azure.com/).


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Шаг 1. Установка бесплатной версии Visual Studio Community


Установить Visual Studio можно следующим образом.

- Установите бесплатную версию Visual Studio Community. Для этого откройте в браузере страницу с продуктами Visual Studio, на которой представлены ссылки для скачивания бесплатных и других версий.
- С помощью [портала предварительной версии Azure](http://portal.azure.com/) перейдите на страницу загрузки, как описано ниже.


### Установка Visual Studio с помощью портала предварительной версии Azure


1. Войдите на [портал предварительной версии Azure](http://portal.azure.com/) по адресу http://portal.azure.com/.

2. Последовательно выберите **ПРОСМОТРЕТЬ ВСЕ** > **Базы данных SQL**. Откроется колонка для поиска баз данных.

3. В верхней части страницы в поле фильтра начните вводить имя базы данных **AdventureWorksLT**.

4. Когда появится строка с нужной базой данных для установки на сервер, щелкните ее. Откроется колонка этой базы данных.

5. Для удобства щелкните элемент управления «Свернуть» на каждой из предыдущих колонок.

6. В верхней части колонки базы данных нажмите кнопку **Открыть в Visual Studio**. Откроется новая колонка Visual Studio с ссылками на расположения установки Visual Studio.
 
	![Кнопка «Открыть в Visual Studio»][20-OpenInVisualStudioButton]

7. Щелкните ссылку **Community (бесплатно)** (ссылка может отличаться). Откроется новая веб-страница.

8. Используйте ссылки на открытой веб-странице, чтобы установить Visual Studio.

9. После установки Visual Studio в колонке **Открытие в Visual Studio** щелкните кнопку **Открыть в Visual Studio**. Откроется среда Visual Studio.

10. Для удобства использования панели **Обозреватель объектов SQL Server** в среде Visual Studio откроется диалоговое окно для ввода строки подключения.
 - Выберите пункт **Аутентификация SQL Server**, не **Аутентификация Windows**.
 - Не забудьте указать свою базу данных **AdventureWorksLT** (в диалоговом окне **Параметры** > **Свойства соединения**).

11. В **обозревателе объектов SQL Server** разверните узел своей базы данных.


## Шаг 2. Создание проекта в Visual Studio


В Visual Studio создайте проект, используя начальный шаблон для C# > Windows > **Консольное приложение**.


1. Последовательно выберите пункты **Файл**, **Создать** и **Проект**. Откроется диалоговое окно ****.

2. В разделе **Установлено** последовательно выберите C# и Windows. На средней панели отобразится шаблон **Консольное приложение**.

	![Диалоговое окно «Новый проект»][30-VSNewProject]

2. В поле **Имя** введите **ConnectAndQuery\_Example**. Нажмите кнопку **ОК**.


## Шаг 3. Добавление ссылки на сборку для обработки конфигурации


Поскольку в нашем примере C# используется сборка .NET Framework **System.Configuration.dll**, давайте добавим ссылку на нее.


1. В области **обозревателя решений** щелкните правой кнопкой узел **Ссылки** > **Добавить ссылку**. Откроется окно **Диспетчер ссылок**.

2. Разверните узел **Сборки** > **Framework**.

3. Прокрутите список и выберите **System.Configuration**. Убедитесь, что установлен соответствующий флажок.

4. Нажмите кнопку **ОК**.

5. Скомпилируйте программу, выбрав в меню **СБОРКА** > **Построить решение**.


## Шаг 4. Получение строки подключения


С помощью [портала предварительной версии Azure](http://portal.azure.com/) скопируйте строку подключения для своей базы данных.

Для начала нужно подключить Visual Studio к базе данных SQL Azure **AdventureWorksLT**.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## Шаг 5. Добавление строки подключения в файл App.config


1. В Visual Studio откройте в обозревателе решений файл App.config.

2. Добавьте элемент **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;**, как показано в приведенном ниже примере кода App.config.
 - Замените заполнитель *{your\_placeholders}* собственными значениями.

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup> 
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	
		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. Сохраните файл App.config.

4. На панели обозревателя решений щелкните правой кнопкой узел **App.config** и выберите **Свойства**.

5. Для свойства **Копировать в выходной каталог** выберите значение **Копировать всегда**.
 - В результате содержимое файла App.config заменит содержимое файла &#x2a;.exe.config в каталоге сборки файла &#x2a;.exe. Файл &#x2a;.exe будет заменен при каждой повторной компиляции.
 - Файл &#x2a;.exe.config считывается при запуске примера программы C#.

	![«Копировать в выходной каталог» = «Копировать всегда»][50-VSCopyToOutputDirectoryProperty]


## Шаг 6. Вставка примера кода на C#


1. В Visual Studio на панели **обозревателя решений** откройте файл **Program.cs**. 

	![Вставьте фрагмент в код примера программы C#][40-VSProgramCsOverlay]

2. Замените весь начальный код в файле Program.cs следующим примером кода C#.
 - Чтобы сократить код, вы можете присвоить переменной **SQLConnectionString** всю строку соединения в качестве литерала. Затем можно удалить методы **GetConnectionStringFromExeConfig** и **GatherPasswordFromConsole**.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;
	
namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;
	
			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();
	
			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;
	
			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;
	
				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();
	
				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------
	
		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];
	
			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}
	
		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### Компиляция программы


1. Скомпилируйте программу в Visual Studio, последовательно щелкнув **Сборка** > **Собрать решение**.


### Краткое описание действий, выполняемых в примере программы


1. Считывание основной части строки подключения SQL из файла конфигурации.

2. Получение имени пользователя и пароля, введенных с помощью клавиатуры, и дополнение строки подключения этой информацией.

3. Использование строки подключения и классов ADO.NET для подключения к демонстрационной базе данных **AdventureWorksLT** в базе данных SQL Azure.

4. Создание инструкции SQL **SELECT** для чтения данных из таблицы **SalesLT**.

5. Вывод возвращенных строк в консоль.


Мы старались сделать пример кода на C# максимально коротким. Еще мы добавили код для чтения файла конфигурации, следуя просьбам наших пользователей. Мы согласны, что для создания качественных программ следует использовать файлы конфигурации вместо литералов, указанных в коде ЕХЕ-файла.


> [AZURE.WARNING]Чтобы сократить код в этом обучающем примере, мы решили не включать код, задающий обработку исключений и логику повторных попыток. В рабочих программах, взаимодействующих с облачными базами данных, эти компоненты обязательны.
>
> [Здесь](sql-database-develop-csharp-retry-windows.md) можно найти пример кода с логикой повторных попыток.


## Шаг 7. Добавление диапазона допустимых IP-адресов в брандмауэр сервера


Ваша клиентская программа C# не сможет подключиться к базе данных SQL Azure, пока IP-адрес клиентского компьютера не будет добавлен в брандмауэр базы данных SQL. Запуск программы завершится сбоем, и вы получите сообщение об ошибке с указанием необходимого IP-адреса.


Добавить IP-адрес можно на [портале предварительной версии Azure](http://portal.azure.com/).



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



Дополнительную информацию см. в статье <br/> [Как настроить параметры брандмауэра для базы данных SQL Azure](sql-database-configure-firewall-settings.md).



## Шаг 8. Запуск программы


1. Запустите программу из Visual Studio, выбрав **ОТЛАДКА** > **Начать отладку**. Откроется окно консоли.

2. Введите имя пользователя и пароль.
 - Для некоторых средств подключения необходимо указывать имя пользователя с суффиксом @{имя\_сервера}. В ADO.NET этот суффикс не является обязательным. Не утруждайте себя вводом суффикса.

3. Отобразятся строки данных.


## Связанные ссылки


- [Простые примеры кода клиентских приложений для работы с базой данных SQL](sql-database-develop-quick-start-client-code-samples.md)

- Если ваша клиентская программа запущена на виртуальной машине Azure, дополнительные сведения о TCP-портах, отличных от 1433, см. в статье <br/>[Порты для ADO.NET 4.5, отличные от 1433, и база данных SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=Oct15_HO3-->