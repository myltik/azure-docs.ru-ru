<properties 
	pageTitle="Логика повторов в C# для подключения к Базе данных SQL | Microsoft Azure" 
	description="Пример кода на языке C# включает логику повторных попыток для надежного взаимодействия с Базой данных SQL Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="genemi"/>


# Пример кода: логика повторов в C# для подключения к базе данных SQL


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Этот раздел содержит пример кода C#, демонстрирующий пользовательскую логику повторных попыток. Логика повторов предназначена для корректной обработки временных ошибок или *временных сбоев*, которые обычно исчезают, если программа ждет несколько секунд и выполняет повторную попытку.


Классы ADO.NET, используемые для подключения к локальному серверу Microsoft SQL, могут также подключаться к базе данных SQL Azure. При этом сами по себе классы ADO.NET необходимый для работы уровень надежности обеспечить не могут. Ваша клиентская программа может испытывать временные сбои, с которыми должна справляться самостоятельно, без вмешательства пользователя.


Приведем несколько примеров временных сбоев.


- При подключении через Интернет могут иметь место кратковременные отказы сети, после которых соединение можно восстановить.

- Облачные вычисления требуют распределения нагрузки, при котором попытки подключения или запросы могут кратковременно блокироваться.


## Выявление временных ошибок


Программа должна отличать временные ошибки от постоянных. Если название целевой базы данных указано в программе неправильно, ошибка «База данных не найдена» будет возникать постоянно, при каждом запуске.


Список номеров ошибок, которые считаются временными сбоями, можно найти по следующей ссылке:


- [Сообщения об ошибках для клиентских программ базы данных SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - См. верхний раздел *Временные ошибки, ошибки потери подключения*.


## Пример кода C#


Пример кода C#, приведенный в этом разделе, содержит настраиваемую логику обнаружения и обхода временных ошибок.


После примера кода приводятся некоторые основные инструкции и рекомендации, которые работают независимо от используемой технологии взаимодействия с базой данных SQL Azure. Ознакомиться с общими рекомендациями можно по следующей ссылке:


- [Подключение к базе данных SQL: ссылки, рекомендации и советы по разработке](sql-database-connect-central-recommendations.md)


Пример кода C# состоит из одного файла с именем Program.cs. Его код приведен в следующем разделе.


### Запись и компиляция примера кода


Чтобы скомпилировать пример кода, выполните указанные ниже действия.


1. В [бесплатном выпуске Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs) создайте новый проект на основе шаблона консольного приложения C#.
 - Выберите Файл > Создать > Проект > Установленные> Шаблоны > Visual C# > Windows > Классический рабочий стол> Консольное приложение.
 - Присвойте проекту имя **RetryAdo2**.

2. Откройте панель обозревателя решений.
 - Найдите имя своего проекта.
 - Найдите имя файла Program.cs.

3. Откройте файл Program.cs.

4. Замените все содержимое файла Program.cs приведенным ниже кодом.

5. Щелкните меню «Сборка» > «Собрать решение».


#### Исходный код C# для вставки


Вставьте этот код в ваш файл **Program.cs**.


После этого следует изменить строки для имени сервера, пароля и т. д. Эти строки можно найти в методе с именем **GetSqlConnectionStringBuilder**.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			Program program = new Program();

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}

					program.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### Запуск программы


Исполняемый файл **RetryAdo2.exe** не принимает никаких параметров. Для запуска исполняемого файла в Visual Studio выполните следующие действия:


1. Установите точку останова на операторе **return;** метода **Main**.

2. Нажмите кнопку запуска с зеленой стрелкой. Откроется окно консоли.

3. Когда отладчик останавливается в конце **Main**, переключитесь в окно консоли.

4. Найдите три строки, аналогичные следующим:


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## Тестирование логики повторных попыток


Один из удобных способов проверки логики повторных попыток заключается в следующем:


1. Временно добавьте **11001** в свою коллекцию значений **SqlConnection.Number**, которые должны рассматриваться как временные ошибки.

2. Выполните повторную компиляцию программы.

3. Отключите клиентский компьютер от сети.

4. Запустите программу в отладчике с точкой останова в цикле.
 - Первый цикл завершится сбоем с ошибкой 11001.

5. Пока программа ожидает в точке останова во время второго цикла, снова подключите компьютер к сети.

6. Продолжите выполнение программы. Во втором цикле она выполняется успешно.


### Другой вариант проверки


Кроме того, можно добавить в программу логику, распознающую значение параметра командной строки test. Реагируя на данный параметр, программа выполняет следующие действия:


1. Временно добавляет посторонние буквы, чтобы сделать написание имени сервера Базы данных SQL неправильным.

2. Временно добавляет **40615** в список временных ошибок.

3. В начале второго цикла, который представляет собой первую повторную попытку, программа выполняет следующие действия:
 - Устраняет ошибку в написании имени сервера.
 - Удаляет 40615 из списка временных ошибок.


Запустите программу с параметром test и убедитесь, что сначала в ней происходит сбой, но на втором цикле она успешно выполняется.


## Связанные ссылки


- [Простые примеры кода клиентских приложений для работы с базой данных SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO4-->