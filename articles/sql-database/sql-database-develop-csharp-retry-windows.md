<properties 
	pageTitle="Пример кода: логика повторов в C# для подключения к базе данных SQL | Microsoft Azure" 
	description="Пример кода на языке C# включает надежную логику повторных попыток для взаимодействия с базой данных SQL Azure." 
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
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# Пример кода: логика повторов в C# для подключения к базе данных SQL


<!--
sql-database-develop-csharp-retry-windows.md  ,  NEW
mgm4f20150723retryfrommsdn68
. . . .
Old Titles on MSDN:  Code sample: Retry logic for connecting to Azure SQL Database with ADO.NET
.
How to: Connect to Azure SQL Database by using ADO.NET
.
ShortId on MSDN was:  ee336243.aspx
Dx  4d7936fd-341c-4a37-8796-25e385ae6c5b
-->


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Этот раздел содержит пример кода C#, демонстрирующий пользовательскую логику повторных попыток. Логика повторов предназначена для корректной обработки временных ошибок или *временных сбоев*, которые обычно исчезают, если программа ждет несколько секунд и выполняет повторную попытку.


Классы ADO.NET, используемые для подключения к локальному серверу Microsoft SQL, могут также подключаться к базе данных SQL Azure. При этом сами по себе классы ADO.NET необходимый для работы уровень надежности обеспечить не могут. Ваша клиентская программа может испытывать временные сбои, с которыми должна справляться самостоятельно, без вмешательства пользователя. Приведем несколько примеров временных сбоев.


- При подключении через Интернет могут иметь место кратковременные отказы сети, после которых соединение можно восстановить.

- Облачные вычисления требуют распределения нагрузки, при котором попытки подключения или запросы могут кратковременно блокироваться.


## Выявление временных ошибок


Программа должна отличать временные ошибки от постоянных. Если название целевой базы данных указано в программе неправильно, ошибка «База данных не найдена» будет возникать постоянно, при каждом запуске.


Список номеров ошибок, которые считаются временными сбоями, можно найти по следующей ссылке:


