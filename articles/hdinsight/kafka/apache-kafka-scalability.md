---
title: Повышение масштабируемости Apache Kafka в Azure HDInsight | Документация Майкрософт
description: Узнайте, как настроить управляемые диски для кластера Apache Kafka в Azure HDInsight, чтобы повысить масштабируемость.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/30/2018
ms.author: larryfr
ms.openlocfilehash: 1a104f4b4dee340f43c1dba01b83cb80e138a9ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626730"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Настройка объема хранилища и уровня масштабируемости для Apache Kafka в HDInsight

Узнайте, как настроить количество управляемых дисков, используемых Apache Kafka в HDInsight.

Kafka в HDInsight использует локальный диск для виртуальных машин в кластере HDInsight. Так как Kafka обрабатывает большое количество операций ввода-вывода, используются [управляемые диски Azure](../../virtual-machines/windows/managed-disks-overview.md), чтобы обеспечить высокую пропускную способность и предоставить дополнительное хранилище для каждого узла. Если для Kafka использовать стандартные виртуальные жесткие диски (VHD), на каждом узле будет доступен 1 ТБ памяти. Но благодаря управляемым дискам можно использовать несколько дисков и увеличить объем каждого узла кластера до 16 ТБ.

На схеме ниже сравниваются две версии Kafka в HDInsight: до использования управляемых дисков и с ними.

![Схема: Kafka в HDInsight с одним виртуальным жестким диском для каждой виртуальной машины и с несколькими управляемыми дисками для каждой виртуальной машины](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Настройка управляемых дисков на портале Azure

1. Следуйте указаниям в статье о [создании кластеров HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), чтобы ознакомиться с основными действиями для создания кластера с помощью портала. Не завершайте создание кластера на портале.

2. В разделе __Размер кластера__ укажите число дисков в поле __Число дисков на рабочий узел__.

    > [!NOTE]
    > Управляемый диск может быть двух типов: __Стандартный__ (HDD) или __Премиум__ (SSD). Диски категории "Премиум" используются с виртуальными машинами серий DS и GS. Для всех остальных виртуальных машин используются стандартные управляемые диски.

    ![Изображение раздела "Размер кластера" с выделенным полем "Число дисков на рабочий узел"](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Настройка управляемых дисков с использованием шаблона Resource Manager

Чтобы управлять количеством дисков, которое используется рабочими узлами в кластере Kafka, используйте следующий раздел шаблона:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Полный шаблон, в котором показано, как настроить управляемые диски, можно найти на странице [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о работе с Kafka HDInsight см. в следующих документах:

* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)

* [Блог HDInsight об управляемых дисках в Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)