<properties 
	pageTitle="Приступая к работе с эмулятором HDInsight | Azure" 
	description="Узнайте, как использовать эмулятор HDInsight для Azure." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	author="nitinme" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="nitinme"/>

# Приступая к работе с эмулятором HDInsight 

Этот учебник содержит сведения о том, как начать работу с кластерами Hadoop в эмуляторе Microsoft HDInsight для Azure (ранее HDInsight Server Developer Preview). Эмулятор HDInsight поставляется с теми же компонентами из экосистемы Hadoop, что и Azure HDInsight. Подробные сведения, включая информацию о развернутых версиях, см. в разделе [Версии Hadoop, доступные в Azure HDInsight][hdinsight-versions].

>[AZURE.NOTE] В эмулятор HDInsight включен только кластер Hadoop. Он не содержит HBase.

Эмулятор HDInsight предоставляет локальную среду разработки для Azure HDInsight. Если вы знакомы с Hadoop, можно начать работу с эмулятором с помощью HDFS. В HDInsight по умолчанию используется файловая система хранилища BLOB-объектов Azure (WASB или хранилище Azure - BLOB-объекты). В конечном итоге будет необходимо работать над заданиями с помощью WASB. Для использования WASB с эмулятором HDInsight в конфигурацию эмулятора HDInsight необходимо внести изменения. 

> [AZURE.NOTE] Эмулятор HDInsight может использовать только развертывание в одном узле. 


**Необходимые условия**	
Перед началом работы с этим учебником необходимо иметь следующее:

- Эмулятор HDInsight требует 64-разрядной версии Windows. Должна быть установлена одна из следующих систем:

	- Windows 7 с пакетом обновления 1
	- Windows Server 2008 R2 с пакетом обновления 1
	- Windows 8 
	- Windows Server 2012

- Установите и настройте Azure PowerShell. Указания см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. 

## В этом учебнике рассматриваются следующие темы:

