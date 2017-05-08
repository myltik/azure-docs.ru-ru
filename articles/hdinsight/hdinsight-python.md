---
title: "Использование Python с Hive и Pig в HDInsight | Документация Майкрософт"
description: "Узнайте, как использовать пользовательские функции (UDF) технологической платформы Hadoop на базе Azure — Python с Hive и Pig в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 985f14ce7b8249c8e80deb1851cfee8c89651786
ms.lasthandoff: 04/27/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Использование пользовательских функций Python с Hive и Pig в Azure HDInsight

Hive и Pig отлично подходят для работы с данными в HDInsight, но в некоторых случаях вам может понадобиться язык программирования более общего назначения. Как Hive, так и Pig позволяют вам создавать пользовательские функции с использованием многих языков программирования. В этой статье вы узнаете, как использовать пользовательские функции на Python из Hive и Pig.

## <a name="requirements"></a>Требования

* Кластер HDInsight.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Текстовый редактор

## <a name="python"></a>Python в HDInsight

По умолчанию на кластерах HDInsight 3.0 и более поздних версий установлен Python версии 2.7. Для обработки потоков Hive можно использовать с этой версией Python (данные передаются между Hive и Python с использованием STDOUT/STDIN).

В состав HDInsight также входят Jython, который представляет собой реализацию Python, написанную на Java. Pig может взаимодействовать с Jython без необходимости прибегать к использованию потоков, поэтому предпочтительнее применять его с Pig. Вы также можете использовать обычный язык Python (CPython) с Pig.

## <a name="hivepython"></a>Hive и Python

Скрипт Python можно использовать в виде UDF из Hive через HiveQL с помощью инструкции **TRANSFORM** . Так, следующий запрос HiveQL вызывает скрипт Python, хранящийся в файле **streaming.py** .

**HDInsight под управлением Linux**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight под управлением Windows**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> В кластерах HDInsight под управлением Windows оператор **USING** должен задавать полный путь к python.exe.

Вот что делает данный пример:

1. Инструкция **add file** в начале файла добавляет файл **streaming.py** в распределенный кэш, и он становится доступен всем узлам кластера.
2. Инструкция **SELECT TRANSFORM ... USING** выбирает данные из **hivesampletable**. Он также передает параметры clientid, devicemake и devicemodel в скрипт **streaming.py**.
3. Предложение **AS** описывает поля, возвращаемые из **streaming.py**.

<a name="streamingpy"></a> Вот файл **streaming.py** , который используется в примере HiveQL.

```python
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
```

Сценарий выполняет следующие действия:

1. Чтение данных из STDIN.
2. Стоящий в конце знак новой строки удаляется с помощью `string.strip(line, "\n ")`.
3. При обработке потока в одной строке будут содержаться все значения, разделенные символом табуляции. Поэтому можно использовать `string.split(line, "\t")` для разделения входящих данных при каждой табуляции, возвращая лишь поля.
4. По завершении обработки результат должен быть записан в поток STDOUT в виде одной строки, с разделенными символами табуляции полями. Для этого используется `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Цикл `while` повторяется до тех пор, пока считывается `line`.

Выходные данные скрипта представляют собой объединенные входные значения для `devicemake` и `devicemodel`, а также хэш для объединенного значения.

Сведения о выполнении этого примера в кластере HDInsight см. в разделе [Выполнение примеров](#running).

## <a name="pigpython"></a>Pig и Python

Скрипт Python можно использовать в виде UDF из Pig с использованием инструкции **GENERATE** . Вы можете запустить скрипт с помощью Jython или CPython.

Различие между ними заключается в том, что Jython работает на JVM и изначально может быть вызван из Pig. CPython является внешним процессом, поэтому данные из Pig на JVM отправляются в скрипт, выполняющийся в процессе Python. Выходные данные скрипта Python отправляются обратно в Pig.

Чтобы определить, какой интерпретатор будет использовать Pig (Jython или CPython) для выполнения скрипта, используйте команду **register** при выполнении скрипта Python из Pig Latin. Следующие примеры регистрируют скрипты с Pig в качестве **myfuncs**.

* **Для использования Jython:** `register '/path/to/pig_python.py' using jython as myfuncs;`
* **Для использования CPython:** `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> При использовании Jython путь к файлу pig_jython может быть локальным путем или путем WASB://. Но при использовании CPython необходимо указать ссылку на файл в локальной файловой системе узла, который используется для отправки задания Pig.

