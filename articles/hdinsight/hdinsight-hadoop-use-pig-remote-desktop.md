<properties
   pageTitle="Использование Hadoop Pig и удаленного рабочего стола в HDInsight | Microsoft Azure"
   description="Узнайте, использовать команду Pig для выполнения операторов Pig Latin через подключение к удаленному рабочему столу, установленное с кластером Hadoop под управлением Windows в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="larryfr"/>

#Выполнение заданий Pig через подключение к удаленному рабочему столу

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Этот документ содержит пошаговое руководство по использованию команды Pig для выполнения операторов Pig Latin через подключение к удаленному рабочему столу, установленное с кластером HDInsight под управлением Windows. Pig Latin позволяет создавать приложения MapReduce, описывая преобразования данных, а не функции сопоставления и приведения.

Вот что вы узнаете в этом документе.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight на платформе Windows (Hadoop в HDInsight).

* Клиентский компьютер под управлением Windows 10, Windows 8 или Windows 7.

##<a id="connect"></a>Подключение к удаленному рабочему столу

Запустите протокол удаленного рабочего стола для кластера HDInsight, а затем выполните подключение, следуя инструкциям раздела [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Использование команды Pig

2. Установив подключение к удаленному рабочему столу, запустите **командную строку Hadoop** с помощью значка на рабочем столе.

2. Для выполнения команды Pig используйте следующее:

		%pig_home%\bin\pig

	Откроется командная строка `grunt>`.

3. Введите следующий оператор:

		LOGS = LOAD 'wasbs:///example/data/sample.log';

	Эта команда загружает содержимое файла sample.log в файл LOGS. Вы можете просмотреть содержимое файла с помощью следующей команды:

		DUMP LOGS;

4. Преобразуйте данные, применив регулярное выражение для извлечения из каждой записи только информации об уровне ведения журнала:

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	Вы можете использовать **DUMP**, чтобы просмотреть данные после преобразования. В этом случае — `DUMP LEVELS;`.

5. Продолжайте применение преобразований с помощью следующих инструкций. Используйте `DUMP` для просмотра результатов преобразования после каждого шага.

	<table>
	<tr>
	<th>Инструкция</th><th>Действие</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Удаляет строки, содержащие значение NULL для уровня ведения журнала и сохраняет результаты в FILTEREDLEVELS.</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Группирует строки по уровню ведения журнала и сохраняет результаты в GROUPEDLEVELS.</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Создает новый набор данных, который содержит каждое уникальное значение уровня ведения журнала и количество его вхождений. Он сохраняется в FREQUENCIES.</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Упорядочивает уровни ведения журнала по количеству (по убыванию) и сохраняет данные в RESULT.</td>
	</tr>
	</table>

6. Можно также сохранить результаты преобразования с помощью оператора `STORE`. Например, следующий оператор сохраняет `RESULT` в каталог **/example/data/pigout** в используемом по умолчанию контейнере хранилища для кластера:

		STORE RESULT into 'wasbs:///example/data/pigout'

	> [AZURE.NOTE] Данные хранятся в указанном каталоге в файлах с именем **part-№№№№№**. Если каталог уже существует, появится сообщение об ошибке.

7. Чтобы выйти из командной строки grunt, введите следующий оператор.

		QUIT;

###Пакетные файлы Pig Latin

Вы также можете использовать команду Pig для выполнения кода Pig Latin, содержащегося в файле.

3. После выхода из командной строки grunt откройте **Блокнот** и создайте новый файл с именем **pigbatch.pig** в каталоге **%PIG\_HOME%**.

4. Введите или вставьте следующие строки в файл **pigbatch.pig**, а затем сохраните его:

		LOGS = LOAD 'wasbs:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Используйте следующую команду, чтобы запустить файл **pigbatch.pig** с помощью команды Pig.

		pig %PIG_HOME%\pigbatch.pig

	После завершения пакетного задания вы должны увидеть следующий результат, который должен быть таким же, как при использовании `DUMP RESULT;` в предыдущих шагах.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>Сводка

Как вы видите, команда Pig позволяет интерактивно выполнять операции MapReduce или задания Pig Latin, хранимые в пакетном файле.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->