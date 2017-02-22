---
title: "Использование Pig в Hadoop в HDInsight с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как отправлять задания Pig в кластер Hadoop в HDInsight с помощью Azure PowerShell."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8b88db5bb2c8153953109fcd0511c22042bcf931
ms.openlocfilehash: bef30df14f6d00c4a7f5f5b3d59ec85b2e4fbe10


---
# <a name="run-pig-jobs-using-powershell"></a>Выполнение заданий Pig с помощью PowerShell
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования Azure PowerShell для отправки заданий Pig в Hadoop в кластере HDInsight. Pig позволяет написать задания MapReduce с использованием языка (Pig Latin), который моделирует преобразования данных, а не функции сопоставления и приведения.

> [!NOTE]
> В этом документе не приводится подробное описание процессов, которые выполняют операторы Pig Latin, используемые в примерах. Информацию об операторах Pig Latin, используемых в данном примере, см. в статье [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).
> 
> 

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* **Кластер Azure HDInsight**.

  > [!IMPORTANT]
  > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **Рабочая станция с Azure PowerShell.**.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="a-idpowershellarun-pig-jobs-using-powershell"></a><a id="powershell"></a>Выполнение заданий Pig с помощью PowerShell
Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать задания Pig в HDInsight. Внутренне это достигается с помощью выполнения вызовов REST для [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (прежнее название — Templeton) на кластере HDInsight.

При выполнении заданий Pig на удаленном кластере HDInsight используются следующие командлеты:

* **Login-AzureRmAccount**— выполняет аутентификацию Azure PowerShell для подписки Azure.
* **New-AzureRmHDInsightPigJobDefinition**— создает новое *определение задания* с использованием заданных операторов Pig Latin.
* **Start-AzureRmHDInsightJob**— отправляет определение задания в HDInsight, запускает задание и возвращает объект- *задание* , который можно использовать для проверки состояния задания.
* **Wait-AzureRmHDInsightJob**— использует объект-задание для проверки состояния задания. Он будет ждать завершения задания или превышения времени ожидания.
* **Get-AzureRmHDInsightJobOutput**— используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight.

1. С помощью редактора сохраните следующий код как **pigjob.ps1**.
   
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

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"

        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds


1. Откройте командную строку Windows PowerShell. Перейдите к расположению файла **pigjob.ps1** , а затем используйте следующую команду для запуска сценария:
   
        .\pigjob.ps1
   
    Сначала вам будет предложено выполнить вход в свою подписку Azure. Затем вам будет предложено ввести сведения HTTPS/имя и пароль учетной записи администратора для кластера HDInsight.

2. После завершения задания должна быть возвращена информация следующего вида:
   
        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a name="a-idtroubleshootingatroubleshooting"></a><a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **pigjob.ps1** , сохраните его, а затем запустите снова.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Будет возвращена информация, которая записывается в STDERR на сервере при запуске задания и может помочь определить причину сбоя задания.

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Сводка
Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания Pig в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Дальнейшие действия
Общая информация о Pig в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Jan17_HO3-->


