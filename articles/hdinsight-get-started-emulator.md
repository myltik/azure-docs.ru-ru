<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" author="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Приступая к работе с эмулятором HDInsight

Этот учебник содержит сведения о том, как начать работу с эмулятором HDInsight Microsoft для Windows Azure (ранее HDInsight Server Developer Preview), используя кластеры Hadoop. Эмулятор HDInsight поставляется с теми же компонентами из экосистемы Hadoop, что и Azure HDInsight. Подробные сведения, включая информацию о развернутых версиях, см. в разделе [Версии Hadoop, доступные в Azure HDInsight][Версии Hadoop, доступные в Azure HDInsight]

Эмулятор HDInsight предоставляет локальную среду разработки для Azure HDInsight. Если вы знакомы с Hadoop, можно начать работу с эмулятором с помощью HDFS. Однако в HDInsight по умолчанию используется файловая система хранилища BLOB-объектов Azure (WASB или хранилище Azure — BLOB), поэтому в конечном итоге будет необходимо работать над заданиями с помощью WASB. Разработку под WASB можно начать с использования эмулятора хранилища Azure — возможно, нужно использовать только небольшое подмножество данных (в эмуляторе HDInsight не требуется изменять конфигурацию, достаточно другого имени учетной записи хранения). Затем необходимо протестировать задания локально для хранилища Windows Azure — опять же, с использованием только подмножества данных (без изменения конфигурации эмулятора HDInsight). Наконец, теперь можно переместить вычислительную часть задания в HDInsight и выполнить это задание для производственных данных.

> [WACOM.NOTE] Эмулятор HDInsight может использовать только развертывание в одном узле.

Учебник по использованию HDInsight см. в разделе [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]

**Предварительные условия**

Перед началом работы с этим учебником необходимо иметь следующее:

-   Эмулятор HDInsight требует 64-разрядной версии Windows. Должна быть установлена одна из следующих систем:

    -   Windows 7 с пакетом обновления 1
    -   Windows Server 2008 R2 с пакетом обновления 1
    -   Windows 8
    -   Windows Server 2012.
-   Установите и настройте Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

## В этом учебнике рассматриваются следующие темы:

-   [Установка эмулятора HDInsight][Установка эмулятора HDInsight]
-   [Выполнение примера программы с подсчетом слов][Выполнение примера программы с подсчетом слов]
-   [Выполнение примеров начального уровня][Выполнение примеров начального уровня]
-   [Подключение к хранилищу BLOB-объектов Azure][Подключение к хранилищу BLOB-объектов Azure]
-   [Выполнение HDInsight PowerShell][Выполнение HDInsight PowerShell]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="install"></a>Установка эмулятора HDInsight

Эмулятор Microsoft HDInsight устанавливается через установщик веб-платформы Майкрософт.

> [WACOM.NOTE] Эмулятор HDInsight в настоящее время поддерживает только английские ОС.

> [WACOM.NOTE] Если установлена версия Microsoft HDInsight Developer Preview, необходимо предварительно удалить следующие два компонента в окне панели управления "Программы и компоненты".
>
> -   HDInsight Developer Preview
> -   Hortonworks Data Platform Developer Preview
>
> </p>

**Установка эмулятора HDInsight**

1.  Откройте Internet Explorer и перейдите на [страницу установки эмулятора Microsoft HDInsight для Azure][страницу установки эмулятора Microsoft HDInsight для Azure].
2.  Нажмите кнопку **Установить**.
3.  Нажмите кнопку **Выполнить** в ответ на запрос на установку HDINSIGHT.exe в нижней части страницы.
4.  Нажмите кнопку **Да** во всплывающем окне **Контроль учетных записей пользователей**, чтобы завершить установку. Откроется окно установщика веб-платформы 4.6.
5.  Нажмите кнопку **Установить** в нижней части страницы.
6.  Нажмите кнопку **Принимаю**, чтобы согласиться с условиями лицензионного соглашения.
7.  Убедитесь, что в установщике веб-платформы отображена запись **Были успешно установлены следующие продукты**, и нажмите кнопку **Готово**
8.  Нажмите кнопку **Выйти**, чтобы закрыть окно установщика веб-платформы 4.6.

    В ходе установки на рабочем столе должны появиться три значка. Они связаны следующим образом:

    -   **Командная строка Hadoop** Командная строка Hadoop: командная строка Hadoop для выполнения заданий MapReduce, Pig и Hive в эмуляторе HDInsight.

    -   **Состояние Hadoop NameNode**: NameNode обеспечивает ведение древовидного каталога для всех файлов в HDFS. Эта служба также отслеживает местонахождение данных для всех файлов в кластере Hadoop. Клиенты взаимодействуют с NameNode, чтобы узнать, где хранятся узлы данных для всех файлов.

    -   **Состояние Hadoop MapReduce**: Инспектор задания, который распределяет задания MapReduce между узлами в кластере.

    В ходе установки также должно быть установлено несколько локальных служб. Далее представлен снимок экрана окна "Службы":

    ![HDI.Emulator.Services][HDI.Emulator.Services]

    Известные проблемы установки и запуска сервера HDInsight см. в статье [Заметки к версии для эмулятора HDInsight][Заметки к версии для эмулятора HDInsight]. Журнал установки находится по пути: **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

