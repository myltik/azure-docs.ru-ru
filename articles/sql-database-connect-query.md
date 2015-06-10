
<properties 
	pageTitle="Подключение и выполнение запросов к базе данных SQL с помощью языка C#" 
	description="Пример кода, позволяющий использовать C# и ADO.NET для подключения к и взаимодействия с базой данных AdventureWorks в базе данных SQL Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="ckarst" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="cakarst"/>


# Подключение и выполнение запросов к базе данных SQL с помощью языка C# 

В этой статье приведен пример кода на C#, показывающий, как подключиться к существующей базе данных SQL AdventureWorks, используя ADO.NET. Код из примера компилируется в консольное приложение, которое отправляет запрос к базе данных и выводит результаты.


## Предварительные требования


- Существующая база данных AdventureWorks в базе данных SQL Azure. [Создать базу данных за считанные минуты](sql-database-get-started.md).
- [Visual Studio с .NET Framework](https://www.visualstudio.com/ru-ru/visual-studio-homepage-vs.aspx).


## Шаг 1. Консольное приложение


1. С помощью Visual Studio создайте консольное приложение C#.


![Подключение и запросы](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## Шаг 2. Пример кода SQL


1. Скопируйте и вставьте приведенный ниже код в консольное приложение.


> [AZURE.WARNING]Мы специально сделали его максимально коротким, чтобы его было проще понять. Он не предназначен для использования в рабочей среде.


Этот код не предназначен для рабочей среды. Если вы хотите получить код, который можно использовать в реальных условиях, рекомендуем уделить внимание следующим темам:


- Обработка исключений.
- Повтор логики для временных ошибок.
- Защищенное хранение паролей в файле конфигурации.



### Пример исходного кода на C#


Вставьте этот исходный код в ваш файл **Program.cs**.


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;
	
	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## Шаг 3. Получение строки подключения к базе данных


1. Откройте [портал Azure](http://portal.azure.com/).
2. Щелкните **Обзор** > **Базы данных SQL** > **База данных Adventure Works** > **Свойства** > **Показать строки подключения к базе данных**.


![Портал](.\media\sql-database-connect-query\ConnectandQuery_portal.png)


В колонке строк подключения к базе данных вы увидите соответствующие строки подключения для ADO.NET, ODBC, PHP и JDBC.


## Шаг 4. Вставка фактической информации для подключения


Во вставленном исходном коде замените *[Your_Connection_String]* на строку подключения, а *your_password_here* в ней — на действительный пароль.


## Шаг 5. Выполнение приложения


1. Чтобы скомпилировать и запустить приложение, щелкните **ОТЛАДКА** > **Начать отладку**.
2. Программа выведет результаты запроса в окне консоли.

<!---HONumber=58-->