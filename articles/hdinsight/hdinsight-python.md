<properties
	pageTitle="Использование Python с Hive и Pig в HDInsight | Microsoft Azure"
	description="Узнайте, как использовать пользовательские функции (UDF) технологической платформы Hadoop на базе Azure — Python с Hive и Pig в HDInsight."
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
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/07/2016" 
	ms.author="larryfr"/>

#Использование Python с Hive и Pig в HDInsight

Hive и Pig отлично подходят для работы с данными в HDInsight, но в некоторых случаях вам может понадобиться язык программирования более общего назначения. Как Hive, так и Pig позволяют вам создавать пользовательские функции (UDF) с использованием различных языков программирования. В этой статье вы узнаете, как использовать пользовательские функции на Python из Hive и Pig.

##Требования

* Кластер HDInsight (под управлением Windows или Linux)

* Текстовый редактор

    > [AZURE.IMPORTANT] Если вы используете сервер HDInsight под управлением Linux, но создаете файлы Python на клиенте Windows, следует использовать редактор, который использует символ LF в качестве конца строки. Если вы не уверены, использует ли редактор символы LF или CRLF, см. раздел [Устранение неполадок](#troubleshooting), в котором описано, как удалять символы CR с помощью служебных программ в кластере HDInsight.
    
##<a name="python"></a>Python в HDInsight

По умолчанию на кластерах HDInsight 3.0 и более поздних версий установлен Python версии 2.7. Для обработки потоков Hive можно использовать с этой версией Python (данные передаются между Hive и Python с использованием STDOUT/STDIN).

В состав HDInsight также входят Jython, который представляет собой реализацию Python, написанную на Java. Pig может взаимодействовать с Jython без необходимости прибегать к использованию потоков, поэтому предпочтительнее применять его с Pig. Тем не менее вы можете использовать обычный язык Python (CPython), а также Pig.

##<a name="hivepython"></a>Hive и Python

Скрипт Python можно использовать в виде UDF из Hive через HiveQL с помощью инструкции **TRANSFORM**. Так, следующий запрос HiveQL вызывает скрипт Python, хранящийся в файле **streaming.py**.

**HDInsight под управлением Linux**

	add file wasbs:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'python streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

**HDInsight под управлением Windows**

	add file wasbs:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] В кластерах HDInsight под управлением Windows оператор **USING** должен задавать полный путь к python.exe. Это всегда `D:\Python27\python.exe`.

Вот что делает данный пример:

1. Инструкция **add file** в начале файла добавляет файл **streaming.py** в распределенный кэш, и он становится доступен всем узлам кластера.

2. Инструкция **SELECT TRANSFORM ... USING** выбирает данные из **hivesampletable** и передает clientid, devicemake и devicemodel в сценарий **streaming.py**.

3. Предложение **AS** описывает поля, возвращаемые из **streaming.py**

<a name="streamingpy"></a> Вот файл **streaming.py**, который используется в примере HiveQL.

	#!/usr/bin/env python

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

1. прочитать данные из потока STDIN. В данном примере для этого используется `sys.stdin.readline()`.

2. Стоящий в конце знак новой строки удаляется с помощью `string.strip(line, "\n ")`, так как нам нужны исключительно текстовые данные, а не символы конца строки.

2. При обработке потока в одной строке будут содержаться все значения, разделенные символом табуляции. Поэтому можно использовать `string.split(line, "\t")` для разделения входящих данных при каждой табуляции, возвращая лишь поля.

