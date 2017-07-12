---
title: "Высокий уровень доступности с Apache Kafka в Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как обеспечить высокий уровень доступности с помощью Apache Kafka в Azure HDInsight. Узнайте, как перераспределять реплики секций в Kafka, чтобы в случае сбоя они находились в разных доменах в регионе Azure, в котором размещена служба HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f8164d1c3483b28e5f2abcc8035da78880daec1e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017

---
<a id="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight" class="xliff"></a>

# Высокий уровень доступности данных с Apache Kafka (предварительная версия) в HDInsight

Узнайте, как настроить реплики секций для разделов Kafka, чтобы воспользоваться преимуществами конфигураций базовых аппаратных стоек. Эта конфигурация обеспечивает доступность данных, хранящихся в Apache Kafka в HDInsight.

<a id="fault-and-update-domains-with-kafka" class="xliff"></a>

## Домены сбоя и обновления с Kafka

Домен сбоя — это логическое объединение базового оборудования в центре обработки данных Azure. Все домены сбоя используют общий источник питания и сетевой коммутатор. Виртуальные машины и управляемые диски, на которых реализуются узлы в кластере HDInsight, распределяются по этим доменам сбоя. Такая архитектура ограничивает потенциальное влияние сбоев физического оборудования.

В каждом регионе Azure есть определенное количество доменов сбоя. Список доменов и количество доменов сбоя в них см. в документации [о группах доступности](../virtual-machines/linux/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> В Kafka нет сведений о доменах сбоя. При создании раздела в Kafka все реплики секций могут храниться в одном домене сбоя. Чтобы решить эту проблему, мы предоставляем [средство перераспределения секций Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

<a id="when-to-rebalance-partition-replicas" class="xliff"></a>

## Когда следует перераспределять реплики секций?

Чтобы обеспечить максимально высокий уровень доступности данных Kafka, следует перераспределять реплики секций для раздела в следующих случаях:

* при создании раздела или секции;

* при масштабировании кластера.

<a id="replication-factor" class="xliff"></a>

## Коэффициент репликации

> [!IMPORTANT]
> Мы рекомендуем использовать регион Azure с тремя доменами сбоя и коэффициент репликации 3.

Если необходимо указать регион с двумя доменами сбоя, используйте коэффициент репликации 4, чтобы равномерно распределить реплики на этих доменах.

Примеры создания разделов и настройки коэффициента репликации см. в статье [Приступая к работе с Apache Kafka (предварительная версия) в HDInsight](hdinsight-apache-kafka-get-started.md).

<a id="how-to-rebalance-partition-replicas" class="xliff"></a>

## Как перераспределять реплики секций?

Воспользуйтесь [средством перераспределения секций Kafka](https://github.com/hdinsight/hdinsight-kafka-tools), чтобы перераспределить выбранные разделы. Это средство следует запускать из сеанса SSH на главном узле кластера Kafka.

Дополнительные сведения о подключении к HDInsight с помощью SSH см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

* [Масштабируемость Kafka в HDInsight](hdinsight-apache-kafka-scalability.md)
* [Зеркальное отображение Kafka в HDInsight](hdinsight-apache-kafka-mirroring.md)
