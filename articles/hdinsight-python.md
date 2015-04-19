<properties 
	pageTitle="Использование Python с Hive и Pig в Azure HDInsight" 
	description="Вы узнаете, как использовать пользовательские функции (UDF) на Python с Hive и Pig в Azure HDInsight" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#Использование Python с Hive и Pig в HDInsight

Hive и Pig отлично подходят для работы с данными в HDInsight, но в некоторых случаях вам может понадобиться язык программирования более общего назначения. Как Hive, так и Pig позволяют вам создавать пользовательские функции (UDF) с использованием различных языков программирования. В этой статье вы узнаете, как использовать пользовательские функции на Python из Hive и Pig.

> [AZURE.NOTE] Шаги, указанные в данной статье, относятся к кластеру HDInsight версий 2.1, 3.0 и версии 3.1.

##Оглавление

* [Python в HDInsight](#python)
*   [Hive и Python](#hivepython)
*   [Pig и Python](#pigpython)
* [Выполнение примеров](#running)
* [Устранение неполадок](#troubleshooting)
* [Дальнейшие действия](#next)

##<a name="python"></a>Python на HDInsight

По умолчанию на кластерах HDInsight 3.0 установлен Python версии 2.7. Установка произведена в папку D:\Python. Для обработки потоков Hive можно использовать с этой версией Python (данные передаются между Hive и Python с использованием STDOUT/STDIN).

В состав HDInsight также входят Jython, который представляет собой реализацию Python, написанную на Java. Pig может взаимодействовать с Jython без необходимости прибегать к использованию потоков, поэтому предпочтительнее применять его с Pig.

###<a name="hivepython"></a>Hive и Python

Python можно использовать в качестве определяемой пользователем функции из Hive через инструкцию HiveQL **TRANSFORM**. Так, следующий запрос HiveQL вызывает скрипт Python, хранящийся в файле **streaming.py**.

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

Вот что делает данный пример:

1. Инструкция **add file** в начале файла добавляет файл **streaming.py** в распределенный кэш, и он становится доступен всем узлам кластера.

2. Инструкция   **SELECT TRANSFORM ... USING 'D:\Python27\python.exe streaming.py'** выбирает данные из **hivesampletable** и передает clientid, devicemake и devicemodel в скрипт **streaming.py**.

	> [AZURE.NOTE] Предложение **USING** указывает полный путь к python.exe, если он отсутствует в значении переменной окружения path.

3. Предложение **AS** описывает поля, возвращаемые из **streaming.py**


<a name="streamingpy"></a>
Это файл **streaming.py**, который используется в примере HiveQL.

	import sys
	import string
	import hashlib
	
	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break
	
	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Так как мы используем потоки, этот скрипт должен выполнить следующее:

1. прочитать данные из потока STDIN. В данном примере это достигается использованием  `sys.stdin.readline()`.

2. Стоящий в конце символ перевода на новую строку удаляется с использованием `string.strip(line, "\n ")`, т.к. нам нужны исключительно текстовые данные, а не символы перевода строки.

2. При обработке потока в одной строке будут содержаться все значения, разделенные символом табуляции. Поэтому можно использовать `string.split(line, "\t")` для разделения входящих данных при каждой табуляции, возвращая лишь значения.

3. По завершении обработки результат должен быть записан в поток STDOUT в виде одной строки, с разделенными символами табуляции полями. Это достигается с помощью `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Все это происходит в цикле `while`, который повторяется до тех пор, пока считывается `line`, после чего `break` выходит из цикла и скрипт завершает выполнение.

Кроме того, сценарий просто объединяет входные значения для `devicemake` и `devicemodel`, и вычисляет хэш для объединенного значения. Довольно просто, но тем не менее мы описали основы того, как должен работать вызов любого скрипта Python из Hive: Цикл, чтение входных данных до прекращения их поступления, разделение каждой входной строки символами табуляции обработка, запись одной выходной строки, разделенной символами табуляции.

См. раздел [Выполнение примеров] (#running) для получения сведений о выполнении данного примера на кластере HDInsight.

###<a name="pigpython"></a>Pig и Python

Сценарий Python можно использовать в качестве определяемой пользователем функции из Pig с помощью инструкции **GENERATE**. Так, в следующем примере используется скрипт Python, хранящийся в файле **jython.py**.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Вот как работает данный пример:

1. Он регистрирует файл,, в котором содержится скрипт Python  (**jython.py**,) с помощью **Jython** и представляет его для Pig в виде **myfuncs**. Jython - это реализация Python на Java, запускается в той же виртуальной машине, что и Pig. Это позволяет расценивать скрипт Python как обычный вызов функции, в отличие от потокового подхода, используемого в Hive.

2. Следующая строка загружает файл с выборками данных, **sample.log**, в **LOGS**. Так как этот файл журнала не имеет согласованной схемы, она также объявляет каждую запись (в данном случае **LINE**) как **chararray**. По существу, массив строк является строкой.

3. Третья строка отфильтровывает все пустые значения, сохраняя результат работы в **LOG**.

4. Далее, выполняется проход по всем записям в **LOG** и используется инструкция **GENERATE** для вызова метода **create_structure**, находящегося в скрипте **jython.py**, загруженном как **myfuncs**.  **LINE** используется для передачи в функцию текущей записи.

5. Наконец, выходные данные сбрасываются в поток STDOUT командой **DUMP**. Это сделано исключительно в целях немедленного вывода результатов по завершении выполнения; в реальном скрипте как правило необходимо сохранить данные в новый файл командой **STORE**.

<a name="jythonpy"></a>
Это файл **jython.py**, который используется в примере Pig:

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

Помните, что ранее мы определили ввод **LINE** в виде массива строк только потому, что согласованная схема для ввода отсутствует? Скрипт **jython.py** выполняет преобразование данных в согласованную схему на выходе. Работает он так:

1. Инструкция **@outputSchema** задает формат данных, в котором они будут возвращаться в Pig. В данном случае это **data bag**, являющийся типом данных Pig. Корзина содержит следующие поля, все они имеют тип "Массив строк" (строки):

	* date - дата создания записи журнала;
	* time - время создания записи журнала;
	* classname - имя класса, для которого создана запись;
	* level - уровень журналирования;
	* detail - подробная информация о записи журнала.

2. Далее, **def create_structure(input)** определяет функцию, которая будет передавать строковые элементы в Pig.

3. Данные для примера, **sample.log**, в основном соответствуют схеме даты, времени, имени класса, уровня и подробной информации, которую мы хотим возвращать. Но они также имеют несколько строк, которые начинаются со значения '*java.lang.Exception*', которые нам необходимо привести в соответствие со схемой. Инструкция **if** проверяет на наличие таких строк, затем манипулирует входными данным, переставляя строку '*java.lang.Exception*' в конец, формируя данные в соответствии с ожидаемой схемой.

4. Затем, команда **split** используется для разделения данных по первым четырем символам пробела. В результате получаем пять значений, которые присвоены **date**, **time**, **classname**, **level** и **detail**.

5. И результаты возвращаются в Pig.

Когда данные возвращаются в Pig, они будут иметь согласованную схему, определенную инструкцией **@outputSchema**.

См. раздел [Выполнение примеров] (#running) для получения сведений о выполнении данного примера на кластере HDInsight.

##<a name="running"></a>Выполнение примеров

Для выполнения этих действий используется Windows Azure PowerShell. Если вы еще не установили и не настроили его на компьютере для разработки, см. статью [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) перед выполнением следующих действий.


1. Используя примеры на языке Python, [streaming.py](#streamingpy) и [jython.py](#jythonpy), создайте локальные копии файлов на компьютере разработки.

2. Используйте   следующий скрипт PowerShell для загрузки файлов **streaming.py** и **jython.py** на сервер. Подставьте имя вашего кластера Azure HDInsight и путь к файлам **streaming.py** и **jython.py** в первые три строки скрипта.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	Этот скрипт получает информацию для кластера HDInsight, извлекает учетную запись и ключ для учетной записи хранения по умолчанию и загружает файлы в корневую папку контейнера.

	> [AZURE.NOTE] Другие способы загрузки скриптов можно найти в документе [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).

###Использование панели мониторинга Hive (только для примера с Hive)

1. После загрузки файла откройте браузер и перейдите по адресу https://YourClusterName.azurehdinsight.net/. Когда появится запрос на ввод учетных данных, введите имя пользователя и пароль администратора кластера.

	> [AZURE.NOTE] Также можно воспользоваться ссылкой **Управление кластером** в нижней части **Панели мониторинга** HDInsight на портале управления Azure для запуска панели мониторинга Hive.

2. Используя **Редактор Hive**, замените строку `select * from hivesampletable` следующим кодом HiveQL.

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Чтобы отправить задание, нажмите кнопку **Отправить**. В зависимости от версии кластера HDInsight вы можете быть перенаправлены на страницу сведений о задании. В противном случае выберите **Просмотр сведений** в области **Сеанс задания** в нижней части станицы.

4. Страница **Сведения о задании** будет обновляться до завершения выполнения задания. После завершения будет отображена информация о задании, а также выходные данные.

###Использование PowerShell (примеры Hive и Pig)

После загрузки файлов используйте следующий скрипт PowerShell для запуска заданий. После завершения задания результат должен записаться в консоль PowerShell.

**Выполнение задания Hive**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

Результат выполнения задания **Hive** должен выглядеть аналогично следующему:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**Выполнение задания Pig**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

Результат выполнения задания **Pig** должен выглядеть аналогично следующему:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Устранение неполадок

Оба примера скриптов PowerShell, используемых для запуска примеров, содержат закомментированную строку, которая отображает вывод ошибок для задания. Если вы не видите ожидаемых результатов задания, раскомментируйте следующую строку и просмотрите информацию об ошибках на предмет отображения проблемы.

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

Информация об ошибках (STDERR,) и результаты выполнения задания (STDOUT,) также записываются в контейнер больших двоичных объектов данных по умолчанию для ваших кластеров в следующих расположениях.

<table>
<tr>
<td>Для данного задания...</td><td>Смотрите эти файлы в контейнере</td>
</tr>
<td>Hive</td><td>/HivePython/stderr</br>/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr</br>/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>Дальнейшие действия

Если вам необходимо загрузить модули Python, которые не предоставляются по умолчанию, см. раздел [Развертывание модуля в Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) для примера того, как это сделать.

Если вы хотите удаленно запустить задания на HDInsight без использования PowerShell, см. раздел [Использование Azure HDInsight в Linux](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx) для примера использования Python для запуска заданий через интерфейс API REST WebHCat.
<!--HONumber=42-->
