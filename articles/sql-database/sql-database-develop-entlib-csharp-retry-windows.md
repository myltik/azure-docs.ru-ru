<properties 
	pageTitle="Образец кода: логика повторных попыток из библиотеки Enterprise Library в C# для подключения к базе данных SQL | Microsoft Azure"
	description="Enterprise Library призвана упростить задачу включения логики повторных попыток в клиентские программы, которые получают доступ к облачным службам."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# Образец кода: логика повторный попыток из библиотеки Enterprise Library в C&#x23; для подключения к базе данных SQL


В этом разделе представлен полный образец кода, демонстрирующий Enterprise Library (EntLib). EntLib облегчает программисту задачу включения логики повторных попыток в клиент, который взаимодействует с облачной службой базы данных SQL Microsoft Azure.


Enterprise Library 6 (EntLib60) является последней версией.


Чтобы получить более подробные сведения о EntLib, рекомендуется посетить следующие ссылки:


- [Enterprise Library 6 — апрель 2013 г.](http://msdn.microsoft.com/library/dn169621.aspx)<br/> — многочисленные ссылки на дополнительную информацию.

- Бесплатная электронная книга в формате PDF от корпорации Майкрософт<br/>[ — руководство разработчика по Microsoft Enterprise Library, 2-е издание](http://www.microsoft.com/download/details.aspx?id=41145).


## Предварительные требования


EntLib не включена при установке Visual Studio и Microsoft .NET Framework. Ее необходимо загрузить отдельно.


Система **NuGet** с помощью Visual Studio упрощает загрузку. Каталог с именем packages\\ сохраняется в том же каталоге, где хранится SLN-файл решения Visual Studio.


DLL-файлы сборки EntLib устанавливаются в подкаталоге. Два файла сборки именуются следующим образом:


- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.dll`
- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.Data.dll`


## Файлы кода C# с пояснениями


Пример кода C# состоит из трех файлов с расширением CS, содержимое которых будет использоваться в следующих разделах. Вот имена этих файлов:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`
- `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


### Более короткий `Program.cs` без `try/catch`


Другой предоставляемый CS-файл — гораздо более короткая версия `Program.cs`. Весь код `try/catch` был удален. Это позволяет более наглядно показать вызовы EntLib.



### Тестовый CS-файл


Тестировать логику повторных попыток достаточно сложно, поскольку отсутствует очевидный способ вызывать настоящую временную ошибку. Одно из решений этой проблемы — использовать для тестирования временный код, чтобы:


1. вызвать искусственную временную ошибку;
2. устранить причину временных ошибок;
3. повторить попытку подключения или запроса с ожидаемым успешным завершением.


Чтобы помочь вам с тестированием, предоставлен другой CS-файл, который можно использовать в качестве временной замены:


- `Test2_TransientErrorDetectionStrategy`
 - Временная замена для `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`.


Места в коде, где необходимо внести изменения для этого тестирования, помечены комментарием, который содержит строку `TEST.PASSWORD_FIX`.


## Компиляция и запуск примера кода


Чтобы скомпилировать пример кода, выполните указанные ниже действия.


1. В Visual Studio создайте новый проект на основе шаблона консольного приложения C#.

2. Щелкните проект правой кнопкой мыши и добавьте CS-файлы, для которых в этом разделе приводится исходный код.

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


Следующий файл кода Program.cs длинный, так как он содержит блоки `try/catch`, которые выполняются только в том случае, если произошла ошибка. В конце раздела представлена намного более короткая версия `Program.cs`, где удалены все строки `try/catch`.


Метод `Main` находится в файле `Program.cs`. Стек вызовов работает следующим образом.


1. `Main` вызывает `ConnectAndQuery`.

2. `ConnectAndQuery` вызывает `InitializeFields`.

3. `ConnectAndQuery` вызывает `EstablishConnection`.

4. `EstablishConnection` вызывает `IssueQueryCommand`.


&nbsp;


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1 
	{
	    /// <summary>
	    /// Demo sample C# program that uses Microsoft's Enterprise Library 6 to robustly
	    /// connect to and interact with Azure SQL Database.
	    /// .
	    /// The sample chooses a strategy of discarding the Sql Connection whenever
	    /// a Sql Command fails, even for a transient reason. The 'for' loop
	    /// manages this choice. We are neither recommending nor disrecommending
	    /// this particular choice, just demonstrating flexibility.
	    /// .
	    /// For an optional test mechanism manually built into this program, find all:
	    /// 'TEST.PASSWORD_FIX' (related to connection).
	    /// </summary>
	    class Program
	    {
	        // const fields, so that SqlException objects thrown from Sql Commands
	        // are tested for transience by our custom logic.
	        private const string M_progressCreatingConnection = "CreatingConnection";
	        private const string M_progressIssuingCommand     = "IssuingCommand";
	
	        // Fields, shared among methods.
	        private string progressLocation;
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        // Also consider E.FixedInterval or E.Incremental.
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	
	        // TEST.PASSWORD_FIX.  Replace with Test2_TransientErrorDetectionStrategy.
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	
	        /// <summary>
	        /// Called from Main.
	        /// Calls a method to establish a connection, which calls
	        /// another method to issue a query through the connection. 
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int maxCountTriesConnectAndQuery = 3;  // Adjustable.
	
	            this.InitializeFields();
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	            // Next is a call .ExecuteAction to invoke the method EstablishConnection.
	            // Method EstablishConnection then calls another .ExecuteAction to
	            //     invoke the method IssueQueryCommand.
	            // Then control is returned to this method.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                if (cc >= 2) { Console.WriteLine("-- Another iteration of outer loop, cc=={0}. --", cc); }
	
	                try
	                {
	                    // [A.1] Connect, which proceeds to issue a query command.
	                    this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	
	                    break; // [A.2] All has gone well, so let the program end.
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientErrorNumber = false;
	
	                    // [A.3] Call our collection of transient error numbers.
	                    isTransientErrorNumber =
	                        Custom_SqlDatabaseTransientErrorDetectionStrategy // TEST.PASSWORD_FIX.
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientErrorNumber == false)
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number = {0}."
	                            + "  Will terminate.", sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.4] Either the connection attempt or the query command attempt
	                        //     suffered a persistent SqlException.
	                        // Break the query command loop, let the hopeless program end.
	                        break;
	                    }
	
	                    if (this.progressLocation == M_progressCreatingConnection)
	                    {
	                        // [A.5] The EntLib retry mechanisms already gave the Sql Connection
	                        //     enough retries, so this block will not give connection any more tries.
	                        // This block gives retries to certain Sql Command only.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient error from an attempt
	                    //     to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new
	                    // query attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number = {0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught.  Will terminate.");
	                    throw exc; // [A.7] The program must end, so re-throw the unrecognized exception.
	                }
	
	
	                // [A.8] Throw an exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum of retries.
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
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        void EstablishConnection()
	        {
	            try
	            {
	                // [C.1]
	                using ( this.rsConnection = new E.ReliableSqlConnection(
	                    this.scsBuilder.ToString(),
	                    this.connectionRetryPolicy,
	                    this.commandRetryPolicy )
	                    )
	                {
	                    this.progressLocation = M_progressCreatingConnection;
	                    this.rsConnection.Open(); // [C.2] Open the newly constructed reliable connection.
	
	                    // [C.3] Prepare delegate, then issue a query command.
	                    this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	                }
	            }
	            // TEST.PASSWORD_FIX.
	                // Step_1: In InitializeFields(), set password to a wrong value.
	                // Step_2: Uncomment the following 'catch' of sqlExc.
	            //catch (C.SqlException sqlExc)  // For TESTING only.  Usually comment out this one CATCH.
	            //{
	            //    if (sqlExc.Number == 18456)  // Could mean wrong password, could not connect.
	            //    {
	            //        this.scsBuilder["Password"] = "MyCorrectPassword";
	            //        Console.WriteLine("Testing.  Password now fixed in catch.");
	            //    }
	            //    throw sqlExc;
	            //}
	            catch (Exception exc)
	            {
	                throw exc;  // [C.4] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            try
	            {
	                // [D.1] Use the connection to create a query command.
	                using (dbCommand = this.rsConnection.CreateCommand())
	                {
	                    dbCommand.CommandText = this.sqlSelectCode;
	
	                    // [D.2] Issue the query command through the connection.
	                    this.progressLocation = M_progressIssuingCommand;
	                    using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                    {
	                        Console.WriteLine();
	                        // [D.3] Loop through all returned rows, writing the data to the console.
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
	                throw exc; // [D.4] Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	
	
	        void InitializeFields()
	        {
	            // [B.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyCorrectPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30; // Default is 15 seconds, too brief over Internet!
	
	            // [B.2] Prepare the Transact-SQL select statement.
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            // [B.3] Begin steps to create a retry policy for connecting.
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,                               // Maximum number of times to retry.
	                TimeSpan.FromMilliseconds(2000), // Minimum interval between retries.
	                TimeSpan.FromMilliseconds(8000), // Maximum interval between retries.
	                TimeSpan.FromMilliseconds(2000)  // Factor for random differences in interval between retries.
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();// TEST.PASSWORD_FIX.
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	
	            return;
	        } // method InitializeFields
	    } // class Program
	}


&nbsp;


## Файл `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


EntLib60 имеет класс с именем `ReliableSqlConnection`. Можно управлять тем, как экземпляр подключения решает, является ли исключение временной ошибкой, назначив ему класс, реализующий `ITransientErrorDetectionStrategy`.

EntLib60 предлагает класс `SqlDatabaseTransientErrorDetectionStrategy`. Но в этом разделе мы решили реализовать и использовать собственный пользовательский класс `Custom_SqlDatabaseTransientErrorDetectionStrategy`. Наш пользовательский класс имеет белый список значений для сравнения с `SqlException.Number`.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method is required by ITransientErrorDetectionStrategy.
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
	                {4060, 10928, 10929, 40197, 40501, 40613};
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


&nbsp;


## Файл `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


Пользовательский класс, реализующий `ITransientErrorDetectionStrategy`, обеспечивает наш выбор никогда не разрешать EntLib60 рассматривать какую-либо ошибку команды запроса лишь как временную. Вместо этого программа в целом предназначена для оценки `SqlException.Number` с помощью собственной пользовательской логики.


С помощью описанного в этом разделе класса мы реализовали свой выбор: наша программа удаляет и заново создает новое подключение перед повторной попыткой выполнения любой закончившейся ошибкой команды — как временной, так и нет.


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// Is a substitute for class E.SqlCommandTransientErrorDetectionStrategy.
	    /// Choosing to always discard the Sql Connection whenever the command fails,
	    /// regardless of whether command failure was transient.
	    /// Program try/catch and for-loop logic prefers to discard then re-construct
	    /// a new Sql Connection.
	    /// </summary>
	    public class ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            return false;  // Force a failure, even if is transient.  No retry of sql command.
	        }
	    }
	}


