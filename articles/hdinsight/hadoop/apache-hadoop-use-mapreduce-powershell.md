---
title: Использование MapReduce и PowerShell с Hadoop в Azure HDInsight | Документы Майкрософт
description: Информация об использовании PowerShell для удаленного выполнения заданий MapReduce с помощью Hadoop в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.openlocfilehash: 7416d064f89515feb04523ca6d4ea73f37c14e38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33938544"
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Выполнение заданий MapReduce с помощью PowerShell с использованием Hadoop в HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

В этом документе приведен пример использования Azure PowerShell для выполнения задания MapReduce в Hadoop на кластере HDInsight.

## <a id="prereq"></a>Предварительные требования

* **Кластер Azure HDInsight (Hadoop в HDInsight)**.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Рабочая станция с Azure PowerShell**.

## <a id="powershell"></a>Выполнение задания MapReduce

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать задания MapReduce в HDInsight. На внутреннем уровне PowerShell выполняет вызовы REST к [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (прежнее название — Templeton) в кластере HDInsight.

При выполнении заданий MapReduce в удаленном кластере HDInsight используются следующие командлеты:

* **Connect-AzureRmAccount** — выполняет аутентификацию Azure PowerShell для подписки Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition** — создает *определение задания*, используя заданную информацию MapReduce.

* **Start-AzureRmHDInsightJob** — отправляет определение задания в HDInsight и запускает задание. Будет возвращен объект *задания*.

* **Wait-AzureRmHDInsightJob**— использует объект-задание для проверки состояния задания. Он ожидает завершения задания или превышения времени ожидания.

* **Get-AzureRmHDInsightJobOutput** — используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Откройте новую командную строку **Azure PowerShell** . Перейдите к расположению файла **mapreducejob.ps1** , а затем используйте следующую команду для запуска сценария:

        .\mapreducejob.ps1

    При выполнении скрипта вам будет предложено ввести имя кластера HDInsight и имя для входа в кластер. Вам также может быть предложено аутентифицироваться в подписке Azure.

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

Чтобы просмотреть слова и статистику, полученные при выполнении задания, откройте файл **output.txt** в текстовом редакторе.

> [!NOTE]
> Выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

## <a id="troubleshooting"></a>Устранение неполадок

Если при завершении задания данные не возвращаются, просмотрите ошибки задания. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **mapreducejob.ps1** , сохраните его, а затем запустите снова.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Этот командлет возвращает информацию, которая записывалась в STDERR при выполнении задания.

## <a id="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания MapReduce в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