После регистрации язык Pig Latin будет одинаковым для обоих примеров:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Вот что делает данный пример:

1. Первая строка загружает файл с демонстрационными данными **sample.log** в **LOGS**. Она также определяет каждую запись как массив символов **chararray**.
2. Следующая строка отфильтровывает все пустые значения, сохраняя результат работы в **LOG**.
3. Далее выполняется проход по всем записям в **LOG** и используется инструкция **GENERATE** для вызова метода **create_structure**, находящегося в скрипте Python или Jython, загруженном как **myfuncs**.  **LINE** используется для передачи текущей записи в функцию.
4. Наконец, выходные данные сбрасываются в поток STDOUT командой **DUMP** . После завершения операции будет выведен результат.

Файл скрипта Python аналогичен для CPython и Jython. Единственное различие заключается в том, что при использовании интерпретатора CPython его необходимо импортировать из **pig\_util**. Ниже приведен пример скрипта **pig\_python.py**:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

> [!NOTE]
> Вам не нужно устанавливать pig_util. Эта служебная программа автоматически доступна для сценария.

Помните, что ранее мы определили ввод **LINE** в виде массива строк только потому, что согласованная схема для ввода отсутствует? Скрипт Python выполняет преобразование данных в согласованную схему на выходе.

1. Оператор **@outputSchema** задает формат данных, в котором они возвращаются в Pig. В данном случае это **data bag**, являющийся типом данных Pig. Корзина содержит следующие поля, все они имеют тип "Массив строк" (строки):

   * date — дата создания записи журнала;
   * date — время создания записи журнала;
   * classname — имя класса, для которого создана запись;
   * level — уровень журналирования;
   * detail — подробная информация о записи журнала.

2. Далее **def create_structure(input)** определяет функцию, используемую для передачи строковых элементов в Pig.

3. Данные для примера, **sample.log**, в основном соответствуют схеме даты, времени, имени класса, уровня и подробной информации, которую мы хотим возвращать. Но они также имеют несколько строк, которые начинаются со значения '*java.lang.Exception*', которые нам необходимо привести в соответствие со схемой. Инструкция **if** проверяет на наличие таких строк, затем манипулирует входными данным, переставляя строку '*java.lang.Exception*' в конец, формируя данные в соответствии с ожидаемой схемой.

4. Затем, команда **split** используется для разделения данных по первым четырем символам пробела. Выходным данным присваиваются значения **date**, **time**, **classname**, **level** и **detail**.

5. И результаты возвращаются в Pig.

Когда данные возвращаются в Pig, они имеют согласованную схему, определенную оператором **@outputSchema**.

## <a name="running"></a>Выполнение примеров
Если используется кластер HDInsight под управлением Linux, примените указанные действия с **SSH**. Если используется кластер HDInsight под управлением Windows и клиент Windows, примените действия с **PowerShell** .

### <a name="ssh"></a>SSH

Дополнительные сведения об использовании SSH см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

