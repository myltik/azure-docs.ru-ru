---
title: "Разработка заданий Python MapReduce в HDInsight | Документация Майкрософт"
description: "Информация о создании и выполнении заданий Python MapReduce в кластерах HDInsight на платформе Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6ce490fb903d4ed2177b95145bb98fb3eeb0654f
ms.lasthandoff: 03/25/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>Разработка программ потоковой передачи на Python для HDInsight

Hadoop предоставляет API-интерфейс для MapReduce, позволяющий создавать функции map и reduce на языках, отличных от Java. В этой статье вы узнаете, как использовать Python для выполнения операций MapReduce.

Эта статья основана на информации и примерах, опубликованных Майклом Ноллом (Michael Noll) в статье [Составление программы Hadoop MapReduce на языке Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Hadoop в кластере HDInsight на платформе Linux

  > [!IMPORTANT]
  > Для выполнения действий, описанных в этом документе, необходим кластер HDInsight, который использует Linux. Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Текстовый редактор
  
  > [!IMPORTANT]
  > Текстовый редактор должен использовать LF в качестве конца строки. Если он использует CRLF, это приведет к ошибкам при выполнении задания MapReduce в кластерах HDInsight под управлением Linux. Если вы не уверены, выполните необязательный шаг в разделе [Выполнение MapReduce](#run-mapreduce) , чтобы преобразовать все CRLF в LF.

* **Знакомство с SSH и SCP**. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="word-count"></a>Статистика

В этом примере реализуется простая функция подсчета слов с помощью модулей сопоставления и сжатия. Модуль сопоставления делит предложение на отдельные слова, а модуль сжатия объединяет слова и подсчитывает их количество для вывода выходных данных, т. е. результата.

На следующей блок-схеме показано, что происходит на этапах сопоставления и сжатия.

![изображение MapReduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>Причины использования Python

Python — это высокоуровневый язык программирования общего назначения, позволяющий выразить основные понятия с помощью меньшего числа строк кода, чем это можно сделать на многих других языках. Недавно он стал довольно популярным среди специалистов по обработке и анализу данных, которые используют его в качестве языка для создания прототипов. Благодаря своему интерпретируемому характеру, динамической типизации и изящному синтаксису он прекрасно подходит для быстрой разработки приложений.

Python устанавливается на всех кластерах HDInsight.

## <a name="streaming-mapreduce"></a>Потоковая передача MapReduce

Hadoop позволяет указать файл, содержащий логику сопоставления и сжатия, используемую для выполнения задания. К сопоставлению и сжатию выдвигаются определенные требования.

* **Входные данные**— компоненты сопоставления и сжатия должны считывать входные данные с STDIN.
* **Выходные данные**— компоненты сопоставления и сжатия должны записывать выходные данные в STDOUT.
* **Формат данных**— полученные и созданные данные должны быть парой «ключ-значение», разделенной символом табуляции.

Python позволяет с легкостью выполнить эти требования, используя модуль **sys** для чтения с STDIN и **print** для вывода в STDOUT. Остается просто отформатировать данные, вставив символ табуляции (`\t`) между ключом и значением.

## <a name="create-the-mapper-and-reducer"></a>Создание средств сопоставления и приведения

Модули сопоставления и сжатия — это текстовые файлы. В данном случае **mapper.py** — это модуль сопоставления, а **reducer.py** — модуль сжатия (чтобы вам было понятнее, что делает каждый из них). Для их создания можете воспользоваться любым текстовым редактором по своему усмотрению.

### <a name="mapperpy"></a>Mapper.py

Создайте новый файл с именем **mapper.py** и в качестве его содержимого используйте следующий код:

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

Просмотрите код, чтобы понять, что он делает.

### <a name="reducerpy"></a>Reducer.py

Создайте новый файл с именем **reducer.py** и в качестве его содержимого используйте следующий код:

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>Передача файлов

Для запуска файлов **mapper.py** и **reducer.py** оба они должны быть на головном узле кластера. В этом разделе приведен пример команды `scp` и скрипт Azure PowerShell, с помощью которых можно передавать файлы в кластер.

> [!IMPORTANT]
> Использование команды `scp` и PowerShell для передачи файлов существенно отличаются.
>
> * Если используется команда `scp`, файлы помещаются в головной узел кластера. При этом предполагается, что позже вы подключитесь к головному узлу и запустите задание из сеанса SSH.
> * При использовании скрипта PowerShell файлы помещаются в стандартное хранилище кластера. При этом предполагается, что позже вы запустите задание с удаленного клиентского компьютера с помощью скрипта PowerShell.

### <a name="upload-using-scp"></a>Передача с помощью SCP

Используйте следующую команду в среде развертывания (в том же каталоге, в котором расположены файлы **mapper.py** и **reducer.py**). Замените **username** именем пользователя SSH для кластера, а **clustername** — именем кластера.

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

В результате файлы будут скопированы из локальной системы на головной узел.

> [!NOTE]
> Если для защиты учетной записи SSH использовался пароль, будет предложено ввести пароль. Если использовался ключ SSH, возможно, нужно будет использовать параметр `-i` и указать путь к закрытому ключу, например `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

### <a name="upload-using-powershell"></a>Отправка с помощью PowerShell

1. В среде развертывания создайте новый файл с именем `Put-FilesInHDInsight.ps1` и следующим содержимым:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Чтобы отправить файл с помощью этого скрипта, выполните следующую команду в командной строке Azure PowerShell:

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    При появлении запроса введите учетные данные пользователя HTTP для кластера.

3. Повторите команду, заменив `mapper.py` на `reducer.py`. Файлы модуля сопоставления и модуля сжатия будут отправлены в стандартное хранилище кластера.

## <a name="run-mapreduce-ssh"></a>Выполнение задания MapReduce (SSH)

Чтобы подключиться к кластеру и запустить задание потоковой передачи MapReduce из сеанса SSH, выполните следующие действия.

1. Подключитесь к кластеру с помощью SSH:
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > Если для защиты учетной записи SSH использовался пароль, будет предложено ввести пароль. Если использовался ключ SSH, возможно, нужно будет использовать параметр `-i` и указать путь к закрытому ключу, например `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Необязательно.) Если вы использовали текстовый редактор, который использует CRLF в качестве конца строки, при создании файлов mapper.py и reducer.py, или не знаете, что редактор использует в качестве конца строки, воспользуйтесь следующими командами для преобразования вхождений CRLF в файлах mapper.py и reducer.py в символы LF.
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. Используйте следующую команду для запуска задания MapReduce.
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    Эта команда состоит из следующих частей:
   
   * **hadoop streaming.jar**— используется при выполнении операций потоковой передачи MapReduce. Эта часть обеспечивает взаимодействие Hadoop с предоставленным внешним кодом MapReduce.

   * **-files**— сообщает Hadoop, что для этого задания MapReduce необходимы указанные файлы, которые нужно скопировать на все рабочие узлы.

   * **-mapper**— сообщает Hadoop о том, какой файл следует использовать в качестве модуля сопоставления.

   * **-reducer**— сообщает Hadoop о том, какой файл следует использовать в качестве модуля сжатия.

   * **-input**— входной файл, в котором нужно подсчитать слова.

   * **-output**— каталог, в который будут записаны выходные данные.
     
     > [!NOTE]
     > Этот каталог будет создан при выполнении задания.

При запуске задания появится много инструкций **INFO**, а в конце будет отображаться процент выполнения операций **сопоставления** и **сжатия**.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

После завершения задания вы получите информацию о его состоянии.

## <a name="run-mapreduce-powershell"></a>Выполнение задания MapReduce (PowerShell)

Чтобы запустить задание потоковой передачи MapReduce из PowerShell в среде разработки, выполните следующие действия. Скрипт PowerShell запускает задание, подключившись к кластеру HDInsight с помощью WebHCat.

1. Создайте файл с именем `Start-HDInsightStreamingPythonJob` и следующим содержимым:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Выполните следующую команду в командной строке Azure PowerShell, чтобы запустить задание:

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    В этой команде будут использоваться файлы `mapper.py` и `reducer.py`, ранее отправленные в кластер для подсчета слов в файле `davinci.txt`. Выходные данные сохраняются в папке `/example/wordcount` в стандартном хранилище кластера.

    После завершения задания отобразятся примерно такие данные:

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>Просмотр результатов

Выходные данные задания сохраняются в каталоге `/example/wordcountout`. Вы можете просмотреть их из сеанса SSH или загрузить их локально и просмотреть в PowerShell.

Чтобы просмотреть данные кластера из сеанса SSH, выполните следующую команду:

`hdfs dfs -text /example/wordcountout/part-00000`

После этого отобразится список слов и частота их появления. Ниже приведен пример выходных данных:

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

Чтобы просмотреть выходные данные в среде разработки с помощью PowerShell, выполните следующие действия.

1. Создайте файл с именем `Get-FilesInHDInsight.ps1` и следующим содержимым:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. В командной строке Azure PowerShell выполните следующую команду, чтобы запустить скрипт и просмотреть выходные данные:

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    После этого отобразится список слов и частота их появления. Ниже приведен пример выходных данных:

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать задания потоковой передачи MapReduce в HDInsight, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)


