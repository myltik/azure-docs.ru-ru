<properties
	pageTitle="Логика повторных попыток в C# для работы с базой данных SQL | Microsoft Azure"
	description="Пример кода на языке C# включает логику повторных попыток для надежного взаимодействия с Базой данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="annemill"/>


# Пример кода: логика повторов в C\# для подключения к базе данных SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



Этот раздел содержит пример кода C\#, демонстрирующий пользовательскую логику повторных попыток. Логика повторных попыток предназначена для корректной обработки временных ошибок или *временных сбоев*, которые обычно устраняются, если программа ожидает несколько секунд, а затем выполняет повторную попытку.


Классы ADO.NET, используемые для подключения к локальному серверу Microsoft SQL Server, могут также подключаться к базе данных SQL Azure. При этом сами по себе классы ADO.NET необходимый для работы уровень надежности обеспечить не могут. Ваша клиентская программа может испытывать временные сбои, с которыми она должна справляться самостоятельно, продолжая работу без вмешательства пользователя.


Приведем несколько примеров временных сбоев.


- При подключении через Интернет могут иметь место кратковременные отказы сети, после которых соединение можно восстановить.
- Облачные вычисления требуют распределения нагрузки, при котором попытки подключения или запросы могут кратковременно блокироваться.


## О. Выявление временных ошибок


Программа должна отличать временные ошибки от постоянных. Если название целевой базы данных указано в программе неправильно, ошибка «База данных не найдена» будет возникать постоянно, при каждом запуске.

Список номеров ошибок, которые считаются временными сбоями, можно найти по следующей ссылке:

- [Сообщения об ошибках для клиентских программ базы данных SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - См. верхний раздел *Временные ошибки, ошибки потери подключения*.


В примере кода C\# далее в этом разделе перечислены номера временных ошибок в поле с именем **TransientErrorNumbers**.



## B. Пример кода C\#


Пример кода C\#, приведенный в этом разделе, содержит настраиваемую логику обнаружения и обхода временных ошибок. В том примере предполагается, что установлена среда .NET Framework 4.5.1 или более поздняя версия.


После примера кода приводятся некоторые основные инструкции и рекомендации, которые работают независимо от используемой технологии взаимодействия с базой данных SQL Azure. Ознакомиться с общими рекомендациями можно по следующей ссылке:


- [Подключение к базе данных SQL: ссылки, рекомендации и советы по разработке](sql-database-connect-central-recommendations.md)


Пример кода C\# состоит из одного файла с именем Program.cs. Этот код приведен в следующем разделе.


### В.1 Запись и компиляция примера кода


Чтобы скомпилировать пример кода, выполните указанные ниже действия.


1. В [бесплатном выпуске Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs) создайте новый проект на основе шаблона консольного приложения C\#.
  - Выберите Файл \> Создать \> Проект \> Установленные\> Шаблоны \> Visual C\# \> Windows \> Классический рабочий стол\> Консольное приложение.
  - Присвойте проекту имя **RetryAdo2**.
2. Откройте панель обозревателя решений.
  - Найдите имя своего проекта.
  - Найдите имя файла Program.cs.
3. Откройте файл Program.cs.
4. Замените все содержимое файла Program.cs приведенным ниже кодом.
5. Щелкните меню «Сборка» \> «Собрать решение».


#### Исходный код C\# для вставки


Вставьте этот код в ваш файл **Program.cs**.


После этого следует изменить строки для имени сервера, пароля и т. д. Эти строки вы можете найти в методе с именем **GetSqlConnectionStringBuilder**.



```
using System;   // C#
using G = System.Collections.Generic;
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C. Запуск программы


Исполняемый файл **RetryAdo2.exe** не принимает никаких параметров. Чтобы запустить ЕХЕ-файл:

1. откройте окно консоли — там, где была выполнена компиляция двоичного файла RetryAdo2.exe;
2. запустите файл RetryAdo2.exe без входных параметров.



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D. Способы тестирования логики повторных попыток

Есть много разных способов имитации временных ошибок для тестирования логики повторных попыток.


### D.1 Вывод тестового исключения

Пример кода включает:

- Небольшой второй класс с именем **TestSqlException**, имеющий свойство с именем **Number**.
- Строка `//throw new TestSqlException(4060);`, которую можно раскомментировать.

Если раскомментировать строку throw и затем выполнить повторную компиляцию, при следующем выполнении файла **RetryAdo2.exe** вывод будет примерно следующим.



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug\]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug\]
>>
```



#### Повторное тестирование с постоянными ошибками


Чтобы убедиться, что код корректно обрабатывает постоянные ошибки, повторно выполните предыдущий тест, но не используйте номер реальной временной ошибки \(например, 4060\). Вместо этого используйте любой другой номер \(например, 7654321\). Программа должна обработать этот номер как номер постоянной ошибки, пропуская все повторные попытки.



### D.2 Отключение от сети

1. Отключите клиентский компьютер от сети.
  - В случае с настольным компьютером отключите сетевой кабель.
  - В случае с ноутбуком нажмите функциональное сочетание клавиш для отключения сетевого адаптера.
2. Запустите файл RetryAdo2.exe и дождитесь, пока в консоли отобразится первая временная ошибка \(скорее всего, 11001\).
3. В ходе выполнения файла RetryAdo2.exe повторно подключитесь к сети.
4. Просмотрите отчет консоли об успешном выполнении последующих повторных попыток.


### D.3 Временная ошибка в имени сервера

1. Временно добавьте 40615 в поле **TransientErrorNumbers** в качестве другого номера ошибки, а затем выполните повторную компиляцию.
2. Задайте точку останова строке: `new S.SqlConnectionStringBuilder()`.
3. Используйте параметр *Изменить и продолжить*, чтобы специально исказить имя сервера несколькими строками ниже.
  - Запустите программу и вернитесь к точке останова.
  - Произойдет ошибка 40615.
4. Исправьте ошибку в имени.
5. Запустите программу и дождитесь успешного завершения процедуры.
6. Удалите 40615 и выполните повторную компиляцию.


## E. Связанные ссылки

- [Простые примеры кода клиентских приложений для работы с базой данных SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Руководство по базам данных SQL. Создание Базы данных SQL с помощью C\# и библиотеки Базы данных SQL для .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->