## <a name="runwordcount"></a>Выполнение задания на подсчет слов в MapReduce

Теперь эмулятор HDInsight настроен на рабочей станции. Можно запустить задание MapReduce, чтобы проверить правильность установки. Сначала нужно будет загрузить несколько текстовых файлов в HDFS, а затем запустить задание MapReduce на подсчет слов, которое подсчитывает частотность слов в этих файлах.

Программа подсчета слов MapReduce упакована в *hadoop-examples.jar*. Jar-файл размещен в папке *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT*.

JAR-команда имеет следующий синтаксис:

    hadoop jar <jar> [mainClass] args...

Также будут использоваться некоторые команды fs . Дополнительные сведения о командах Hadoop см. в [Справочнике команд Hadoop][Справочнике команд Hadoop]

Задание на подсчет слов MapReduce принимает два аргумента: входная папка и выходная папка. В качестве входной папки будет использоваться *hdfs://localhost/user/HDIUser*, а в качестве каталога вывода — *hdfs://localhost/user/HDIUser/WordCount\_Output*. Выходная папка не может быть одной из существующих папок, в противном случае задание MapReduce завершится ошибкой. Если нужно выполнить задание MapReduce во второй раз, необходимо указать другую выходную папку или удалить существующую.

**Выполнение задания MapReduce на подсчет слов**

1.  На рабочем столе дважды щелкните значок **Командная строка Hadoop**, чтобы открыть окно командной строки Hadoop. Текущая папка должна быть следующей:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    В противном случае выполните следующую команду:

        cd %hadoop_home%

2.  Выполните следующую команду Hadoop, чтобы создать папку HDFS для хранения входных и выходных файлов:

        hadoop fs -mkdir /user/HDIUser

3.  Выполните следующую команду Hadoop, чтобы скопировать некоторые локальные файлы в HDFS:

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  Выполните следующую команду, чтобы вывести список файлов в папке /user/HDIUser :

        hadoop fs -ls /user/HDIUser

    Вы увидите следующие файлы:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 8 items
        -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
        -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
        -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
        -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
        -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  Выполните следующую команду, чтобы выполнить задание MapReduce на подсчет слов:

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Выполните следующую команду, чтобы вывести список слов, содержащих "windows", из выходного файла:

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    Должен быть получен следующий результат:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
        rt-r-00000 | findstr "windows"
        windows 12
        windows+java6.  1
        windows.        3

## <a name="rungetstartedsamples"></a> Выполнение примеров начального уровня

Установка эмулятора HDInsight содержит ряд примеров, которые позволяют новым пользователям быстро приступить к изучению служб на платформе Apache Hadoop в Windows. В этих примерах рассматриваются некоторые задачи, которые обычно необходимы при обработке большого набора данных. Изучая эти примеры, можно ознакомиться с понятиями, связанными с моделью программирования MapReduce и соответствующей экосистемой.

Примеры упорядочены по сценариям обработки данных журнала IIS W3C. Предоставлено средство создания данных, которое обеспечивает создание и импорт наборов данных различных размеров в HDFS или WASB (хранилище BLOB-объектов Azure). Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure для HDInsight][Использование хранилища BLOB-объектов Azure для HDInsight]). Затем можно выполнить задания MapReduce, Pig или Hive по страницам данных, которые были созданы скриптом PowerShell. Обратите внимание, что скрипты Pig и Hive компилируются в программы MapReduce. Пользователи могут выполнить ряд заданий для самостоятельного анализа результатов применения этих разных технологий, а также влияния размера данных на выполнение задач обработки.

### В этом разделе

