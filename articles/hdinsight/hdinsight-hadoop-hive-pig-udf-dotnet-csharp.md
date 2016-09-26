<properties
	pageTitle="Использование C# с Hive и Pig в Hadoop HDInsight | Microsoft Azure"
	description="Узнайте, как использовать определяемые пользователем функции C# при потоковой передаче Hive и Pig в Azure HDInsight."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>


#Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight.

Hive и Pig идеально подходят для работы с данными в Azure HDInsight, но иногда требуется язык программирования более общего назначения. Hive и Pig позволяют вызывать внешний код с помощью определяемых пользователем функций или через потоковую передачу.

В этом документе содержится информация об использовании C# с Hive и Pig.

##Предварительные требования

* Windows 7 или более поздней версии.

* Visual Studio следующих версий:

	* Visual Studio 2012 Professional, Premium или Ultimate с [обновлением 4](http://www.microsoft.com/download/details.aspx?id=39305);

	* Visual Studio 2013 Community, Professional, Premium или Ultimate с [обновлением 4](https://www.microsoft.com/download/details.aspx?id=44921);

	* Visual Studio 2015

* Hadoop в кластере HDInsight. Пошаговые указания по созданию кластера см. в разделе [Подготовка кластера HDInsight](hdinsight-provision-clusters.md).

* Инструменты Hadoop для Visual Studio. Пошаговые указания по установке и настройке инструментов см. в статье [Начало работы со средствами HDInsight Hadoop для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

##.NET в HDInsight

Общеязыковая среда выполнения (CLR) и платформы .NET устанавливаются в кластерах HDInsight под управлением Windows по умолчанию. Это позволяет выполнять потоковую передачу между приложениями C# и Hive и Pig (данные передаются между Hive или Pig и приложением C# через stdout или stdin).

В настоящее время запуск приложений на платформе .NET Framework не поддерживается в кластерах HDInsight под управлением Linux.

##.NET и потоковая передача

При потоковой передаче данные передаются из Hive и Pig во внешнее приложение через stdout, а результаты возвращаются через stdin. Для приложений C# это проще всего выполнить с помощью `Console.ReadLine()` и `Console.WriteLine()`.

Так как Hive и Pig необходимо вызывать приложение во время выполнения, для проектов C# следует использовать шаблон **консольного приложения**.

##Hive и C&#35;

###Создание проекта C#

1. Откройте Visual Studio и создайте новое решение. Выберите для проекта тип **Консольное приложение** и назовите его **HiveCSharp**.

2. Замените содержимое файла **Program.cs** следующим:

        using System;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;

        namespace HiveCSharp
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Parse the string, trimming line feeds
                        // and splitting fields at tabs
                        line = line.TrimEnd('\n');
                        string[] field = line.Split('\t');
                        string phoneLabel = field[1] + ' ' + field[2];
                        // Emit new data to stdout, delimited by tabs
                        Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                    }
                }
                /// <summary>
                /// Returns an MD5 hash for the given string
                /// </summary>
                /// <param name="input">string value</param>
                /// <returns>an MD5 hash</returns>
                static string GetMD5Hash(string input)
                {
                    // Step 1, calculate MD5 hash from input
                    MD5 md5 = System.Security.Cryptography.MD5.Create();
                    byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                    byte[] hash = md5.ComputeHash(inputBytes);

                    // Step 2, convert byte array to hex string
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < hash.Length; i++)
                    {
                        sb.Append(hash[i].ToString("x2"));
                    }
                    return sb.ToString();
                }
            }
        }

3. Создайте проект.

###Отправка в хранилище

1. В Visual Studio в откройте **обозреватель сервера**.

3. Разверните пункт **Azure**, а затем — **HDInsight**.

4. При появлении запроса введите учетные данные подписки Azure и щелкните **Войти**.