&nbsp;


## Тестирование логики повторных попыток


Тестировать программу EntLib60 довольно сложно. Трудно вызвать действительно временную ошибку и контролировать ее длительность. Этот демонстрационный код включает простой метод, который можно использовать для имитации временных ошибок.


Способ состоит в использовании нашего пользовательского детекторного класса `Test2_TransientErrorDetectionStrategy` вместо класса `Custom_SqlTransientErrorDetectionStrategy`. Таким образом можно заставить `ReliableSqlConnection` в EntLib60 оценивать *все* вхождения `SqlException` как временные. Хитрость в том, чтобы заставить программу самостоятельно исправить проблему перед первой повторной попыткой.


### Прием с ошибочным паролем


Проще всего начать с ошибочного значения пароля для подключения. Если это вызывает `SqlException` с `sqlExc.Number==18456`, программа должна исправить пароль. При повторной попытке программа завершается успешно.


Чтобы реализовать этот тест в демонстрационной программе, найдите в исходном коде все вхождения `TEST.PASSWORD_FIX`. Обнаружив все вхождения `TEST.PASSWORD_FIX` и внеся изменения, описанные в комментарии к каждому расположению, вы внесете следующие изменения:


- В нормальном коде в методе `InitializeFields` измените значение пароля на неправильное.