-   [Сценарии данных журнала IIS w3c][Сценарии данных журнала IIS w3c]
-   [Загрузка примера данных журнала w3c][Загрузка примера данных журнала w3c]
-   [Выполнение заданий MapReduce на Java][Выполнение заданий MapReduce на Java]
-   [Выполнение заданий Hive][Выполнение заданий Hive]
-   [Выполнение заданий Pig][Выполнение заданий Pig]
-   [Повторное построение примеров][Повторное построение примеров]

### <a name="scenarios"></a>Сценарии данных журнала IIS w3c

Сценарий w3c создает и импортирует данные журнала IIS W3C трех размеров в HDFS или WASB: 1 МБ, 500 МБ и 2 ГБ. Он предоставляет три типа заданий и реализует каждое из них на C#, Java, Pig и Hive.

-   **totalhits**: вычисление общего числа запросов для конкретной страницы
-   **avgtime**: вычисление среднего времени (в секундах) для запроса на страницу
-   **errors**: вычисление числа ошибок на странице в час для запросов со статусом 404 или 500

Эти примеры и сопутствующая документация не обеспечивают глубокого изучения или полной реализации ключевых технологий Hadoop. Используется кластер только с одним узлом, поэтому эффект от добавления дополнительных узлов в данном случае оценить невозможно.

### <a name="loaddata"></a>Загрузка примера данных журнала w3c

Создание и импорт данных в HDFS осуществляется с использованием скрипта PowerShell importdata.ps1.

**Чтобы импортировать пример данных журнала w3c:**

1.  Откройте командную строку Hadoop с рабочего стола.
2.  Выполните следующую команду, чтобы перейти к каталогу **C:\\Hadoop\\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Выполните следующую команду, чтобы создать и импортировать данные в HDFS:

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Если вместо этого требуется загрузить данные в WASB, см. [Подключение к хранилищу BLOB-объектов Azure][Подключение к хранилищу BLOB-объектов Azure].

4.  Чтобы вывести импортированные файлы в HDFS, выполните следующую команду в командной строке Hadoop:

        hadoop fs -lsr /w3c

    Должен быть получен результат, аналогичный приведенному ниже:

        c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
        -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
        -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
        -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  Выполните следующую команду, чтобы отобразить один из файлов данных в окне консоли:

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Файл данных создан и импортирован в HDFS. Можно выполнить различные задания Hadoop.

### <a name="javamapreduce"></a>Выполнение заданий MapReduce на Java

MapReduce — основной механизм вычислений для Hadoop. По умолчанию он реализован на языке Java, но существуют также примеры, в которых используются .NET и Hadoop Streaming, использующие C#. Синтаксис для выполнения задания MapReduce :

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

JAR-файл и исходные файлы находятся в папке C:\\Hadoop\\GettingStarted\\Java.

**Выполнение задания MapReduce для расчета посещений веб-страницы**

1.  Откройте командную строку Hadoop.
2.  Выполните следующую команду, чтобы перейти к каталогу **C:\\Hadoop\\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Выполните следующую команду, чтобы удалить выходной каталог, если такая папка уже существует. Если выходная папка уже есть, задание MapReduce завершится ошибкой.

        hadoop fs -rmr /w3c/output

4.  Выполните следующую команду:

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    В следующей таблице описаны элементы этой команды:

    <table border="1">

    <tr>
    <td>
    Параметр

    </td>
    <td>
    Примечание.

    </td>
    </tr>

    <tr>
    <td>
    w3c\_scenarios.jar

    </td>
    <td>
    JAR-файл находится в папке C:\\Hadoop\\GettingStarted\\Java.

    </td>
    </tr>

    <tr>
    <td>
    microsoft.hadoop.w3c.TotalHitsForPage

    </td>
    <td>
    Тип может быть заменен одним из следующих:
	<ul>
    <li>microsoft.hadoop.w3c.AverageTimeTaken</li>
    <li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul>
    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    Входной файл может быть заменен одним из следующих:
	<ul>
    <li>/w3c/input/medium/data\_w3c\_medium.txt</li>
    <li>/w3c/input/large/data\_w3c\_large.txt</li>
	</ul>
    </td>
    </tr>

    <tr>
    <td>
    /w3c/output

    </td>
    <td>
    Это имя выходной папки.

    </td>
    </tr>

    </table>

5.  Выполните следующую команду, чтобы отобразить выходной файл:

        hadoop fs -cat /w3c/output/part-00000

    Результат должен быть аналогичен приведенному ниже:

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130

    Таким образом страница Default.aspx имеет 3409 посещений и т. д.

### <a name="hive"></a> Выполнение заданий Hive

