---
title: Как использовать MMLSpark в машинном обучении | Документация Майкрософт
description: Руководство по использованию библиотеки MMLSpark со службой "Машинное обучение Azure".
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 3c9bcce67bb802a8596416b55ef61a51ab2900bd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831536"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Как использовать библиотеку службы "Машинное обучение Microsoft" для Apache Spark

## <a name="introduction"></a>Введение

[Библиотека службы "Машинное обучение Microsoft" для Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) предоставляет средства, позволяющие легко создавать масштабируемые модели машинного обучения для больших наборов данных. Она включает интеграцию конвейеров SparkML с [Microsoft Cognitive Toolkit ](https://github.com/Microsoft/CNTK) и [OpenCV](http://www.opencv.org/). Благодаря этому вы получаете следующие возможности: 
 * Получение и предварительная обработка данных изображения.
 * Создание признаков изображений и текста с помощью предварительно обученных моделей глубокого обучения.
 * Обучение и оценка моделей классификации и регрессии с помощью неявного создания признаков.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем приступить к выполнению этого руководства, вам понадобится сделать следующее:
- [Установить Azure Machine Learning Workbench](../service/quickstart-installation.md).
- [Настроить кластер Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

## <a name="run-your-experiment-in-docker-container"></a>Запуск эксперимента в контейнере Docker

Служба Azure Machine Learning Workbench настроена для использования MMLSpark при запуске экспериментов в контейнере Docker на локальном компьютере или на удаленной виртуальной машине. Благодаря этому вы можете легко отлаживать модели PySpark и экспериментировать с ними, прежде чем масштабно запускать их в кластере. 

Чтобы начать работу с примером, создайте проект и выберите пример коллекции "MMLSpark в Adult Census". Выберите Docker в качестве контекста вычислений на панели мониторинга проекта и щелкните Run (Запустить). Azure Machine Learning Workbench создает контейнер Docker, чтобы запустить программу PySpark, а затем выполняет программу.

После выполнения результаты можно просмотреть в представлении журнала выполнения Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Установите MMLSpark в кластере Azure HDInsight Spark.

Чтобы завершить этот и следующий шаг, сначала необходимо [создать кластер Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

По умолчанию Azure Machine Learning Workbench устанавливает пакет MMLSpark в кластере при запуске эксперимента. Этот процесс можно контролировать и установить другие пакеты Spark, изменив файл _aml_config/spark_dependencies.yml_ в папке проекта.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

MMLSpark также можно установить непосредственно в кластере HDInsight Spark с помощью [действия скрипта](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Настройка кластера Azure HDInsight Spark в качестве целевого объекта вычислений

Откройте окно интерфейса командной строки в Azure Machine Learning Workbench. Для этого перейдите в меню "Файл" и щелкните "Открыть командную строку".

В окне интерфейса командной строки выполните следующие команды.

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Теперь кластер доступен как целевой объект вычислений для проекта.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Выполните эксперимент в кластере Azure HDInsight Spark.

Вернитесь на панель мониторинга проекта примера "MMLSpark в Adult Census". Выберите кластер в качестве целевого объекта вычислений и щелкните Run (Запустить).

Azure Machine Learning Workbench отправит задание Spark в кластер. Вы можете отслеживать ход выполнения и просмотреть результаты в представлении журнала выполнения.

## <a name="next-steps"></a>Дополнительная информация
Сведения о библиотеке MMLSpark и примерах см. в [репозитории MMLSpark на GitHub](https://github.com/Azure/mmlspark).

*Apache® Apache Spark и Spark® являются охраняемыми товарными знаками или товарными знаками Apache Software Foundation в США и/или других странах.*
