<properties
   pageTitle="Использование Hadoop Pig в HDInsight для платформы Azure"
   description="Информация об использовании Pig с Hadoop в HDInsight через удаленный рабочий стол."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Выполнение заданий Pig с помощью команды Pig (удаленный рабочий стол)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Этот документ содержит пошаговое руководство по использованию команды Pig для интерактивного выполнения операторов Pig Latin и применению ее в качестве пакетного задания Hadoop в кластере HDInsight на платформе Linux. Pig Latin позволяет создавать приложения MapReduce, описывая преобразования данных, а не функции сопоставления и приведения.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер HDInsight на платформе Windows (Hadoop в HDInsight).

* Клиент Windows 7, Windows 8 или Windows 10.

##<a id="connect"></a>Подключение к удаленному рабочему столу

Запустите протокол удаленного рабочего стола для кластера HDInsight, а затем выполните подключение, следуя инструкциям раздела <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Соединение с кластерами HDInsight с использованием протокола RDP</a>.

##<a id="pig"></a>Использование команды Pig

2. После подключения откройте окно **командной строки Hadoop** с помощью значка на рабочем столе.

2. Используйте следующую команду для запуска командной строки Pig.

		%pig_home%\bin\pig

	Откроется строка `grunt>`. 

3. Введите следующую инструкцию.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Эта команда загружает содержимое файла sample.log в LOGS. Вы можете просмотреть содержимое файла следующим образом.

		DUMP LOGS;

4. Затем преобразуйте данные, применив регулярное выражение для извлечения из каждой записи только информации об уровне ведения журнала, следующим образом.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	Вы можете использовать **DUMP**, чтобы просмотреть данные после преобразования. В этом случае - `DUMP LEVELS;`.

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

6. Можно также сохранить результаты преобразования с помощью инструкции `STORE`. Например, следующая инструкция сохраняет `RESULT` в каталог **/example/data/pigout** в используемом по умолчанию контейнере хранилища для кластера.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] Данные хранятся в указанном каталоге в файлах с именем **part-№№№№№**. Если каталог уже существует, появится сообщение об ошибке.

7. Чтобы выйти из командной строки grunt, введите следующую инструкцию.

		QUIT;

###Пакетные файлы Pig Latin

Вы также можете использовать команду Pig для выполнения кода Pig Latin, содержащегося в файле.

3. После выхода из строки grunt откройте **Блокнот** и создайте новый файл с именем **pigbatch.pig** в каталоге **%PIG_HOME%**.

4. Введите или вставьте следующие строки в файл **pigbatch.pig**, а затем сохраните его, когда все будет готово.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Используйте следующую команду, чтобы запустить файл **pigbatch.pig** с помощью команды pig.

		pig %PIG_HOME%\pigbatch.pig

	После завершения пакетного задания вы должны увидеть следующий результат, который должен быть таким же, как при использовании `DUMP RESULT;` в предыдущих шагах.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>Сводка

Как вы видите, команда Pig позволяет интерактивно выполнять операции MapReduce с помощью Pig Latin, а также выполнять инструкции, хранимые в пакетном файле.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
<!--HONumber=47-->
