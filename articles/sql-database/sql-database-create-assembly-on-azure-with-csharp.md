<properties 
	pageTitle="Инструкция CREATE ASSEMBLY в базе данных Azure SQL с использованием CSharp"
	description="В этой статье приведен исходный код C# для запуска инструкции CREATE ASSEMBLY в базе данных Azure SQL (предварительно нужно закодировать DLL-файл в строку, содержащую длинное шестнадцатеричное число)." 
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
	ms.date="04/17/2015" 
	ms.author="genemi"/>


# Инструкция CREATE ASSEMBLY в базе данных Azure SQL с использованием CSharp


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


В этой статье приведен пример на C#, который можно использовать для выполнения инструкции [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) к базе данных Azure SQL. В базе данных SQL предложение FROM не принимает простой формат пути к расположению на локальном компьютере, где размещена база данных. Чтобы обойти это ограничение, закодируйте двоичные разряды DLL-файла сборки в длинную строку, содержащую шестнадцатеричное число, и укажите ее как значение предложения FROM.


### Предварительные требования


Чтобы понять эту статью, вам нужно хотя бы частично ознакомиться со следующим материалом:


- [Функции среды CLR с табличным значением](http://msdn.microsoft.com/library/ms131103.aspx)<br/>В нем объясняется, как инструкция CREATE ASSEMBLY (Transact-SQL) работает с другими инструкциями локального сервера Microsoft SQL Server.


## О. Общее описание процедуры


1. Используйте инструкции DROP FUNCTION и DROP ASSEMBLY, если необходимо очистить предыдущее выполнение.
2. Запомните расположение DLL-файла сборки Microsoft .NET Framework, который вы скомпилировали из своего кода. Это расположение нужно будет указать на следующем этапе. 
3. Запустите EXE-файл, исходный код которого (C#) приведен в этой статье. Скажите EXE-файлу, где хранится DLL-файл.
 - Двоичный DLL-файл будет закодирован в длинную строку, содержащую шестнадцатеричное число.
 - Инструкция CREATE ASSEMBLY запускается с шестнадцатеричной строкой из предложения FROM.
4. Выполните инструкцию [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx), чтобы создать ссылку на метод в сборке.
5. Выполните инструкцию SELECT (T-SQL), чтобы вызвать и протестировать свою функцию.


В предыдущем списке никак не упоминается...<br/> **execute sp_configure 'clr enabled', 1;**<br/> ... так как она не требуется для базы данных SQL Azure, даже если необходима для Microsoft SQL Server.


Если для повторных запусков нужен код T-SQL, удаляющий функцию и сборку, используйте следующий код:


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. DLL-файл простой сборки, на который будет ссылаться функция T-SQL


Простой образец кода C# в этом разделе можно скомпилировать в DLL-файл сборки.


В коде используется метод **CompareCaseSensitiveNet**, на который позже ссылается инструкция CREATE FUNCTION (T-SQL). Обратите внимание, что в метод добавлен атрибут .NET с именем **SqlFunction**. Метод с таким атрибутом может вызываться инструкцией T-SQL как функция.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. Образец кода C&#x23; для EXE-файла, который запускает инструкцию CREATE ASSEMBLY


При запуске EXE-файла, созданного из этого образца кода C#, выполняется такая последовательность действий:


1. При запуске EXE-файла в командной строке вызывается метод **Main**.
2. Метод Main вызывает метод **ObtainHexStringOfAssembly**.
 - Этот метод выдает строку SqlString, в которой сборка указана в виде шестнадцатеричного числа.
3. Метод Main вызывает метод **SubmitCreateAssemblyToAzureSqlDb**.
 - В качестве основных входных данных используется строка SqlString.
 - Результатом будет вызов инструкции CREATE ASSEMBLY, отправляемый в базу данных Azure SQL.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### В.1. Компиляция ссылок и версий


Для компиляции и тестирования EXE-файла, в котором использовался образец кода, мы использовали:


- Visual Studio 2013 с обновлением 4.
 - Шаблон нашего проекта был простым консольным приложением.
- .NET Framework 4.5.


Наш проект в Visual Studio содержал ссылки на следующие сборки для компиляции:


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### В.2. Командная строка для выполнения EXE-файла


Следующий блок кода — это пример командной строки, которую нужно ввести, чтобы выполнить EXE-файл из консоли. Параметры командной строки здесь специально свернуты для большей наглядности.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


Для простоты в этом примере пароль передается как параметр командной строки. Более правильным подходом считается написание кода C#, который получает пароль из файла CONFIG.


## D. Выполнение инструкции CREATE FUNCTION


После сохранения сборки на сервере базы данных Azure SQL вы должны запустить инструкцию CREATE FUNCTION (Transact-SQL), которая ссылается на метод в сборке.


Следующий блок кода Transact-SQL содержит пару неосновных инструкций SELECT, которые подтверждают, что в системе базы данных есть записи для вашей сборки и функции. В самом конце выполняется инструкция SELECT, которая вызывает нужную функцию.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


Указанный выше блок кода Transact-SQL завершается инструкцией SELECT, которая вызывает новую функцию. Эту инструкцию SELECT можно поместить в хранимую процедуру.


<!-- EndOfFile -->

<!---HONumber=July15_HO2-->