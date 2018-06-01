---
title: Рекомендации по настройке производительности Hive в Azure Data Lake Store | Документация Майкрософт
description: Рекомендации по настройке производительности Hive в Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: c46eb1b2da62d70337e60066ed0706c3a4fdedcf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198975"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Рекомендации по настройке производительности Hive в HDInsight и Azure Data Lake Store

По умолчанию устанавливаются параметры, которые должны обеспечить оптимальную производительность для самых разных сценариев использования.  Но для запросов, предполагающих интенсивное выполнение операций ввода-вывода, производительность Hive в Azure Data Lake Store можно повысить, изменив некоторые настройки.  

## <a name="prerequisites"></a>предварительным требованиям

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранения озера данных Azure**. Инструкции по созданию учетной записи см. в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Store. См. статью [Создание кластера HDInsight с Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Убедитесь, что вы включили удаленный рабочий стол для кластера.
* **Запустите Hive в HDInsight**.  Дополнительные сведения о выполнении заданий Hive в HDInsight см. в разделе [Использование Hive в HDInsight]https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Рекомендации по настройке производительности в Azure Data Lake Store**.  См. [рекомендации по настройке производительности для Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).

## <a name="parameters"></a>Параметры

Ниже приведены наиболее важные параметры, которые помогут повысить производительность Azure Data Lake Store:

* **hive.tez.container.size** — объем памяти, используемой для каждой задачи;

* **tez.grouping.min-size** — минимальный размер каждого модуля сопоставления;

* **tez.grouping.max-size** — максимальный размер каждого модуля сопоставления;

* **hive.exec.reducer.bytes.per.reducer** — размер каждого модуля уменьшения.

**hive.tez.container.size** — размер контейнера, который определяет объем доступной памяти для каждого задания.  Это главный параметр для управления параллелизмом в Hive.  

**tez.grouping.min-size** — этот параметр позволяет задать минимальный размер каждого модуля сопоставления.  Если число модулей сопоставления, которые выбирает Tez, окажется меньше, чем значение этого параметра, Tez будет использовать заданное здесь значение.

**tez.grouping.max-size** — этот параметр позволяет задать максимальный размер каждого модуля сопоставления.  Если число модулей сопоставления, которые выбирает Tez, окажется больше, чем значение этого параметра, Tez будет использовать заданное здесь значение.

**hive.exec.reducer.bytes.per.reducer** — этот параметр задает размер каждого модуля уменьшения.  По умолчанию используется размер 256 МБ.  

## <a name="guidance"></a>Руководство

**Задайте hive.exec.reducer.bytes.per.reducer** — значение по умолчанию хорошо работает для несжатых данных.  Если используются сжатые данные, попробуйте сократить размер модуля уменьшения редукции.  

**Задайте hive.tez.container.size** — объем памяти для каждого узла, который определяется параметром yarn.nodemanager.resource.memory-mb (должен быть правильно определен для кластера HDI по умолчанию).  Дополнительные сведения о настройке памяти в YARN см. в этой [записи блога](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Для интенсивных нагрузок ввода-вывода будет полезным увеличить параллелизм, снижая размер контейнера Tez. Это предоставит пользователю дополнительные контейнеры, то есть увеличит параллелизм.  Но некоторые запросы Hive требуют значительного объема памяти (например, MapJoin).  Если задача не получит достаточного объема памяти, во время выполнения возникнет соответствующее исключение.  Если вы заметите такие исключения, увеличьте объем памяти.   

Число одновременно выполняемых задач для параллелизма ограничивается общим объемом памяти YARN.  Число контейнеров YARN определяет, сколько параллельных задач можно запустить.  Чтобы узнать, сколько памяти YARN доступно для каждого узла, зайдите в Ambari.  Перейдите к YARN и откройте вкладку конфигураций.  Объем памяти YARN отобразится в этом окне.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
При использовании Azure Data Lake Store повышение производительности достигается за счет максимально возможного уровня параллелизма.  Tez автоматически вычисляет число задач, которые нужно создать, поэтому нет необходимости настраивать этот параметр.   

## <a name="example-calculation"></a>Пример вычисления

Предположим, что у вас есть кластер D14 с 8 узлами.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Ограничения

**Регулирование Azure Data Lake Store** 

Если вы достигнете пределов пропускной способности, предоставляемой Azure Data Lake Store, начнут происходить сбои задач. Это можно заметить, отслеживая ошибки регулирования в журналах задач.  Можно уменьшить параллелизм, увеличив размер контейнера Tez.  Если для обработки задания требуется больший параллелизм, свяжитесь с нами.

Чтобы проверить, применяется ли для вас регулирование, включите ведение журнала отладки на стороне клиента. Вот как это сделать.

1. Поместите следующее свойство в окно свойств log4j в файле конфигурации Hive. Это можно сделать из представления Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG. Перезагрузите все узлы или службу, чтобы изменения конфигурации вступили в силу.

2. Если регулирование выполняется, вы увидите код ошибки HTTP 429 в файле журнала Hive. Файл журнала находится в каталоге /tmp/&lt;имя_пользователя&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Дополнительные сведения о настройке Hive

Ниже приведены некоторые блоги, которые помогут в настройке производительности запросов Hive.
* [Оптимизация запросов Hive для Hadoop в HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Troubleshooting Hive query performance](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) (Устранение неполадок с производительностью запросов Hive)
* [Примите участие в дискуссии об оптимизации Hive на HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