3. По завершении обработки результат должен быть записан в поток STDOUT в виде одной строки, с разделенными символами табуляции полями. Для этого используется `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Все это происходит в цикле `while`, который повторяется до тех пор, пока считывается `line`, после чего `break` выходит из цикла и сценарий завершает выполнение.

Кроме того, сценарий объединяет входные значения для `devicemake` и `devicemodel`, а также вычисляет хэш для объединенного значения. Довольно просто, но это основы функционирования сценария Python, вызванного из Hive: цикл, чтение входных данных до прекращения их поступления, разделение каждой входной строки по символам табуляции, обработка, запись одной выходной строки, разделенной символами табуляции.

См. раздел [Выполнение примеров](#running) для получения сведений о выполнении данного примера на вашем кластере HDInsight.

##<a name="pigpython"></a>Pig и Python

Скрипт Python можно использовать в виде UDF из Pig с использованием инструкции **GENERATE**. Вы можете сделать это двумя способами: с помощью Jython (язык Python, реализованный на виртуальной машине Java (JVM)) и CPython (обычный Python).

Основное различие между ними заключается в том, что Jython работает на JVM и изначально может быть вызван из Pig (также работает на JVM). CPython является внешним процессом, который написан на языке C. Поэтому данные из Pig на JVM отправляются в сценарий, выполняющийся в процессе Python. Затем результат отправляется обратно в Pig.

Чтобы определить, какой интерпретатор будет использовать Pig (Jython или CPython) для выполнения сценария, используйте команду __register__ для ссылки на сценарий Python из Pig Latin. Эта команда сообщает Pig, какой интерпретатор следует использовать и какой псевдоним создавать для сценария. Следующие примеры регистрируют сценарии с Pig в качестве __myfuncs__.

* __Для использования Jython__: `register '/path/to/pig_python.py' using jython as myfuncs;`
* __Для использования CPython__: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [AZURE.IMPORTANT] При использовании Jython путь к файлу pig\_jython может быть локальным путем или путем WASB://. Но при использовании CPython необходимо указать ссылку на файл в локальной файловой системе узла, который используется для отправки задания Pig.

После регистрации язык Pig Latin будет одинаковым для обоих примеров:

    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Вот что делает данный пример:

1. Первая строка загружает файл с демонстрационными данными **sample.log** в **LOGS**. Так как этот файл журнала не имеет согласованной схемы, она также объявляет каждую запись (в данном случае **LINE**) как массив символов **chararray**. По существу, массив строк является строкой.

2. Следующая строка отфильтровывает все пустые значения, сохраняя результат работы в **LOG**.

3. Далее выполняется итерация по всем записям в **LOG** и используется инструкция **GENERATE** для вызова метода **create\_structure**, находящегося в сценарии Python/Jython, загруженном как **myfuncs**. **LINE** используется для передачи текущей записи в функцию.

4. Наконец, выходные данные сбрасываются в поток STDOUT командой **DUMP**. Это сделано исключительно в целях немедленного вывода результатов по завершении выполнения; в реальной скрипте как правило необходимо сохранить данные командой **STORE** в новый файл.

Фактический файл сценария Python также одинаков для CPython и Jython. Единственное реальное различие заключается в том, что его необходимо импортировать из __pig\_util__ при использовании интерпретатора CPython. Вот сценарий __pig\_python.py__:

<a name="streamingpy"></a>

    # Uncomment the following if using C Python
    #from pig_util import outputSchema

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail

> [AZURE.NOTE] Вам не нужно устанавливать pig\_util. Эта служебная программа автоматически доступна для сценария.

Помните, что ранее мы определили ввод **LINE** в виде массива строк только потому, что согласованная схема для ввода отсутствует? Сценарий Python выполняет преобразование данных в согласованную схему на выходе. Работает он так:

1. Оператор **@outputSchema** задает формат данных, в котором они будут возвращаться в Pig. В данном случае это **data bag**, являющийся типом данных Pig. Корзина содержит следующие поля, все они имеют тип "Массив строк" (строки):

	* date — дата создания записи журнала;
	* date — время создания записи журнала;
	* classname — имя класса, для которого создана запись;
	* level — уровень журналирования;
	* detail — подробная информация о записи журнала.

2. Далее, **def create\_structure(input)** определяет функцию, которая будет передавать строковые элементы в Pig.

3. Данные для примера, **sample.log**, в основном соответствуют схеме даты, времени, имени класса, уровня и подробной информации, которую мы хотим возвращать. Но они также имеют несколько строк, которые начинаются со значения '*java.lang.Exception*', которые нам необходимо привести в соответствие со схемой. Инструкция **if** проверяет на наличие таких строк, затем манипулирует входными данным, переставляя строку '*java.lang.Exception*' в конец, формируя данные в соответствии с ожидаемой схемой.

4. Затем, команда **split** используется для разделения данных по первым четырем символам пробела. В результате получаем пять значений, которые присвоены **date**, **time**, **classname**, **level** и **detail**.

5. И результаты возвращаются в Pig.

Когда данные возвращаются в Pig, они будут иметь согласованную схему, определенную оператором **@outputSchema**.

##<a name="running"></a>Выполнение примеров

Если используется кластер HDInsight под управлением Linux, примените указанные ниже действия с **SSH**. Если используется кластер HDInsight под управлением Windows и клиент Windows, примените действия с **PowerShell**.

###SSH

Дополнительную информацию об использовании SSH см. в разделе <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X</a> или <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">Использование SSH с Hadoop на основе Linux в HDInsight из Windows</a>.

1. Создайте локальные копии файлов на компьютере для разработки, используя примеры Python [streaming.py](#streamingpy) и [pig\_python.py](#jythonpy).

2. Используйте `scp` для копирования файлов в кластер HDInsight. Например, следующая команда позволяет скопировать файлы в кластер с именем **mycluster**.

		scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. Используйте SSH, чтобы подключиться к кластеру. Например, следующая команда позволяет подключиться к кластеру с именем **mycluster** под пользователем **myuser**.

		ssh myuser@mycluster-ssh.azurehdinsight.net

4. В сеансе SSH добавьте переданные ранее файлы Python в хранилище WASB для кластера.

		hdfs dfs -put streaming.py /streaming.py
		hdfs dfs -put pig_python.py /pig_python.py

После передачи файлов выполните следующие действия для выполнения заданий Hive и Pig.

####Hive

1. Используйте команду `hive`, чтобы запустить оболочку Hive. После загрузки оболочки вы увидите запрос `hive>`.

2. В окне запроса `hive>` введите следующее.

		add file wasbs:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'python streaming.py' AS
		  (clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. После ввода последней строки запустится задание. В итоге возвратятся выходные данные следующего вида:

		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig,

1. Используйте команду `pig`, чтобы запустить оболочку. После загрузки оболочки вы увидите запрос `grunt>`.

2. При появлении запроса `grunt>` введите следующие инструкции, чтобы запустить сценарий Python с помощью интерпретатора Jython.

		Register wasbs:///pig_python.py using jython as myfuncs;
	    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

3. После ввода указанной строки должно запуститься задание. В итоге возвратятся выходные данные следующего вида:

		((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
		((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
		((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
		((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
		((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Используйте `quit` для выхода из оболочки Grunt, а затем следующую команду для изменения файла pig\_python.py в локальной файловой системе:

    nano pig\_python.py

5. Войдите в редактор и раскомментируйте следующую строку, удалив символ `#` в начале строки.

        #from pig_util import outputSchema

    Закончив вносить изменения, нажмите сочетание клавиш CTRL+X, чтобы выйти из редактора. Выберите Y и нажмите ВВОД, чтобы сохранить изменения.