Механизм обработки запросов Hive покажется знакомым аналитикам с хорошим знанием SQL. Он обеспечивает интерфейс SQL и реляционную модель данных для HDFS. Hive использует язык HiveQL (или HQL), который является диалектом SQL.

**Чтобы выполнить задание Hive**

1.  Откройте командную строку Hadoop.
2.  Измените каталог на папку **C:\\Hadoop\\GettingStarted**
3.  Выполните следующую команду, чтобы удалить папку **/w3c/hive/input**, если такая уже существует. Если выходная папка уже есть, задание Hive завершится ошибкой.

        hadoop fs -rmr /w3c/hive/input

4.  Выполните следующую команду, чтобы создать папку **/w3c/hive/input** и скопировать файл данных с рабочей станции в HDFS:

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Выполните приведенную ниже команду, чтобы запустить скрипт**w3ccreate.hql**. Этот скрипт создает таблицу Hive и загружает данные в эту таблицу:

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    Скрипт HiveQL :

        DROP TABLE w3c;

        CREATE TABLE w3c(
         logdate string,
         logtime string,
         c_ip string,
         cs_username string,
         s_ip string,
         s_port string,
         cs_method string,
         cs_uri_stem string,
         cs_uri_query string,
         sc_status int,
         sc_bytes int,
         cs_bytes int,
         time_taken int,
         cs_agent string, 
         cs_Referrer string)
        ROW FORMAT delimited
        FIELDS TERMINATED BY ' ';

        LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    Результат должен быть аналогичен приведенному ниже:

        c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea    te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
        Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
        Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
        OK
        Time taken: 0.616 seconds
        OK
        Time taken: 0.139 seconds
        Loading data to table default.w3c
        Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
        OK
        Time taken: 0.573 seconds

6.  Выполните следующую команду, чтобы запустить файл скрипта HiveQL **w3ctotalhitsbypate.hql**.

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    В следующей таблице описаны элементы этой команды:

    <table border="1">

    <tr>
    <td>
    Файл

    </td>
    <td>
    Описание

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\hive-0.9.0\\bin\\hive.cmd

    </td>
    <td>
    Командный скрипт Hive.

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql

    </td>
    <td>
    Файл скрипта Hive можно заменить одним из следующих файлов:
	<ul>
    <li>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li>
    <li>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li>
	</ul>
    </td>
    </tr>
    </p>
    </table>

    Скрипт HiveQL: w3ctotalhitsbypage.hql:

        SELECT filtered.cs_uri_stem,COUNT(*) 
        FROM (
          SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
        ) filtered
        GROUP BY (filtered.cs_uri_stem);

    Завершающая часть выходных данных должна иметь следующий вид:

        MapReduce Total cumulative CPU time: 3 seconds 47 msec
        Ended Job = job_201310291309_0006
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
        rite: 53 SUCCESS
        Total MapReduce CPU Time Spent: 3 seconds 47 msec
        OK
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130
        Time taken: 34.68 seconds

Обратите внимание, что в качестве первого шага в каждом из заданий создается таблица, в которую загружаются данные из созданного ранее файла. Найти этот файл можно в узле /Hive в HDFS при помощи следующей команды:

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Выполнение заданий Pig

При обработке данных в Pig используется язык потока данных под названием *Pig Latin*. Абстракции на Pig Latin предоставляют расширенные структуры данных по сравнению с MapReduce и играют такую же роль в Hadoop, как SQL в СУБД.

**Чтобы выполнить задания Pig:**

1.  Откройте командную строку Hadoop.
2.  Измените каталог на папку C:\\Hadoop\\GettingStarted.
3.  Выполните следующую команду, чтобы отправить задание Pig:

        C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    В следующей таблице показаны элементы этой команды:

    <table border="1">

    <tr>
    <td>
    Файл

    </td>
    <td>
    Описание

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\pig-0.9.3-SNAPSHOT\\bin\\pig.cmd

    </td>
    <td>
    Командный скрипт Pig.

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig

    </td>
    <td>
    Файл скрипта на Pig Latin можно заменить одним из следующих файлов:
	<ul>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li>
	</ul>
    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    Можно заменить этот параметр файлом большего размера:

    <ul>
    <li>/w3c/input/medium/data\_w3c\_medium.txt</li>
    <li>/w3c/input/large/data\_w3c\_large.txt</li>
	</ul>

    </td>
    </tr>
    </table>

    Должен быть получен результат, аналогичный приведенному ниже:

        (/Info.aspx,1115)
        (/UserService,1130)
        (/Default.aspx,3409)

