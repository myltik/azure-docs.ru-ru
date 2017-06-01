---
title: "Использование MapReduce и PowerShell с Hadoop | Документация Майкрософт"
description: "Информация об использовании PowerShell для удаленного выполнения заданий MapReduce с помощью Hadoop в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: b24bfb08e692ec7ab6da9c60515d7cbeec9589b3
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Выполнение заданий MapReduce с помощью PowerShell с использованием Hadoop в HDInsight

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этом документе приведен пример использования Azure PowerShell для выполнения задания MapReduce в Hadoop на кластере HDInsight.

## <a id="prereq"></a>Предварительные требования

* **Кластер Azure HDInsight (Hadoop в HDInsight)**.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

* <seg>
  **Рабочая станция с Azure PowerShell**.</seg>

## <a id="powershell"></a>Выполнение задания MapReduce с помощью Azure PowerShell

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать задания MapReduce в HDInsight. Внутренне это достигается с помощью выполнения вызовов REST для [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (прежнее название — Templeton) на кластере HDInsight.

При выполнении заданий MapReduce в удаленном кластере HDInsight используются следующие командлеты:

* **Login-AzureRmAccount** — выполняет аутентификацию Azure PowerShell для подписки Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition** — создает *определение задания*, используя заданную информацию MapReduce.

* **Start-AzureRmHDInsightJob** — отправляет определение задания в HDInsight, запускает задание и возвращает объект *задание*, который можно использовать для проверки состояния задания.

* **Wait-AzureRmHDInsightJob**— использует объект-задание для проверки состояния задания. Он ожидает завершения задания или превышения времени ожидания.

* **Get-AzureRmHDInsightJobOutput** — используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как **mapreducejob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Откройте новую командную строку **Azure PowerShell** . Перейдите к расположению файла **mapreducejob.ps1** , а затем используйте следующую команду для запуска сценария:

        .\mapreducejob.ps1

    При выполнении сценария вам будет предложено ввести имя кластера HDInsight, а также сведения HTTPS/имя и пароль учетной записи администратора для кластера. Вам также может быть предложено аутентифицироваться в подписке Azure.

3. По завершении задания выходные данные будут иметь примерно следующий вид.

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Такие выходные данные означают, что задание завершено успешно.

    > [!NOTE]
    > Если значение **ExitCode** не равно 0, см. сведения в разделе [Устранение неполадок](#troubleshooting).

    В этом примере скачанные файлы сохраняются в файле **output.txt** в каталоге, из которого запускается сценарий.

### <a name="view-output"></a>Просмотр выходных данных

Откройте файл **output.txt** в текстовом редакторе, чтобы просмотреть слова и статистику, полученные при выполнении задания.

> [!NOTE]
> Выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

## <a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **mapreducejob.ps1** , сохраните его, а затем запустите снова.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Этот командлет возвращает информацию, которая записывается в STDERR на сервере при запуске задания и может помочь определить причину сбоя задания.

## <a id="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания MapReduce в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

