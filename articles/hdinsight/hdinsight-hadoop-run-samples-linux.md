<properties
	pageTitle="Выполнение примеров Hadoop MapReduce в HDInsight на основе Linux | Microsoft Azure"
	description="Приступая к работе с примерами MapReduce в службе HDInsight под управлением Linux. Для подключения к кластеру используйте SSH, после чего используйте команду Hadoop для запуска примеров заданий."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
    tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="larryfr"/>




#Выполнение примеров Hadoop в HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Кластеры HDInsight под управлением Linux предоставляют набор примеров MapReduce, при помощи которых можно познакомиться с процедурой запуска заданий Hadoop MapReduce. В этом документе рассказывается о доступных примерах и пошаговом выполнении некоторых из них.

##Предварительные требования

- **Подписка на Azure**. См. [Получение бесплатной пробной версии Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **Кластер HDInsight на основе Linux**. См. [Приступая к работе с Hadoop с Hive в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Клиент SSH**. Сведения об использовании SSH с HDInsight приведены в следующих статьях.

    - [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Примеры ##

**Расположение**. Примеры находятся в хранилище кластера HDInsight по адресу **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Содержимое**. В этом архиве содержатся следующие примеры:

- **aggregatewordcount**: программа MapReduce для подсчета слов во входных файлах с использованием функций агрегации
- **aggregatewordhist**: программа MapReduce для создания гистограммы слов из входных файлов с использованием функций агрегации
- **bbp**: программа MapReduce для вычисления знаков числа пи с использованием формулы Бэйли—Боруэйна—Плаффа
- **dbcount**: пример задания для расчета количества страниц при просмотре базы данных
- **distbbp**: программа MapReduce для вычисления знаков числа пи с использованием формулы ББП
- **grep**: программа MapReduce для подсчета вхождений регулярного выражения во входных данных
- **join**: задание для выполнения объединения сортированных наборов данных одного размера
- **multifilewc**: задание для подсчета слов из нескольких файлов
- **pentomino**: программа укладки прямоугольников MapReduce для поиска решений при игре в пентамино
- **pi**: программа MapReduce для расчета числа пи по методу квази-Монте-Карло
- **randomtextwriter**: программа Map/Reduce для записи на узел 10 ГБ случайных текстовых данных
- **randomwriter**: программа Map/Reduce для записи на узел 10 ГБ случайных данных
- **secondarysort**: пример определения вторичной сортировки для редуктора
- **sort**: программа Map/Reduce для сортировки случайных данных
- **sudoku**: программа решения судоку
- **teragen**: создание данных для программы TeraSort
- **terasort**: выполнение программы TeraSort
- **teravalidate**: проверка результатов выполнения программы TeraSort
- **wordcount**: программа MapReduce для подсчета слов во входных файлах
- **wordmean**: программа MapReduce для подсчета средней длины слов во входных файлах
- **wordmedian**: программа MapReduce для подсчета средней длины слов по Смиту во входных файлах
- **wordstandarddeviation**: программа MapReduce для подсчета среднеквадратического отклонения в длинах слов во входных файлах

**Исходный код**: исходный код этих примеров располагается в хранилище кластера HDInsight по адресу **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE]Значение `2.2.4.9-1` в пути соответствует версии платформы данных Hortonworks для кластера HDInsight, которое может измениться при обновлении HDInsight.

## Как выполнять примеры ##

1. Подключитесь к HDInsight через SSH, как описано в следующих статьях.

    - [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. В командной строке `username@#######:~$` введите следующую команду, чтобы вывести список примеров:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    В результате будет создан список примеров из предыдущего раздела этого документа.

3. Чтобы получить справку по конкретному примеру, используйте следующую команду: В данном случае запускается пример **wordcount**:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Вы должны увидеть следующий результат:

        Usage: wordcount <in> [<in>...] <out>

    Это означает, что можно ввести несколько путей исходных документов и конечный путь для записи выходных данных (число слов в исходных документах).

4. Для подсчета всех слов в книге «Записи Леонардо да Винчи», которая поставляется с кластером и служит примером исходных данных, используйте следующую команду:

    	hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Исходные данные для этого задания считываются по следующему пути: ****wasb:///example/data/gutenberg/davinci.txt**.

    Результаты этого задания будут записаны по следующему пути: ****wasb:///example/data/davinciwordcount**.

    > [AZURE.NOTE]Как сказано в справке по примеру wordcount, вы также можете указать несколько входных файлов. Например, команда `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` позволит подсчитать слова в файлах davinci.txt и ulysses.txt.

5. По завершении задания воспользуйтесь следующей командой, чтобы просмотреть результат:

        hadoop fs -cat /example/data/davinciwordcount/*

    Все файлы, созданные в процессе выполнения задания, будут объединены и выведены на экран. В этом несложном примере был создан только один файл, однако при наличии нескольких файлов эта команда выполнит их перебор.

    После выполнения команды должен появиться результат, аналогичный приведенному ниже.

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Каждая строка соответствует одному слову и частоте его появления в исходных данных.

## Судоку

Пример «Судоку» содержит немного бесполезную инструкцию: «Включить головоломку в командную строку».

[Судоку](https://en.wikipedia.org/wiki/Sudoku) — это логическая головоломка, которая состоит из девяти полей размером 3 x 3 клетки. Некоторые ячейки в клетках содержат числа, остальные ячейки пустые. Задача заключается в поиске чисел для пустых ячеек. По указанной выше ссылке можно получить дополнительные сведения о головоломке, однако целью этого примера является поиск значений для пустых ячеек. Таким образом, на входе программы должен быть файл в следующем формате.

- Девять строк и девять столбцов

- Каждый столбец может содержать число или знак `?` (означает пустую ячейку).

- Ячейки разделяются пробелами

Следующей задачей является составление головоломки Судоку, по правилам которой не допускается использование одного и того же числа в строке или столбце. К счастью, в хранилище кластера HDInsight имеется соответствующим образом подготовленный пример. Он находится по адресу **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** и имеет следующее содержимое:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE]Значение `2.2.4.9-1` в пути может меняться при обновлении кластера HDInsight.

Чтобы обработать эти данные, используйте следующую команду:

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Результат должен выглядеть аналогично следующему:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## Пи (π)

В примере «Пи» используется статистический метод (квази-Монте-Карло) оценки значения числа пи. Точки в произвольном порядке помещаются внутри единичного квадрата, а также попадают в круг, вписанный в этот квадрат, с вероятностью, равной площади круга, пи/4. Значение числа пи можно оценить по формуле 4R, где R — это отношение количества точек, находящихся внутри круга, к общему количеству точек, находящихся внутри квадрата. Чем больше выборка используемых точек, тем точнее оценка.

В данном примере программа mapper создает указанное количество точек, размещаемых в произвольном порядке внутри единичного квадрата, а затем подсчитывает количество тех точек, которые оказались внутри круга.

Программа reducer суммирует точки, подсчитываемые программами mapper, а затем оценивает значение числа пи по формуле 4R, где R — это отношение количества точек внутри круга к общему количеству точек внутри квадрата.

Для запуска примера используйте следующую команду. Для оценки числа пи в программе используется 16 карт с 10 000 000 образцами в каждой:

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

Должно быть возвращено значение **3,14159155000000000000**. Для справки, первые 10 знаков числа пи: 3,1415926535.

##Graysort 10GB

GraySort — это измерение производительности сортировки, показателем которой служит скорость (ТБ/мин), достигаемая при сортировке очень больших объемов данных, обычно не менее 100 ТБ.

В этом примере используется небольшой объем данных, 10 ГБ, чтобы можно было выполнить сортировку достаточно быстро. Используется приложение MapReduce, разработанное Оуэном О'Мэлли (Owen O'Malley) и Аруном Мёрти (Arun Murthy). Это приложение победило в 2009 г. на конкурсе приложений сортировки общего назначения (daytona) для больших объемов данных, показав скорость 0,578 ТБ/мин (100 ТБ за 173 минуты). Дополнительные сведения об этом и других измерениях производительности сортировки см. на сайте [Sortbenchmark](http://sortbenchmark.org/).

В этом примере используются три набора программ MapReduce.

- **TeraGen**: программа MapReduce, которая создает строки с данными для последующей сортировки.

- **TeraSort**: производит выборку входных данных и использует MapReduce для сортировки данных в общем порядке.

    TeraSort представляет стандартные функции MapReduce, за исключением настраиваемого разделителя, в котором используется отсортированный список выборки ключей N-1, определяющий диапазон ключей для каждой функции reduce. В частности, все ключи, подобные этому примеру [i-1] <= key < sample[i] отправляются для сокращения i. Это гарантирует, что все выходные данные сокращения i будут меньше, чем выходные данные i+1.

- **TeraValidate**: программа MapReduce, которая проверяет глобальную сортировку выходных данных.

    В выходном каталоге создается одна функция map для каждого файла, и каждая функция map гарантирует, что каждый ключ будет меньше или равен предыдущему. Функция map также создает записи первого и последнего ключей каждого файла, а функция reduce гарантирует, что первый ключ файла i больше последнего ключа файла i-1. Все проблемы указываются в выходных данных функции reduce вместе с неотсортированными ключами.

Для создания данных, сортировки и проверки выходных данных используйте следующую команду:

1. Создание 10 ГБ данных, которые будут храниться в хранилище кластера HDInsight по умолчанию по адресу ****wasb:///example/data/10GB-sort-input**:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

	Ключ `-Dmapred.map.tasks` говорит Hadoop о том, сколько задач сопоставления будет использоваться в этом задании. Последние два параметра означают, что задание создаст 10 ГБ данных и сохранит их по адресу ****wasb:///example/data/10GB-sort-input**.

2. Выполните следующую команду, чтобы отсортировать данные:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

	Ключ `-Dmapred.reduce.tasks` говорит Hadoop о том, сколько задач сокращения будет использоваться в этом задании. Последние два параметра соответствуют путям расположения входных и выходных данных.

3. Используйте следующую команду, чтобы просмотреть отсортированные данные:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##Дальнейшие действия ##

В данной статье рассмотрено выполнение примеров, поставляемых с кластерами HDInsight под управлением Linux. Учебники по использованию Pig, Hive и MapReduce в службе HDInsight см. в следующих статьях.

* [Использование Pig с Hadoop в HDInsight][hdinsight-use-pig]
* [Использование Hive с Hadoop в HDInsight][hdinsight-use-hive]
* [Использование MapReduce с Hadoop в HDInsight][hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=AcomDC_1210_2015-->