Обратите внимание, что поскольку скрипты Pig компилируются в задания MapReduce и потенциально в несколько таких заданий, пользователи могут видеть несколько выполняемых заданий MapReduce во время обработки задания Pig.

### <a name="rebuild"></a> Повторное построение примеров

Примеры в настоящее время содержат все необходимые двоичные файлы, поэтому построение не обязательно. Если вы хотите внести изменения в образцы Java или .NET, их можно повторно построить с помощью msbuild или включенного скрипта PowerShell.

**Чтобы повторно построить примеры**

1.  Откройте командную строку Hadoop.
2.  Выполните следующую команду:

        powershell -F buildsamples.ps1

## <a name="blobstorage"></a>Подключение к хранилищу BLOB-объектов Azure

Служба Azure HDInsight использует хранилище BLOB-объектов Azure в качестве файловой системы по умолчанию. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure для HDInsight].

Имеется возможность настроить локальный кластер в эмуляторе HDInsight таким образом, чтобы использовать хранилище BLOB-объектов Azure вместо локального хранилища. В этом разделе рассматриваются следующие вопросы:

-   подключение к эмулятору хранилища;
-   Подключение к хранилищу BLOB-объектов Azure
-   настройка хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию для эмулятора HDInsight.

### Подключение к эмулятору хранилища

Эмулятор хранилища Azure поставляется с [Пакетом SDK Azure для .NET][Пакетом SDK Azure для .NET]. Эмулятор хранилища не запускается автоматически. Необходимо запустить его вручную. Имя приложения — *Эмулятор хранилища Azure*. Для начала и останова эмуляторов щелкните правой кнопкой мыши синий значок Azure в панели задач Windows и выберите команду "Показать пользовательский интерфейс эмулятора хранилища".

> [WACOM.NOTE] При запуске эмулятора хранилища может появиться следующее сообщение об ошибке:

>     The process cannot access the file because it is being used by another process.

> Это происходит потому, что одна из служб Hadoop Hive также использует порт 10000. Чтобы устранить эту проблему, выполните следующую процедуру:

> 1.  Остановите с помощью services.msc две службы Hadoop Hive: Apache Hadoop hiveserver and Apache Hadoop Hiveserver2.
> 2.  Запустите эмулятор хранилища BLOB-объектов.
> 3.  Перезапустите две службы Hadoop Hive.

Для доступа к эмулятору хранилища используется следующий синтаксис:

    wasb://<ContainerName>@storageemulator

Например:

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] Если появляется следующее сообщение об ошибке:

>     ls: No FileSystem for scheme: wasb

> Это связано с тем, что по-прежнему используется версия Developer Preview. Пожалуйста, следуйте инструкциям, изложенным в разделе этой статьи "Установка эмулятора HDInsight", чтобы удалить версию Developer Preview, а затем переустановите приложение.

### Подключение к хранилищу BLOB-объектов Azure

Инструкции по созданию учетной записи хранения см. в разделе [Создание учетной записи хранения][Создание учетной записи хранения].

**Создание контейнера**

1.  Выполните вход на [Портал управления][Портал управления].
2.  Нажмите кнопку **ХРАНИЛИЩЕ** в левой части окна. Будет выведен список учетных записей хранения для данной подписки.
3.  Выберите в списке учетную запись хранения, где требуется создать контейнер.
4.  Нажмите кнопку **КОНТЕЙНЕРЫ** в верхней части страницы.
5.  В нижней части страницы нажмите кнопку **ДОБАВИТЬ**.
6.  Введите значение в поле **ИМЯ** и выберите **ДОСТУП**. Можно использовать любой из трех уровней доступа. Значение по умолчанию — **Частный**.
7.  Нажмите кнопку **Сохранить**, чтобы сохранить изменения. На портале будет выведен новый контейнер.

Для доступа к учетной записи хранения Azure необходимо добавить имя этой учетной записи и соответствующий ключ в файл конфигурации.

**Настройка подключения к учетной записи хранения Azure**

1.  Откройте **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** в Блокноте.
2.  Добавьте следующий тег \<property\> рядом с другими тегами \<property\>:

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    Необходимо заменить \<StorageAccountName\> и \<StorageAccountKey\> значениями, которые соответствуют данным учетной записи хранения.

3.  Сохраните изменения. Нет необходимости перезапускать службы Hadoop.

Для доступа к учетной записи хранения используется следующий синтаксис:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Например:

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### Использование контейнера хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию.