- В коде с комментариями `//` в методе `EstablishConnection` есть целый блок `catch`...<br/>`//catch (SDSqlC.SqlException sqlExc)`<br/>, который закомментирован. Раскомментируйте весь блок.

- В раскомментированном блоке `catch` присвойте правильное значение пароля.

- Там, где на класс `Custom_TransientErrorDetectionStrategy` есть ссылка в коде, замените ссылку на `Test2_TransientErrorDetectionStrategy`. Имя переменной можно сохранить то же.


### Файл `Test2_TransientErrorDetectionStrategy.cs`


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// For testing, is a substitute for class E.SqlDatabaseTransientErrorDetectionStrategy.
	    /// Change the true/false assignment to returnValue for different tests.
	    /// </summary>
	    public class Test2_TransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            bool returnValue = Test2_TransientErrorDetectionStrategy.IsTransientStatic(exc);
	            Console.WriteLine("Inside Test2_TransientErrorDetectionStrategy .IsTransientStatic,"
	                + " returning {0}.", returnValue);
	            return returnValue;
	        }
	        static public bool IsTransientStatic(Exception exc)
	        {
	            return true;  //false;
	        }
	    }
	}


&nbsp;


## Более короткий `Program.cs` без `try/catch`


Более короткий код в этом разделе компилируется и при запуске обычно работает. Но более короткий код не предназначен для запуска в производственной среде.


Единственной целью более короткого образца `Program.cs` является облегчение рассмотрения вызовов EntLib в реальном `Program.cs`. Удаление всех блоков `try/catch` позволяет более четко увидеть вызовы EntLib.


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1_Shorter_No_TryCatch
	{
	    class Program
	    {
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.InitializeFields();
	            this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using ( this.rsConnection = new E.ReliableSqlConnection(
	                this.scsBuilder.ToString(),
	                this.connectionRetryPolicy,
	                this.commandRetryPolicy )
	                    )
	            {
	                this.rsConnection.Open();
	                this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	            }
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            using (dbCommand = this.rsConnection.CreateCommand())
	            {
	                dbCommand.CommandText = this.sqlSelectCode;
	                using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
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
	
	        void InitializeFields()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,
	                TimeSpan.FromMilliseconds(2000),
	                TimeSpan.FromMilliseconds(8000),
	                TimeSpan.FromMilliseconds(2000)
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	        } // method InitializeFields
	    } // class Program
	}


## Связанные ссылки


- [Пространство имен Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)

- [Библиотека классов Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)

- [Пример кода: логика повторных попыток в C# для подключения к базе данных SQL с использованием ADO.NET](sql-database-develop-csharp-retry-windows.md)

- [Простые примеры кода клиентских приложений для работы с Базой данных SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->