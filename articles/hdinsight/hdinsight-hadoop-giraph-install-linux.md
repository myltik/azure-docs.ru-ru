---
title: Установка и использование Giraph в кластерах HDInsight (Hadoop) — Azure | Документы Майкрософт
description: Узнайте, как устанавливать Giraph в кластерах HDInsight на основе Linux с помощью действий сценария. Действия сценария позволяют настроить кластер во время создания, изменяя его конфигурацию или устанавливая службы и служебные программы.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 03e72c29bedf6a3125a5ae0272e93cdf58632bc6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177039"
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Установка Giraph в кластерах HDInsight Hadoop и использование Giraph для обработки диаграмм больших объемов

Узнайте, как установить Apache Giraph в кластере HDInsight. Действие сценария в HDInsight позволяет настроить кластер с помощью сценария bash. Сценарии можно использовать для настройки кластеров во время и после создания кластера.

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whatis"></a>Что такое Giraph

[Apache Giraph](http://giraph.apache.org/) позволяет обрабатывать графы с помощью Hadoop и может использоваться с Azure HDInsight. Графы моделируют взаимоотношения между объектами. Например, подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях. Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

* определение потенциальных друзей на основе текущих взаимоотношений;

* определение кратчайшего маршрута между двумя компьютерами в сети;

* вычисление ранга страницы для веб-страниц.

> [!WARNING]
> Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба технической поддержки Майкрософт помогает выявить и решить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты, такие как Giraph, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Эту проблему может решить служба технической поддержки Майкрософт. В противном случае необходимо обратиться в сообщество разработчиков открытого кода, обладающих большим опытом в этой сфере. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты, например [Hadoop](http://hadoop.apache.org/) на сайте [http://apache.org](http://apache.org).


## <a name="what-the-script-does"></a>Что делает сценарий

Сценарий выполняет следующие действия:

* Устанавливает Giraph в `/usr/hdp/current/giraph`.

* Копирует файл `giraph-examples.jar` в хранилище по умолчанию (WASB) в кластере: `/example/jars/giraph-examples.jar`.

## <a name="install"></a>Установка Giraph с помощью действий сценария

Пример сценария для установки Giraph в кластер HDInsight доступен по следующему адресу:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Этот раздел содержит инструкции по использованию примера сценария при создании кластера с помощью портала Azure.

> [!NOTE]
> Действие сценария может применяться следующими способами:
> * Azure PowerShell
> * Интерфейс командной строки Azure
> * Пакет SDK для HDInsight .NET
> * Шаблоны Azure Resource Manager
> 
> Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Приступите к созданию кластера с помощью действий, описанных в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-hadoop-create-linux-clusters-portal.md), но не завершайте создание.

2. В разделе **Необязательная настройка** выберите **Действия скрипта** и укажите следующие сведения:

   * **ИМЯ**: введите понятное имя для действия сценария.

   * **URI СКРИПТА**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **Головной**: установите флажок.

   * **Рабочий**: не устанавливайте этот флажок.

   * **ZOOKEEPER**: не устанавливайте этот флажок.

   * **ПАРАМЕТРЫ**: оставьте это поле пустым.

3. В нижней части раздела **Действия скрипта** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, в нижней части раздела **Необязательная конфигурация** нажмите кнопку **Выбрать**, чтобы сохранить дополнительные настройки.

4. Продолжите создание кластера, как описано в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Как использовать Giraph в HDInsight

После создания кластера выполните следующие действия, чтобы запустить пример SimpleShortestPathsComputation (входит в Giraph). В примере использована простая реализация [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) для поиска кратчайшего пути между объектами в графе.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Создайте файл **tiny_graph.txt**, используя следующую команду.

    ```bash
    nano tiny_graph.txt
    ```

    В качестве содержимого файла добавьте следующий текст:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Эти данные описывают взаимоотношения между объектами в направленном графе с использованием формата `[source_id, source_value,[[dest_id], [edge_value],...]]`. Каждая строка представляет взаимоотношение между объектом `source_id` и одним или несколькими объектами `dest_id`. Значение `edge_value` можно представить себе как силу или расстояние подключения между `source_id` и `dest\_id`.

    В визуальной форме и с использованием значения (или веса) в качестве расстояния между объектами данные могут выглядеть, как показано на следующей схеме.

    ![tiny_graph.txt начерчен в виде кругов с линиями различной длины между](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Чтобы сохранить файл, нажмите **CTRL+X**, затем **Y** и **ВВОД**.

4. Чтобы сохранить данные в основном хранилище в кластере HDInsight, используйте следующую команду:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Запустите пример SimpleShortestPathsComputation, используя следующую команду:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    В следующей таблице описаны параметры, используемые в этой команде.

   | Параметр | Действие |
   | --- | --- |
   | `jar` |JAR-файл, содержащий примеры. |
   | `org.apache.giraph.GiraphRunner` |Класс, используемый для запуска примеров. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Используемый пример. В нем вычисляется кратчайший путь между ID 1 и всеми другими идентификаторами в графе. |
   | `-ca mapred.job.tracker` |Головной узел кластера. |
   | `-vif` |Формат входных данных. |
   | `-vip` |Файл входных данных. |
   | `-vof` |Формат выходных данных. В данном примере — ИД и значение в виде обычного текста. |
   | `-op` |Расположение выходных данных. |
   | `-w 2` |Количество используемых рабочих ролей. В этом примере — 2. |

    Дополнительные сведения об этих и других параметрах, которые используются в примерах Giraph, см. в [кратком руководстве по Giraph](http://giraph.apache.org/quick_start.html).

6. После завершения задания результаты сохраняются в каталоге **/example/out/shotestpaths**. Имена выходных файлов будут начинаться с **part-m-** и заканчиваться числом, указывающим номер файла (первый, второй и т. д.). Чтобы просмотреть выходные данные, используйте следующую команду:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Выходные данные выглядят следующим образом:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Выходные данные имеют следующий формат: `destination_id` и `distance`. `distance` представляет собой значение переходов на пути между объектом ID 1 и целевым объектом ID.

    При визуализации данных можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Кратчайший путь между ID 1 и ID 4 — это 5. Это значение представляет собой общее расстояние между объектами <span style="color:orange">ID 1 и ID 3</span> и между объектами <span style="color:red">ID 3 и ID 4</span>.

    ![Представление объектов в виде кругов с кратчайшими путями между ними](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Дополнительная информация

* [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md).

* [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md).