- [Сообщения об ошибках для клиентских программ базы данных SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - См. статью *Временные ошибки, ошибки потери подключения*.


## Пример кода C#


Пример кода C#, приведенный в этом разделе, содержит настраиваемую логику обнаружения и обхода временных ошибок.


После примера кода приводятся некоторые основные инструкции и рекомендации, которые работают независимо от используемой технологии взаимодействия с базой данных SQL Azure. Ознакомиться с общими рекомендациями можно по следующей ссылке:


- [Подключение к базе данных SQL: ссылки, рекомендации и советы по разработке](sql-database-connect-central-recommendations.md)


Пример кода C# состоит из двух файлов с расширением CS, содержимое которых будет использоваться в следующих разделах. Вот имена этих файлов:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


## Компиляция и запуск примера кода


Чтобы скомпилировать пример кода, выполните указанные ниже действия.


1. В Visual Studio создайте новый проект на основе шаблона консольного приложения C#.

2. Щелкните проект правой кнопкой мыши и добавьте CS-файл, для которого в этом разделе приводится исходный код.

3. В окне командной строки `cmd.exe` запустите программу, как показано ниже. Также показан фактический результат запуска программы.


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


Исходный код C# для CS-файлов приводится в следующих разделах.


## Файл Program.cs


Эта программа разработана таким образом, что:


- временный сбой при попытке подключения вызывает повторную попытку подключения;

- временная ошибка при выполнении команды запроса заставляет программу прервать подключение и создать новое, прежде чем выполнять команду запроса повторно.


Корпорация Майкрософт не выступает ни за, ни против этого метода. Демонстрационная программа иллюстрирует некоторые возможности разработки, доступные пользователю.


### Длина исходного кода


Своей значительной длиной файл `Program.cs` обязан, в основном, логике, которая перехватывает исключения.


В конце раздела приводится [укороченная версия](#shorter_program_cs) файла `Program.cs` без использования логики повторов и обработки `Exception`.


### Стек вызовов


Метод `Main` находится в файле `Program.cs`. Стек вызовов работает следующим образом.


1. `Main` вызывает `ConnectAndQuery`.

2. `ConnectAndQuery` вызывает `EstablishConnection`.

3. `EstablishConnection` вызывает `IssueQueryCommand`.


### Исходный код файла Program.cs


	using     System;  // C#, pure ADO.NET, no Enterprise Library.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using T = System.Threading;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        // Fields, shared among methods.
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        /// <summary>
	        /// Prepares values for a connection. Then inside a loop, it calls a method
	        /// that opens a connection. The called method calls yet another method
	        /// that issues a query.
	        /// The loop reiterates only if a transient error is encountered.
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
	            int maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
	            int secondsBetweenRetries = 4;  // Simple retry strategy.
	
	            // [A.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = connectionTimeoutSeconds;
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                try
	                {
	                    // [A.2] Connect, which proceeds to issue a query command.
	                    this.EstablishConnection();
	
	                    // [A.3] All has gone well, so let the program end.
	                    break;
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientError;
	
	                    // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
	                    isTransientError = Custom_SqlDatabaseTransientErrorDetectionStrategy
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientError == false)  // Is a persistent error...
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number=={0}.  Will terminate.",
	                            sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
	                        // Break the loop, let the hopeless program end.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient fault from an attempt to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new query
	                    // attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number=={0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught in Main. Will terminate.");
	
	                    // [A.7] The program must end, so re-throw the unrecognized error.
	                    throw exc;
	                }
	
	                // [A.8] Throw an application exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum count of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	                // Else, can retry.
	
	                // A very simple retry strategy, a brief pause before looping.
	                T.Thread.Sleep(1000 * secondsBetweenRetries);
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        /// <summary>
	        /// Open a connection, then call a method that issues a query.
	        /// </summary>
	        void EstablishConnection()
	        {
	            try
	            {
	                // [B.1] The 'using' statement will .Dispose() the connection.
	                // If you are working with a connection pool, you might want instead
	                // to merely .Close() the connection.
	                using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	                {
	                    // [B.2] Open a connection.
	                    sqlConnection.Open();
	                    // [B.3]
	                    this.IssueQueryCommand();
	                }
	            }
	            catch (Exception exc)
	            {
	                // [B.4] This re-throw means we discard the connection whenever
	                // any error occurs during query command, even for a transient error.
	                throw exc;  // [B.5] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        /// <summary>
	        /// Issue a query, then write the result rows to the console.
	        /// </summary>
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            try
	            {
	                // [C.1] Use the connection to create a query command.
	                using (dbCommand = this.sqlConnection.CreateCommand())
	                {
	                    dbCommand.CommandText =
	                        @"SELECT TOP 3
	                              ob.name,
	                              CAST(ob.object_id as nvarchar(32)) as [object_id]
	                          FROM sys.objects as ob
	                          WHERE ob.type='IT'
	                          ORDER BY ob.name;";
	
	                    // [C.2] Issue the query command through the connection.
	                    using (dReader = dbCommand.ExecuteReader())
	                    {
	                        // [C.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	    } // class Program
	}


## Файл кода Custom\_SqlDatabaseTransientErrorDetectionStrategy.cs


[`SqlDatabaseTransientErrorDetectionStrategy`](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.sqldatabasetransienterrordetectionstrategy.aspx) — это класс API Enterprise Library (EntLib). В данном примере кода используется указанный ниже пользовательский класс, основанный на той же идее, что и класс EntLib.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method happens to match ITransientErrorDetectionStrategy of EntLib60.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613 };
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


<a id="shorter_program_cs" name="shorter_program_cs"></a>


&nbsp;


## Сокращенная версия файла Program.cs


Исходный код в этом разделе является сокращенной версией более длинного файла `Program.cs`, который приводится выше. Вся логика повторных попыток и обработка исключений удалены.


Сокращенная версия позволяет упростить отображение вызовов ADO.NET, зная, что обычно они работают. Обычно временные сбои не возникают, а исключения отсутствуют. Точно так же парашютисту обычно не требуется запасной парашют.


	using     System;  // C#, pure ADO.NET, no retry logic, no Exception handling.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1_dn864744
	{
	    class Program
	    {
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.EstablishConnection();
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	            {
	                sqlConnection.Open();
	                this.IssueQueryCommand();
	            }
	        } // method EstablishConnection
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            using (dbCommand = this.sqlConnection.CreateCommand())
	            {
	                dbCommand.CommandText =
	                    @"SELECT TOP 3 ob.name, CAST(ob.object_id as nvarchar(32)) as [object_id]
	                        FROM sys.objects as ob
	                        WHERE ob.type='IT'
	                        ORDER BY ob.name;";
	
	                using (dReader = dbCommand.ExecuteReader())
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	    } // class Program
	}


## Связанные ссылки


- [Простые примеры кода клиентских приложений для работы с Базой данных SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->