5. Разверните пункт с кластером HDInsight, в который нужно развернуть это приложение, а затем — **учетную запись хранения по умолчанию**.

	![В обозревателе серверов отображается учетная запись хранения для кластера](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. Дважды щелкните **контейнер по умолчанию** для кластера. После этого откроется новое окно с содержимым контейнера по умолчанию.

7. Щелкните значок отправки и перейдите к папке **bin\\debug** для проекта **HiveCSharp**. Наконец, выберите файл **HiveCSharp.exe** и нажмите кнопку **OK**.

	![значок отправки](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. После завершения отправки можно будет использовать приложение с помощью запроса Hive.

###Запрос Hive

1. В Visual Studio в откройте **обозреватель сервера**.

2. Разверните пункт **Azure**, а затем — **HDInsight**.

5. Щелкните правой кнопкой мыши кластер, в котором развернуто приложение **HiveCSharp**, а затем выберите **Написать запрос Hive**.

6. В качестве запроса Hive используйте следующее:

		add file wasbs:///HiveCSharp.exe;

		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		USING 'HiveCSharp.exe' AS
		(clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

    Этот запрос предусматривает выбор полей `clientid`, `devicemake` и `devicemodel` в `hivesampletable` и их передачу в приложение HiveCSharp.exe. В результате запроса приложение должно возвратить три поля, которые хранятся под именами `clientid`, `phoneLabel` и `phoneHash`. Кроме этого, в корневом контейнере хранилища по умолчанию должен быть найден файл HiveCSharp.exe (`add file wasbs:///HiveCSharp.exe`).

5. Щелкните **Отправить**, чтобы отправить задания в кластер HDInsight. Откроется окно **Сводка по заданию Hive**.

6. Щелкайте **Обновить** до тех пор, пока значение параметра **Состояние задания** не изменится на **Завершено**. Чтобы просмотреть выходные данные задания, щелкните **Выходные данные задания**.

##Pig и C#35;

###Создание проекта C#

1. Откройте Visual Studio и создайте новое решение. Выберите для проекта тип **Консольное приложение** и назовите его **PigUDF**.

2. Замените содержимое файла **Program.cs** следующим:

		using System;

		namespace PigUDF
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            string line;
				    // Read stdin in a loop
				    while ((line = Console.ReadLine()) != null)
		            {
		                // Fix formatting on lines that begin with an exception
		                if(line.StartsWith("java.lang.Exception"))
		                {
		                    // Trim the error info off the beginning and add a note to the end of the line
		                    line = line.Remove(0, 21) + " - java.lang.Exception";
		                }
		                // Split the fields apart at tab characters
		                string[] field = line.Split('\t');
		                // Put fields back together for writing
		                Console.WriteLine(String.Join("\t",field));
		            }
		        }
		    }
		}

	Это приложение будет анализировать строки, отправленные из Pig, и переформатировать строки, которые начинаются с `java.lang.Exception`.

3. Сохраните **Program.cs**, а затем создайте проект.

###Отправка приложения

1. Для потоковой передачи Pig требуется локальное приложение в файловой системе кластера. Включите удаленный рабочий стол для кластера HDInsight, а затем выполните подключение, следуя указаниям в статье [Подключение к кластерам HDInsight с использованием протокола RDP](hdinsight-administer-use-management-portal.md#rdp).

2. После подключения скопируйте файл **PigUDF.exe** из каталога **bin/debug** для проекта PigUDF на локальном компьютере в каталог **%PIG\_HOME%** в кластере.

###Использование приложения из Pig Latin

1. Во время сеанса удаленного рабочего стола запустите командную строку Hadoop с помощью значка **командной строки Hadoop** на рабочем столе.

2. Для запуска командной строки Pig используйте следующее:

		cd %PIG_HOME%
		bin\pig

	Откроется командная строка `grunt>`.

3. Чтобы запустить простое задание Pig, используя приложение .NET Framework, введите следующее:

		DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
		LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
		LOG = FILTER LOGS by LINE is not null;
		DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
		DUMP DETAILS;

	Оператор `DEFINE` создает псевдоним `streamer` для приложений pigudf.exe и распределяет его с помощью команды `SHIP` по узлам в кластере. Позже `streamer` используется с оператором `STREAM` для обработки отдельных строк ЖУРНАЛА и возвращает данные в виде ряда столбцов.

> [AZURE.NOTE] Имя приложения, которое используется для потоковой передачи, должно быть заключено в кавычки в виде обратного апострофа (`) при использовании псевдонима и в одинарные кавычки (') при использовании с `SHIP`.

3. После ввода последней строки запустится задание. В итоге возвратятся выходные данные следующего вида:

		(2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
		(2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
		(2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##Сводка

В этом документе мы рассмотрели использование приложения .NET Framework из Hive и Pig в HDInsight. Чтобы узнать об использовании Python с Hive и Pig, см. статью [Использование Python с Hive и Pig в HDInsight](hdinsight-python.md).

Другие способы использования Pig и Hive и дополнительную информацию об использовании MapReduce см. в следующих разделах:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)

* [Использование Pig с HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->