* [Установка эмулятора HDInsight](#install)
* [Выполнение примера программы с подсчетом слов](#runwordcount)
* [Выполнение примеров начального уровня](#rungetstartedsamples)
* [Подключение к хранилищу BLOB-объектов Azure](#blobstorage)
* [Выполнение HDInsight PowerShell](#powershell)
* [Удаление эмулятора HDInsight](#remove)
* [Дальнейшие действия](#nextsteps)

##<a name="install"></a>Установка эмулятора HDInsight

Эмулятор Microsoft HDInsight устанавливается через установщик веб-платформы Майкрософт.  

> [AZURE.NOTE] Эмулятор HDInsight в настоящее время поддерживает только английские ОС. Если установлена​предыдущая версия эмулятора, перед установкой последней версии эмулятора необходимо предварительно удалить два компонента в окне панели управления "Программы и компоненты".
><ul>
<li>Эмулятор Microsoft HDInsight для Windows Azure или HDInsight Developer Preview (в зависимости от того, что установлено).</li>
<li>Hortonworks Data Platform</li>
</ul>


**Установка эмулятора HDInsight**

1. Откройте Internet Explorer и перейдите на [страницу установки эмулятора Microsoft HDInsight для Azure][hdinsight-emulator-install].
2. Нажмите кнопку **Установить**. 
3. Нажмите кнопку **Выполнить** в ответ на запрос на установку HDINSIGHT.exe в нижней части страницы. 
4. Нажмите кнопку **Да** во всплывающем окне **Контроль учетных записей пользователей**, чтобы завершить установку. Появится диалоговое окно установщика веб-платформы.
6. Нажмите кнопку **Установить** в нижней части страницы.
7. Нажмите кнопку **Принимаю**, чтобы согласиться с условиями лицензионного соглашения.
8. Убедитесь, что в установщике веб-платформы отображена запись **Следующие продукты были успешно установлены**, и нажмите кнопку **Готово**.
9. Нажмите кнопку **Выйти**, чтобы закрыть окно установщика веб-платформы.

**Проверка правильности установки эмулятора HDInsight**
	
В ходе установки на рабочем столе должны появиться три значка. Они связаны следующим образом: 
	
- **Командная строка Hadoop**. Командная строка Hadoop для выполнения заданий MapReduce, Pig и Hive в эмуляторе HDInsight.

- **Состояние Hadoop NameNode**. NameNode обеспечивает ведение древовидного каталога для всех файлов в HDFS. Эта служба также отслеживает местонахождение данных для всех файлов в кластере Hadoop. Клиенты взаимодействуют с NameNode, чтобы узнать, где хранятся узлы данных для всех файлов.
	
- **Состояние Hadoop Yarn**. Инспектор задания, который распределяет задания MapReduce между узлами в кластере.

В ходе установки также должно быть установлено несколько локальных служб. Далее представлен снимок экрана окна "Службы":

![HDI.Emulator.Services][0]

Службы, связанные с эмулятором HDInsight, не запускаются по умолчанию. Для запуска служб запустите из командной строки Hadoop командлет **start\_local\_hdp_services.cmd** из папки <системный диск\>\hdp. Для автоматического запуска служб после перезагрузки компьютера запустите командлет **set-onebox-autostart.cmd**.  

Известные проблемы установки и запуска сервера HDInsight см. в статье [Заметки о выпуске для эмулятора HDInsight][hdinsight-emulator-release-notes]. Журнал установки находится по пути: **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**.


##<a name="runwordcount"></a>Выполнение задания на подсчет слов в MapReduce

Теперь эмулятор HDInsight настроен на рабочей станции.  Можно запустить задание MapReduce, чтобы проверить правильность установки. Сначала нужно будет отправить несколько файлов данных в HDFS, а затем запустить задание на подсчет слов MapReduce для определения частоты упоминания определенных слов в этих файлах. 

Программа подсчета слов MapReduce упакована в архив *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*.  Нужный JAR-файл находится в папке *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce*.

Задание на подсчет слов MapReduce принимает два аргумента:

- Входная папка. В качестве папки входных данных будет использоваться *hdfs://localhost/user/HDIUser*.
- Выходная папка. В качестве каталога выходных данных будет использоваться *hdfs://localhost/user/HDIUser/WordCount_Output*. Выходная папка не может быть одной из существующих папок, в противном случае задание MapReduce завершится ошибкой. Если нужно выполнить задание MapReduce во второй раз, необходимо указать другую выходную папку или удалить существующую. 

**Выполнение задания MapReduce на подсчет слов**

1. На рабочем столе дважды щелкните значок **Командная строка Hadoop**, чтобы открыть окно командной строки Hadoop.  Текущая папка должна быть следующей:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	В противном случае выполните следующую команду:

		cd %hadoop_home%

2. Выполните следующие команды Hadoop, чтобы создать папку HDFS для хранения входных и выходных файлов:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. Выполните следующую команду Hadoop, чтобы скопировать некоторые локальные текстовые файлы в HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /user/HDIUser

4. Выполните следующую команду, чтобы вывести список файлов в папке /user/HDIUser:

		hadoop fs -ls /user/HDIUser

	Вы увидите следующие файлы:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Выполните следующую команду, чтобы выполнить задание MapReduce на подсчет слов:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981> hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Выполните следующую команду, чтобы вывести из выходного файла список слов, содержащих подстроку "windows":

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	Должен быть получен следующий результат:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Дополнительные сведения о командах Hadoop см. в [справочнике по командам Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Выполнение примеров начального уровня

Установка эмулятора HDInsight содержит ряд примеров, которые позволяют пользователям быстро приступить к изучению служб на платформе Apache Hadoop в Windows. В этих примерах рассматриваются некоторые задачи, которые обычно необходимы при обработке большого набора данных. Изучая эти примеры, можно ознакомиться с понятиями, связанными с моделью программирования MapReduce и соответствующей экосистемой.

Примеры упорядочены по сценариям обработки данных журнала IIS W3C. Предоставлено средство создания данных, которое обеспечивает создание и импорт наборов данных различных размеров в HDFS или WASB (хранилище BLOB-объектов Azure). Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure для HDInsight][hdinsight-storage]). Затем можно выполнить задания MapReduce, Pig или Hive по страницам данных, которые были созданы сценарием PowerShell. Обратите внимание на то, что скрипты Pig и Hive являются уровнем абстракции над MapReduce и компилируются в программы MapReduce. Пользователи могут выполнить ряд заданий для самостоятельного анализа результатов применения этих разных технологий, а также влияния размера данных на выполнение задач обработки. 

### В этом разделе

- [Сценарии данных журнала IIS w3c](#scenarios)
- [Загрузка примера данных журнала w3c](#loaddata)
- [Выполнение заданий MapReduce на Java](#javamapreduce)
- [Выполнение заданий Hive](#hive)
- [Выполнение заданий Pig](#pig)
- [Повторное построение примеров](#rebuild)

###<a name="scenarios"></a>Сценарии данных журнала IIS w3c

Сценарий w3c создает и импортирует данные журнала IIS W3C трех размеров в HDFS или WASB: 1 МБ (маленький), 500 МБ (средний) и 2 ГБ (большой). Он предоставляет три типа заданий и реализует каждое из них на C#, Java, Pig и Hive.

- **totalhits**: вычисление общего числа запросов для конкретной страницы 
- **avgtime**: вычисление среднего времени (в секундах) для запроса на страницу 
- **errors**: вычисление числа ошибок на странице в час для запросов со статусом 404 или 500 

Эти примеры и сопутствующая документация не обеспечивают глубокого изучения или полной реализации ключевых технологий Hadoop. Используется кластер только с одним узлом, поэтому эффект от добавления дополнительных узлов в данном случае оценить невозможно. 

###<a name="loaddata"></a>Загрузка примера данных журнала w3c

Создание и импорт данных в HDFS осуществляется с использованием скрипта PowerShell importdata.ps1.

**Импорт примера данных журнала w3c:**

1. Откройте командную строку Hadoop с рабочего стола.
2. Перейдите в каталог **C:\hdp\GettingStarted**.
3. Выполните следующую команду, чтобы создать и импортировать данные в HDFS:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	Если вместо этого требуется загрузить данные в WASB, см. раздел [Подключение к хранилищу BLOB-объектов Azure](#blobstorage).

4. Выполните в командной строке Hadoop следующую команду, чтобы вывести импортированные файлы в HDFS:

		hadoop fs -ls -R /w3c

	Должен быть получен результат, аналогичный приведенному ниже: 

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Если нужно проверить содержимое файлов, выполните следующую команду, чтобы отобразить один из файлов данных в окне консоли:

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Теперь файл данных создан и импортирован в HDFS. Можно выполнить различные задания Hadoop.

###<a name="javamapreduce"></a> Выполнение заданий MapReduce на Java

MapReduce - основной механизм вычислений для Hadoop. По умолчанию он реализован на языке Java, но существуют также примеры, в которых используются .NET и Hadoop Streaming, использующие C#. Синтаксис для выполнения задания MapReduce:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

JAR-файл и исходные файлы находятся в папке C:\Hadoop\GettingStarted\Java.

**Выполнение задания MapReduce для расчета посещений веб-страницы**

1. Откройте командную строку Hadoop.
2. Перейдите в каталог **C:\hdp\GettingStarted**.
3. Выполните следующую команду, чтобы удалить выходной каталог, если такая папка уже существует.  Если выходная папка уже есть, задание MapReduce завершится ошибкой.

		hadoop fs -rm -r /w3c/output

3. Выполните следующую команду:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	В следующей таблице описаны элементы этой команды:
	<table border="1">
	<tr><td>Параметр</td><td>Примечание</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>Необходимый JAR-файл находится в папке C:\hdp\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Тип может быть заменен одним из следующих: 
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>Входной файл может быть заменен одним из следующих:
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>Это имя выходной папки.</td></tr>
	</table>

4. Выполните следующую команду, чтобы отобразить выходной файл:

		hadoop fs -cat /w3c/output/part-00000

	Результат должен быть аналогичен приведенному ниже:

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137

	Страница Default.aspx имеет 3 360 посещений и т. д. Попробуйте запустить команды снова, заменив значения, как это предлагается в таблице выше, и обратите внимание на изменения выходных данных в зависимости от типа задания и размера данных.

### <a name="hive"></a>Выполнение заданий Hive
Механизм обработки запросов Hive может показаться знакомым для аналитиков, которые имеют опыт работы с SQL. Он обеспечивает интерфейс SQL и реляционную модель данных для HDFS. Hive использует язык HiveQL, который очень похож на SQL. Hive предоставляет уровень абстракции над платформой MapReduce, в основе которой лежит Java, а запросы Hive компилируются в MapReduce во время выполнения.

**Выполнение задания Hive**

1. Откройте командную строку Hadoop.
2. Перейдите в каталог **C:\hdp\GettingStarted**.
3. Выполните следующую команду, чтобы удалить папку **/w3c/hive/input**, если такая уже существует.  Если выходная папка уже есть, задание Hive завершится ошибкой.

		hadoop fs -rmr /w3c/hive/input

4. Выполните следующую команду, чтобы создать папку **/w3c/hive/input**, а затем скопируйте в нее файлы данных:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Выполните следующую команду, чтобы запустить файл сценария **w3ccreate.hql**.  Этот скрипт создает таблицу Hive и загружает данные в эту таблицу:
        
		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	Результат должен быть аналогичен приведенному ниже:

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Выполните следующую команду, чтобы запустить файл сценария **w3ctotalhitsbypage.hql**.  

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	В следующей таблице описаны элементы этой команды:
	<table border="1">
	<tr><td>Файл</td><td>Описание</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>Командный скрипт Hive.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Файл сценария Hive можно заменить одним из следующих файлов:
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>

	Скрипт HiveQL: w3ctotalhitsbypage.hql:

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	Завершающая часть выходных данных должна иметь следующий вид:
		
		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Обратите внимание, что в качестве первого шага в каждом из заданий создается таблица, в которую загружаются данные из созданного ранее файла. Найти этот файл можно в узле /Hive в HDFS при помощи следующей команды:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Выполнение заданий Pig

При обработке данных в Pig используется язык потока данных под названием *Pig Latin*. Абстракции на Pig Latin предоставляют расширенные структуры данных по сравнению с MapReduce и играют такую же роль в Hadoop, как SQL в СУБД. 


**Выполнение заданий Pig:**

1. Откройте командную строку Hadoop.
2. Перейдите в каталог **C:\hdp\GettingStarted** папки.
3. Выполните следующую команду, чтобы отправить задание Pig:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	В следующей таблице показаны элементы этой команды:
	<table border="1">
	<tr><td>Файл</td><td>Описание</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>Командный скрипт Pig.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Файл сценария на Pig Latin можно заменить одним из следующих файлов:
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Можно заменить этот параметр файлом большего размера:
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	Должен быть получен результат, аналогичный приведенному ниже:

		(/Info.aspx,1156)
		(/UserService,1137)
		(/Default.aspx,3360)
		
Обратите внимание на то, что поскольку скрипты Pig компилируются в задания MapReduce и потенциально в несколько таких заданий, пользователи могут видеть несколько выполняемых заданий MapReduce во время обработки задания Pig.


### <a name="rebuild"></a>Повторное построение примеров
Примеры в настоящее время содержат все необходимые двоичные файлы, поэтому построение не обязательно. Если вы хотите внести изменения в образцы Java или .NET, их можно повторно построить с помощью msbuild или включенного скрипта PowerShell.

**Повторное построение примеров**

1. Откройте командную строку Hadoop.
2. Выполните следующую команду:

		powershell -F buildsamples.ps1


##<a name="blobstorage"></a>Подключение к хранилищу BLOB-объектов Azure
В качестве файловой системы по умолчанию эмулятор HDInsight использует HDFS. Однако в Azure HDInsight в качестве файловой системы по умолчанию используется хранилище BLOB-объектов Azure. Можно настроить эмулятор HDInsight таким образом, чтобы использовать хранилище BLOB-объектов Azure вместо локального хранилища. Следуйте приведенным ниже инструкциям, чтобы создать контейнер хранилища в Azure и подключить его к эмулятору HDInsight.

>[AZURE.NOTE] Для получения дополнительной информации о том, как HDInsight использует хранилище BLOB-объектов Azure, см. раздел [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

Прежде чем приступить к выполнению указанных ниже инструкций, необходимо создать учетную запись хранения. Указания см. в разделе [Создание учетной записи хранения][azure-create-storage-account].

**Создание контейнера**

1. Выполните вход на [портал управления][azure-management-portal].
2. Нажмите кнопку **ХРАНИЛИЩЕ** в левой части окна. Будет выведен список учетных записей хранения для данной подписки.
3. Выберите в списке учетную запись хранения, где требуется создать контейнер.
4. Нажмите кнопку **КОНТЕЙНЕРЫ** в верхней части страницы.
5. В нижней части страницы нажмите кнопку **ДОБАВИТЬ**.
6. Введите значение в поле **ИМЯ** и выберите **ДОСТУП**. Можно использовать любой из трех уровней доступа.  Значение по умолчанию - **Частный**.
7. Нажмите кнопку **Сохранить**, чтобы сохранить изменения. На портале будет выведен новый контейнер.

Для доступа к учетной записи хранения Azure необходимо добавить имя этой учетной записи и соответствующий ключ в файл конфигурации.

**Настройка подключения к учетной записи хранения Azure**

1. Откройте **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml** в Блокноте.
2. Добавьте следующий тег <property> рядом с другими тегами <property>:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Необходимо заменить <StorageAccountName> и <StorageAccountKey> значениями, которые соответствуют данным вашей учетной записи хранения.

3. Сохраните изменения.  Нет необходимости перезапускать службы Hadoop.

Для доступа к учетной записи хранения используется следующий синтаксис:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Например:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Выполнение HDInsight PowerShell
В эмуляторе HDInsight поддерживаются некоторые командлеты Azure HDInsight PowerShell. К ним относятся следующие командлеты:

- Командлеты определения заданий HDInsight
	
	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition                                                                                          
	- New-AzureHDInsightHiveJobDefinition                                                                                           
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Ниже приведен пример отправки задания Hadoop:

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Будет выведен запрос при вызове Get-Credential. Необходимо использовать **hadoop** в качестве имени пользователя. Паролем может быть любая строка. Имя кластера всегда имеет значение **http://localhost:50111**.

Дополнительные сведения об отправке заданий Hadoop см. в разделе [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]. Дополнительные сведения о командлетах HDInsight PowerShell см. в [справочнике по командлетам HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Удаление эмулятора HDInsight
На компьютере, где установлен эмулятор, откройте панель управления и выберите **Удаление программы** в области **Программы**. В списке установленных программ щелкните правой кнопкой мыши **Эмулятор Microsoft HDInsight для Azure**, а затем выберите пункт **Удалить**. 


##<a name="nextsteps"></a> Дальнейшие действия
При работе с этим учебником вы установили эмулятор HDInsight и выполнили некоторые задания Hadoop. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с Azure HDInsight][hdinsight-get-started]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]
- [Разработка программ MapReduce потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-deploy-streaming]
- [Заметки о выпуске эмулятора HDInsight][hdinsight-emulator-release-notes]
- [Форум MSDN с обсуждением HDInsight](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: ../hdinsight-emulator-release-notes/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-deploy-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-versions]: ../hdinsight-component-versioning/

[Powershell-install-configure]: ../install-configure-powershell/

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png 
<!--HONumber=42-->