6. Используйте команду `pig`, чтобы снова запустить оболочку. При появлении запроса `grunt>` введите следующие инструкции, чтобы запустить сценарий Python с помощью интерпретатора CPython.

        Register 'pig_python.py' using streaming_python as myfuncs;
	    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

    Когда это задание будет выполнено, вы увидите такой же результат, как при запуске сценария с помощью Jython.

###PowerShell

Для этих действий используется Azure PowerShell. Если вы еще не установили и не настроили его на компьютере для разработки, см. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md) перед выполнением следующих действий.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Создайте локальные копии файлов на компьютере для разработки, используя примеры Python [streaming.py](#streamingpy) и [pig\_python.py](#jythonpy).

2. Используйте следующий сценарий PowerShell для передачи файлов **streaming.py** и **pig\_python.py** на сервер. Подставьте имя своего кластера Azure HDInsight и путь к файлам **streaming.py** и **pig\_python.py** в первые три строки сценария.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\pig_python.py"

		$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

		#Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
        
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
		
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "pig_python.py" `
            -Container $container `
            -Context $context

	Этот скрипт получает информацию для кластера HDInsight, извлекает учетную запись и ключ для учетной записи хранения по умолчанию и загружает файлы в корневую папку контейнера.

	> [AZURE.NOTE] Другие способы передачи сценариев можно найти в документе [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).

После загрузки файлов используйте следующий скрипт PowerShell для запуска заданий. После завершения задания результат должен записаться в консоль PowerShell.

####Hive

Следующий сценарий запустит сценарий __streaming.py__. Перед запуском он предложит вам ввести сведения о HTTPS/учетной записи администратора для кластера HDInsight.

    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    
    # If using a Windows-based HDInsight cluster, change the USING statement to:
    # "USING 'D:\Python27\python.exe streaming.py' AS " +
	$HiveQuery = "add file wasbs:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'python streaming.py' AS " +
	               "(clientid string, phoneLabel string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"

	$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
    #   -Clustername $clusterName `
    #   -JobId $job.JobId `
    #   -DefaultContainer $container `
    #   -DefaultStorageAccountName $storageAccountName `
    #   -DefaultStorageAccountKey $storageAccountKey `
    #   -HttpCredential $creds `
    #   -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

Результат выполнения задания **Hive** должен выглядеть аналогично следующему:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig (Jython)

В следующем примере используется сценарий __pig\_python.py__ с помощью интерпретатора Jython. Перед запуском он предложит вам ввести сведения о HTTPS/учетной записи администратора для кластера HDInsight.

> [AZURE.NOTE] При удаленной отправке задания с помощью PowerShell нельзя использовать CPython в качестве интерпретатора.

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$PigQuery = "Register wasbs:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"

	$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
        
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

Результат выполнения задания **Pig** должен выглядеть аналогично следующему:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Устранение неполадок

###Ошибки при выполнении заданий

При выполнении задания hive может возникнуть ошибка, аналогичная приведенной ниже:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
    
Эта проблема может быть вызвана символами окончания строк в файле streaming.py. Многие редакторы Windows по умолчанию используют символы CRLF, но в приложениях Linux обычно ожидается использование символа LF.

Если вы применяете редактор, который не может использовать символ LF в качестве конца строки, или не уверены в том, какой символ конца строки используется, используйте следующие команды PowerShell для удаления символов CR перед загрузкой файла в HDInsight:

    $original_file ='c:\path\to\streaming.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)

###Сценарии PowerShell

Оба примера скриптов PowerShell, используемых для запуска примеров, содержат закомментированную строку, которая отображает вывод ошибок для задания. Если вы не видите ожидаемых результатов задания, раскомментируйте следующую строку и просмотрите информацию об ошибках на предмет отображения проблемы.

	# Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Информация об ошибках (STDERR,) и результаты выполнения задания (STDOUT,) также записываются в контейнер больших двоичных объектов данных по умолчанию для ваших кластеров в следующих расположениях.

Для данного задания...|Смотрите эти файлы в контейнере
---|---
Hive|/HivePython/stderr<p>/HivePython/stdout
Pig,|/PigPython/stderr<p>/PigPython/stdout

##<a name="next"></a>Дальнейшие действия

Если вам нужно загрузить модули Python, которые не поставляются по умолчанию, см раздел [Развертывание модуля в Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) для примера того, как сделать это.

Другие способы использования Pig и Hive и дополнительная информация об использовании MapReduce:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)

* [Использование Pig с HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->