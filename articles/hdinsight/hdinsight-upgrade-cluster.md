---
title: Обновление кластера HDInsight до более новой версии в Azure | Документация Майкрософт
description: Узнайте, как установить новую версию кластера HDInsight.
services: hdinsight
documentationcenter: ''
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: bhanupr
ms.openlocfilehash: d6cd0dd8b326f5340e5014bba4babc6dd945744d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406836"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Обновление кластера HDInsight до более новой версии
Чтобы воспользоваться преимуществами новых возможностей HDInsight, мы рекомендуем обновить кластеры HDInsight до последней версии. Выполните следующие инструкции, чтобы обновить версию кластера HDInsight.

> [!NOTE]
> Поддержка кластеров HDInsight версий 3.2 и 3.3 скоро завершится. См. дополнительные сведения о [версиях компонентов HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Задачи обновления
Рабочий процесс для обновления кластера HDInsight выглядит так.

![Схема рабочего процесса](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Ознакомьтесь со всеми разделами этого документа. Там описаны изменения, которые могут потребоваться при обновлении кластера HDInsight.
2. Создайте кластер как среду тестирования и контроля качества. См. дополнительные сведения о [создании кластеров под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Скопируйте в новую среду существующие задания, источники данных и приемники. См. дополнительные сведения о [копировании данных в среду тестирования](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment).
4. Выполните проверочное тестирование, чтобы убедиться, что задания должным образом работают новом кластере.


Убедившись, что все работает правильно, запланируйте время простоя для миграции. Во время этого простоя выполните следующие действия.

1.  Создайте резервную копию всех временных данных, хранящихся локально на узлах кластера. Например, к ним могут относиться данные, которые хранятся непосредственно на головном узле.
2.  Удалите существующий кластер.
3.  Создайте кластер в той же подсети виртуальной сети, в которой находится последняя (или поддерживаемая) версия HDI, используя то же хранилище данных по умолчанию, что и для предыдущего кластера. Это позволит новому кластеру продолжить работу с существующими рабочими данными.
4.  Импортируйте все временные данные из резервной копии.
5.  Запустите задания и продолжите обработку с помощью нового кластера.

## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте, как создавать кластеры HDInsight под управлением Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Подключитесь к HDInsight с помощью протокола SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
* [Выполняйте управление кластером под управлением Linux с помощью Ambari](hdinsight-hadoop-manage-ambari.md)

