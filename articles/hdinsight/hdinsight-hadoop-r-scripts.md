---
title: Использование R в HDInsight для настройки кластеров — Azure | Документы Майкрософт
description: Сведения об установке R с помощью действия сценария и использовании R в кластерах HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: c5fb38de8b1023a05709d07ba17b0c776c87957e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Установка и использование R на кластерах HDInsight Hadoop

Научитесь настраивать кластер HDInsight на основе Windows с R с помощью сценария действия и использовать R в кластерах HDInsight. В состав предложения [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) входит R Server. Это позволяет сценариям R использовать MapReduce и Spark для выполнения распределенных вычислений. Дополнительные сведения см. в статье [Приступая к работе с R Server в HDInsight](r-server/r-server-get-started.md). Сведения об использовании R с кластером под управлением Linux см. в статье [Install and use R on HDinsight Hadoop clusters (Linux)](hdinsight-hadoop-r-scripts-linux.md) (Установка и использование R в кластерах HDInsight Hadoop (Linux)).

R можно установить в кластере любого типа (Hadoop, Storm, HBase, Spark) в HDInsight в Azure, воспользовавшись *Действием сценария*. Пример сценария для установки R в кластере HDInsight доступен в большом двоичном объекте хранилища Azure только для чтения по адресу [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Связанные статьи**

* [Установка и использование R на кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-hadoop-provision-linux-clusters.md) — общие сведения о создании кластеров HDInsight.
* [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария.
* [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Что такое R
<a href="http://www.r-project.org/" target="_blank">Проект R для статистических вычислений</a> — это язык программирования с открытым исходным кодом и программная среда для статистических вычислений. R предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности. Большинство профессиональных статистиков и ученых, работающих в целом ряде областей, отдает предпочтение программной среде R.

R совместим с хранилищем больших двоичных объектов Azure (WASB). Это дает возможность обрабатывать находящиеся там данные, используя R в HDInsight.  

## <a name="install-r"></a>Установка R
[Пример сценария](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) для установки R в кластере HDInsight доступен в большом двоичном объекте только для чтения в службе хранилища Azure. Этот раздел содержит инструкции по использованию примера скрипта при создании кластера с помощью портала Azure.

> [!NOTE]
> Пример сценария был представлен в кластере HDInsight версии 3.1. Дополнительные сведения о версиях кластера HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)
>
>

1. При создании кластера HDInsight на портале щелкните **Необязательная настройка**, а затем — **Действия скрипта**.
2. На странице **Действия скрипта** введите следующие значения.

    ![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Использование действия сценария для настройки кластера")

    <table border='1'>
        <tr><th>Свойство</th><th>Значение</th></tr>
        <tr><td>ИМЯ</td>
            <td>Укажите имя для действия скрипта, например <b>Установить R</b>.</td></tr>
        <tr><td>URI-адрес сценария</td>
            <td>Укажите URI для сценария, который вызывается для настройки кластера, например <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i>.</td></tr>
        <tr><td>Тип узла</td>
            <td>Укажите узлы, на которых выполняется сценарий настройки. Вы можете выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Worker nodes only</b> (Только рабочие узлы).
        <tr><td>Параметры</td>
            <td>Укажите параметры, если они требуются для сценария. Но сценарий для установки R не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
    </table>

    Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните флажок, чтобы начать создание кластера.

Сценарий также позволяет установить R в HDInsight с помощью Azure PowerShell или пакета SDK для HDInsight .NET. Указания по выполнению этих процедур приведены ниже в этой статье.

## <a name="run-r-scripts"></a>Запуск сценариев R
В этом разделе содержится описание действий для выполнения скрипта R в кластере Hadoop на базе HDInsight.

1. **Установите подключение между удаленным рабочим столом и кластером.** На портале Azure включите удаленный рабочий стол для созданного кластера с установленным R, а затем подключитесь к кластеру. Указания см. в разделе [Подключение к кластерам HDInsight с помощью RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Откройте консоль R**— установочный файл R размещает на рабочем столе головного узла ссылку на консоль R. Щелкните ее, чтобы открыть консоль R.
3. **Запустите скрипт R.** Скрипт R можно запустить непосредственно из консоли R. Для этого необходимо вставить его в консоль, выбрать его и нажать клавишу ВВОД. Ниже приведен пример простого скрипта, который генерирует числа от 1 до 100 и умножает их на 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Первые две строки вызывают библиотеки RHadoop, установленные с R. Последняя строка выводит результаты на консоль. Выходные данные должны выглядеть так:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Установка R с помощью Azure PowerShell
См. статью [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  В этом примере показано, как установить Spark с помощью Azure PowerShell. Нужно настроить скрипт для использования [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Установка R с помощью пакета SDK для .NET
См. статью [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). В этом примере показано, как установить Spark с помощью пакета SDK для .NET. Нужно настроить скрипт для использования [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>См. также
* [Установка и использование R на кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-hadoop-provision-linux-clusters.md) — общие сведения о создании кластеров HDInsight.
* [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария.
* [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md)
* [Установка и использование Spark в кластерах HDInsight Hadoop с помощью действия сценария][hdinsight-install-spark]. Пример действия сценария для установки Spark.
* [Установка и использование Giraph в HDInsight](hdinsight-hadoop-giraph-install.md) — пример действия скрипта для установки Giraph.
* [Установка и использование Solr на кластерах HDInsight Hadoop](hdinsight-hadoop-solr-install-linux.md) — пример действия скрипта для установки Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]:spark/apache-spark-jupyter-spark-sql.md
