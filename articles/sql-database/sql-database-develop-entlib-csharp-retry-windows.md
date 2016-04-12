<properties
	pageTitle="Логика повторов в EntLib для подключения к Базе данных SQL | Microsoft Azure"
	description="Библиотека Enterprise Library упрощает решение ряда задач для клиентских программ облачных служб, включая интеграцию логики повторов при временных сбоях."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# Образец кода: логика повторных попыток из библиотеки Enterprise Library 6 в C&#x23; для подключения к Базе данных SQL

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этом разделе представлен полный образец кода, демонстрирующий Enterprise Library (EntLib). EntLib упрощает многие задачи для клиентских программ, которые взаимодействуют с облачными службами, такими как База данных SQL Azure. В нашем примере решается важная задача по включению логики повторов для временных сбоев.


Классы EntLib предназначены для различения двух категорий ошибок среды выполнения:

- ошибки, которые не устраняются без вмешательства пользователя, такие как неправильное написание имени сервера;
- временные сбои, такие как задержка приема новых подключений со стороны сервера, при сбалансированной системной нагрузке Azure.


Enterprise Library 6 (EntLib60) является последней версией и была выпущена в апреле 2013 года.

- Корпорация Майкрософт сделала исходный код общедоступным.
- Корпорация Майкрософт не планирует дальнейшую поддержку исходного кода.


## Предварительные требования


#### .NET Framework 4.0 или более поздней версии


Необходимо установить Microsoft .NET Framework версии 4.0 или более поздней версии. Рекомендуется установка последней версии — на момент написания этой статьи доступна версия 4.6.


#### Visual Studio Community Edition, предоставляется бесплатно


Вам нужен способ компиляции исходного кода из этого примера. Один из вариантов — установить [бесплатную программу Microsoft Visual Studio *Community* Edition](http://www.visualstudio.com/products/free-developer-offers-vs.aspx).


Может потребоваться регистрация адреса электронной почты в MSDN. Порядок действий выглядит следующим образом.


1. [Зайдите в MSDN](http://msdn.microsoft.com/).
2. Щелкните **подписки MSDN** вверху.
3. Щелкните **Зарегистрируйтесь сейчас**.
4. Заполните форму, указав свои данные.
5. Щелкните **Создать учетную запись** внизу.


#### Enterprise Library 6 (EntLib60)


Способы установки EntLib60


- Воспользуйтесь функцией диспетчера пакетов *NuGet* в Visual Studio.
 - В NuGet выполните поиск по запросу **enterpriselibrary**.


- В [разделе документации по EntLib60](http://msdn.microsoft.com/library/dn169621.aspx) найдите строку **Загрузки** и щелкните [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898), чтобы загрузить двоичные DLL-файлы сборки.


EntLib60 включает несколько DLL-файлов сборки, имена которых начинаются с одного и того же префикса **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**, но для кода в данном примере нужны только следующие две сборки:

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## Как взаимодействуют классы EntLib


Классы EntLib используются для создания других классов EntLib. В данном примере последовательность создания и использования классов выглядит следующим образом:


1. cоздание объекта **ExponentialBackoff**;
2. создание объекта **SqlDatabaseTransientErrorDetectionStrategy**;
3. создание объекта **RetryPolicy**. Входные параметры:
 - объект **ExponentialBackoff**;
 - объект **SqlDatabaseTransientErrorDetectionStrategy**.
4. объект **ReliableSqlConnection**. Входные параметры:
 - объект **Строка** с именем сервера и другими сведениями о подключении.
 - Объект **RetryPolicy**.
5. Вызов подключения, выполненный с помощью метода **RetryPolicy .ExecuteAction**.
6. Вызов метода **ReliableSqlConnection .CreateCommand**.
 - Возвращение объекта **System.SqlClient.Data.DbCommand**, который является частью ADO.NET.
7. Вызов запроса, выполненный с помощью метода **RetryPolicy .ExecuteAction**.


## Компиляция и запуск примера кода


Пример исходного кода файла Program.cs приводится ниже. Чтобы скомпилировать и запустить пример кода, выполните указанные ниже действия.


1. В Visual Studio создайте новый проект на основе шаблона консольного приложения C#.

2. На панели обозревателя решений измените почти пустой файл Program.cs, заменив его начальное содержимое кодом Program.cs, который приводится ниже.

3. Для компиляции проекта последовательно выберите меню "Сборка" > "Построить решение".

4. В окне командной строки cmd.exe запустите программу, как показано ниже. Также показан фактический результат запуска программы.


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Исходный код файла Program.cs


Весь исходный код для данного примера EntLib содержится в следующем файле Program.cs:


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## Связанные ссылки


- [Enterprise Library 6 — апрель 2013 г.](http://msdn.microsoft.com/library/dn169621.aspx) — многочисленные ссылки на дополнительную информацию.
 - Если вы хотите увидеть исходный код, нажмите кнопку, предлагающую [загрузить исходный код EntLib60](http://go.microsoft.com/fwlink/p/?LinkID=290898), вверху раздела.


- Бесплатная электронная книга в формате PDF от корпорации Майкрософт — [руководство разработчика по Microsoft Enterprise Library, 2-е издание](http://www.microsoft.com/download/details.aspx?id=41145).


- [Пространство имен Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Справочник по библиотеке классов Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)


- [Пример кода: логика повторных попыток в C# для подключения к базе данных SQL с использованием ADO.NET](sql-database-develop-csharp-retry-windows.md)


- [Простые примеры кода клиентских приложений для работы с базой данных SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_0316_2016-->