Можно также использовать контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию, как в случае Azure HDInsight.

**Настройка файловой системы по умолчанию с использованием контейнера BLOB-объектов Azure**

1.  Откройте **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** в Блокноте.
2.  Найдите следующий тег \<property\> :

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <value>hdfs://localhost:8020</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

3.  Замените его следующими двумя тегами \<property\>:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <!--<value>hdfs://localhost:8020</value>-->
          <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

        <property>
          <name>dfs.namenode.rpc-address</name>
          <value>hdfs://localhost:8020</value>
          <description>A base for other temporary directories.</description>
        </property>

    Необходимо заменить \<StorageAccountName\> и \<StorageAccountKey\> значениями, которые соответствуют данным учетной записи хранения.

4.  Сохраните изменения.
5.  Откройте командную строку Hadoop на рабочем столе в режиме с повышенными правами (Запуск от имени администратора)
6.  Выполните следующие команды, чтобы перезапустить службы Hadoop.

        C:\Hadoop\stop-onebox.cmd
        C:\Hadoop\start-onebox.cmd

7.  Выполните следующую команду для проверки подключения к файловой системе по умолчанию:

        hadoop fs -ls /

    Следующие команды выводят содержимое папки:

        hadoop fs -ls wasb:///
        hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
        hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    Для доступа к HDFS введите следующую команду:

        hadoop fs -ls hdfs://localhost:8020/

## <a name="powershell"></a> Выполнение HDInsight PowerShell

В эмуляторе HDInsight поддерживаются некоторые командлеты HDInsight PowerShell. К ним относятся следующие командлеты:

-   Командлеты определения заданий HDInsight

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

Ниже приведен пример отправки задания Hadoop :

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Будет выведен запрос при вызове Get-Credential. Необходимо использовать **hadoop** в качестве имени пользователя. Паролем может быть любая строка. Имя кластера всегда — **<http://localhost:50111>**.

Дополнительные сведения об отправке заданий Hadoop см. в разделе [Отправка заданий Hadoop программным способом][Отправка заданий Hadoop программным способом]. Дополнительные сведения о командлетах HDInsight PowerShell см. в разделе [Справочник командлетов HDInsight][Справочник командлетов HDInsight].

## <a name="nextsteps"></a>Дальнейшие действия

При работе с этим учебником вы установили эмулятор HDInsight и выполнили некоторые задания Hadoop . Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight]
-   [Разработка программ MapReduce потоковой передачи Hadoop на C# для HDInsight][Разработка программ MapReduce потоковой передачи Hadoop на C# для HDInsight]
-   [Комментарии к выпуску эмулятора HDInsight][Заметки к версии для эмулятора HDInsight]
-   [Форум MSDN с обсуждением HDInsight][Форум MSDN с обсуждением HDInsight]

  [Версии Hadoop, доступные в Azure HDInsight]: ../hdinsight-component-versioning/
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Установка эмулятора HDInsight]: #install
  [Выполнение примера программы с подсчетом слов]: #runwordcount
  [Выполнение примеров начального уровня]: #rungetstartedsamples
  [Подключение к хранилищу BLOB-объектов Azure]: #blobstorage
  [Выполнение HDInsight PowerShell]: #powershell
  [Дальнейшие действия]: #nextsteps
  [страницу установки эмулятора Microsoft HDInsight для Azure]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [Заметки к версии для эмулятора HDInsight]: ../hdinsight-emulator-release-notes/
  [Справочнике команд Hadoop]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [Использование хранилища BLOB-объектов Azure для HDInsight]: ../hdinsight-use-blob-storage/
  [Сценарии данных журнала IIS w3c]: #scenarios
  [Загрузка примера данных журнала w3c]: #loaddata
  [Выполнение заданий MapReduce на Java]: #javamapreduce
  [Выполнение заданий Hive]: #hive
  [Выполнение заданий Pig]: #pig
  [Повторное построение примеров]: #rebuild
  [Пакетом SDK Azure для .NET]: http://azure.microsoft.com/ru-ru/downloads/
  [Создание учетной записи хранения]: ../storage-create-storage-account/
  [Портал управления]: https://manage.windowsazure.com/
  [Отправка заданий Hadoop программным способом]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Справочник командлетов HDInsight]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn479228.aspx
  [Разработка программ MapReduce на Java для HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Разработка программ MapReduce потоковой передачи Hadoop на C# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Форум MSDN с обсуждением HDInsight]: http://social.msdn.microsoft.com/Forums/ru-ru/hdinsight
