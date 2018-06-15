---
title: Использование Hive Hadoop с помощью PowerShell в HDInsight — Azure | Документы Майкрософт
description: Использование PowerShell для выполнения запросов Hive в Hadoop в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d72d5223373043648aeb3d783477781dbce35f6d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164580"
---
# <a name="run-hive-queries-using-powershell"></a>Выполнение запросов Hive с помощью PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

В этом документе приведен пример использования Azure PowerShell в режиме группы ресурсов Azure для выполнения запросов Hive в Hadoop в кластере HDInsight.

> [!NOTE]
> В этом документе не приводится подробное описание процессов, которые выполняют инструкции HiveQL, используемые в примерах. Информацию об операторах HiveQL, используемых в данном примере, см. в статье [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>предварительным требованиям

* Hadoop в кластере HDInsight версии 3.4 или выше на платформе Linux.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Клиент с Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать запросы Hive в HDInsight. Во внутренних командлет использует вызовы REST к [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) в кластере HDInsight.

При выполнении запросов Hive на удаленном кластере HDInsight используются следующие командлеты:

* `Connect-AzureRmAccount` — выполняет аутентификацию Azure PowerShell для подписки Azure.
* `New-AzureRmHDInsightHiveJobDefinition` — создает *определение задания* с использованием заданных операторов HiveQL.
* `Start-AzureRmHDInsightJob` — отправляет определение задания в HDInsight и запускает задание. Будет возвращен объект *задания*.
* `Wait-AzureRmHDInsightJob` — использует объект-задание для проверки состояния задания. Он ожидает завершения задания или превышения времени ожидания.
* `Get-AzureRmHDInsightJobOutput` — используется для получения выходных данных задания.
* `Invoke-AzureRmHDInsightHiveJob` — используется для выполнения операторов HiveQL. Этот командлет блокирует выполнение запроса, после чего возвращаются результаты.
* `Use-AzureRmHDInsightCluster` — указывает, что при выполнении команды `Invoke-AzureRmHDInsightHiveJob` должен использоваться текущий кластер.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Откройте новую командную строку **Azure PowerShell** . Перейдите к расположению файла `hivejob.ps1`, а затем используйте следующую команду для запуска скрипта:

        .\hivejob.ps1

    При запуске сценария будет предложено ввести имя кластера, данные HTTPS и учетной записи администратора для кластера. Вам также может быть предложено выполнить вход в свою подписку Azure.

3. Когда задание завершается, возвращается текст следующего содержания:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Как уже упоминалось, `Invoke-Hive` можно использовать для отправки запроса и ожидания ответа. Чтобы увидеть работу команды Invoke-Hive, используйте следующий сценарий:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Выходные данные выглядят так:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Для более длинных запросов HiveQL вы можете использовать командлет Azure PowerShell **Here-Strings** или файлы сценариев HiveQL. В приведенном ниже отрывке кода показано, как использовать командлет `Invoke-Hive` для запуска файла скрипта HiveQL. Файл скрипта HiveQL необходимо передать в wasb://.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Дополнительные сведения о командлете **Here-Strings** см. <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">здесь</a>.

## <a name="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются при завершении задания, просмотрите журналы ошибок. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующий текст в конце файла `hivejob.ps1`, сохраните его, а затем запустите снова.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Этот командлет возвращает информацию, которая записывается в STDERR на сервере при обработке задания.

## <a name="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a name="next-steps"></a>Дополнительная информация

Общая информация о Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