1. Создайте локальные копии файлов на компьютере для разработки, используя примеры Python [streaming.py](#streamingpy) и [pig_python.py](#jythonpy).

2. Используйте `scp` для копирования файлов в кластер HDInsight. Например, следующая команда позволяет скопировать файлы в кластер с именем **mycluster**.

        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. Используйте SSH, чтобы подключиться к кластеру. Например, следующая команда позволяет подключиться к кластеру с именем **mycluster** от имени пользователя **myuser**.

        ssh myuser@mycluster-ssh.azurehdinsight.net
4. В сеансе SSH добавьте переданные ранее файлы Python в хранилище WASB для кластера.

        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

После передачи файлов выполните следующие действия для выполнения заданий Hive и Pig.

#### <a name="hive"></a>Hive

1. Используйте команду `hive` , чтобы запустить оболочку Hive. После загрузки оболочки вы увидите запрос `hive>` .
2. В окне запроса `hive>` введите следующее.

   ```hive
   add file wasbs:///streaming.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python streaming.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```
3. После ввода последней строки запустится задание. По завершении задания эта команда возвращает выходные данные следующего вида:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig"></a>Pig,

1. Используйте команду `pig` , чтобы запустить оболочку. После загрузки оболочки вы увидите запрос `grunt>` .

2. В окне запроса `grunt>` введите следующие операторы:

   ```pig
   Register wasbs:///pig_python.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. После ввода указанной строки должно запуститься задание. По завершении задания эта команда возвращает выходные данные следующего вида:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Используйте `quit` для выхода из оболочки Grunt, а затем следующую команду для изменения файла pig_python.py в локальной файловой системе:

    nano pig_python.py

5. Войдите в редактор и раскомментируйте следующую строку, удалив символ `#` в начале строки.

        #from pig_util import outputSchema

    Закончив вносить изменения, нажмите сочетание клавиш CTRL+X, чтобы выйти из редактора. Выберите Y и нажмите ВВОД, чтобы сохранить изменения.

6. Используйте команду `pig` , чтобы снова запустить оболочку. При появлении запроса `grunt>` введите следующие инструкции, чтобы запустить сценарий Python с помощью интерпретатора CPython.

   ```pig
   Register 'pig_python.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Когда это задание будет выполнено, вы увидите такой же результат, как при запуске сценария с помощью Jython.

### <a name="powershell"></a>PowerShell

Для этих действий используется Azure PowerShell. Дополнительные сведения об использовании Azure PowerShell см. в статье [How to install and configure Azure PowerShell](/powershell/azure/overview) (Как установить и настроить Azure PowerShell).

1. Создайте локальные копии файлов на компьютере для разработки, используя примеры Python [streaming.py](#streamingpy) и [pig_python.py](#jythonpy).
2. Для передачи файлов **streaming.py** и **pig\_python.py** на сервер используйте следующий скрипт PowerShell. Подставьте имя своего кластера Azure HDInsight и путь к файлам **streaming.py** и **pig\_python.py** в первые три строки скрипта.

   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
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
   ```

    Этот скрипт получает информацию для кластера HDInsight, извлекает учетную запись и ключ для учетной записи хранения по умолчанию и загружает файлы в корневую папку контейнера.

   > [!NOTE]
   > Другие способы передачи сценариев можно найти в документе [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md) .

После загрузки файлов используйте следующий скрипт PowerShell для запуска заданий. После завершения задания результат должен записаться в консоль PowerShell.

#### <a name="hive"></a>Hive

Следующий скрипт запускает скрипт **streaming.py**. Перед запуском он предлагает вам ввести сведения об HTTPS и учетной записи администратора для кластера HDInsight.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

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
#   -HttpCredential $creds `
#   -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Результат выполнения задания **Hive** должен выглядеть аналогично следующему примеру:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

В следующем примере используется скрипт **pig_python.py** с интерпретатором Jython. Перед запуском он предлагает вам ввести сведения об HTTPS и учетной записи администратора для кластера HDInsight.

> [!NOTE]
> При удаленной отправке задания с помощью PowerShell нельзя использовать CPython в качестве интерпретатора.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

$PigQuery = "Register wasbs:///pig_python.py using jython as myfuncs;" +
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
#    -Clustername $clusterName `
#    -JobId $job.JobId `
#    -HttpCredential $creds `
#    -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Результат выполнения задания **Pig** должен выглядеть аналогично следующему:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="errors-when-running-jobs"></a>Ошибки при выполнении заданий

При выполнении задания hive может возникнуть ошибка, аналогичная приведенной ниже:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Эта проблема может быть вызвана символами окончания строк в файле streaming.py. Многие редакторы Windows по умолчанию используют символы CRLF, но в приложениях Linux обычно ожидается использование символа LF.

Вы можете использовать следующие команды PowerShell для удаления символов CR перед передачей файла в HDInsight:

```powershell
$original_file ='c:\path\to\streaming.py'
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

### <a name="powershell-scripts"></a>Сценарии PowerShell

Оба примера скриптов PowerShell, используемых для запуска примеров, содержат закомментированную строку, которая отображает вывод ошибок для задания. Если вы не видите ожидаемых результатов задания, раскомментируйте следующую строку и просмотрите информацию об ошибках на предмет отображения проблемы.

```powershell
# Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Сведения об ошибках (STDERR) и результат выполнения задания (STDOUT) также записываются в хранилище HDInsight.

| Для данного задания... | Смотрите эти файлы в контейнере |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig, |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Дальнейшие действия

Если вам нужно загрузить модули Python, которые не поставляются по умолчанию, см. статью [How to deploy a Python module to Windows Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Как развернуть модуль Python в Windows Azure HDInsight).

Сведения о других способах использования Pig и Hive и дополнительную информацию об использовании MapReduce см. в следующих документах:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

