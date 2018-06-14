---
title: Установка или обновление Mono в HDInsight — Azure | Документы Майкрософт
description: Сведения об использовании конкретной версии Mono с кластером HDInsight. Mono используется для запуска приложений .NET в кластерах HDInsight под управлением Linux.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: 165f1d8175c7c7b58a5eec02a208b81fe73cb5f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400441"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Установка или обновление Mono в HDInsight

Сведения об установке конкретной версии [Mono](https://www.mono-project.com) в HDInsight версии 3.4 или выше.

Mono устанавливается на HDInsight версии 3.4 и выше и используется для запуска приложений .NET. Сведения о версии Mono, которая входит в состав каждой версии HDInsight, см. в статье [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md). Чтобы установить разные версии в кластере, используйте действия скрипта, рассматриваемые в этом документе. 

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующий параметр.

* __Номер версии Mono.__ Версия Mono, которую необходимо установить. Эта версия должна быть доступна по адресу [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

С помощью скрипта можно установить следующие пакеты Mono:

* __mono-complete__;

* __ca-certificates-mono__.

## <a name="the-script"></a>Сценарий

__Расположение скрипта__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Требования__

* Скрипт должен применяться к __головным узлам__ и __рабочим узлам__.

## <a name="to-use-the-script"></a>Использование скрипта

Сведения об использовании этого скрипта с HDInsight см. в документе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Вы можете использовать скрипт с помощью портала Azure, Azure PowerShell или интерфейса командной строки Azure.

При выполнении документа со сведениями о действиях скрипта используйте следующий универсальный код ресурса (URI):

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Чтобы указать версию Mono для установки, введите номер версии в поле __Параметры__. Например, введите `5.4`, чтобы установить Mono 5.4.

> [!NOTE]
> Чтобы настроить HDInsight с помощью этого скрипта, пометьте скрипт как __сохраненный__. Этот параметр позволяет HDInsight применять скрипт к рабочим узлам, добавленным с помощью операций масштабирования.

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как обновить или установить конкретную версию Mono в HDInsight. Дополнительные сведения об использовании приложений .NET в HDInsight см. в следующих статьях:

* [Использование языка C# для потоковой передачи MapReduce в Hadoop в HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md);
* [Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md);
* [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md);
* [Перенос решений .NET из HDInsight под управлением Windows в HDInsight под управлением Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

Дополнительные сведения об использовании действий скрипта см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).