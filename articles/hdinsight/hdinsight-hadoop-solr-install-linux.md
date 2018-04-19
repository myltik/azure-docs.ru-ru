---
title: Использование действия скрипта для установки Solr в кластере HDInsight на основе Linux — Azure | Документы Майкрософт
description: Узнайте, как устанавливать Solr в кластерах HDInsight Hadoop на основе Linux с помощью действий сценария.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: f642a1f8060f566ec95b23995d0f82191b0c5315
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Установка и использование Solr на кластерах HDInsight Hadoop

Узнайте, как установить Solr в Azure HDInsight с помощью действия сценария. Solr представляет собой многофункциональную платформу поиска и предоставляет возможности поиска корпоративного уровня на основе данных, управляемых Hadoop.

> [!IMPORTANT]
    > Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> Пример скрипта, используемый в этом документе, устанавливает кластер Solr 4.9 с определенной конфигурацией. Если вы хотите настроить кластер Solr для использования других коллекций, сегментов, схем, реплик и т. п., необходимо соответствующим образом изменить сценарий и двоичные файлы Solr.

## <a name="whatis"></a>Что такое Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) — это корпоративная платформа поиска, предоставляющая многофункциональные инструменты полнотекстового поиска данных. Если Hadoop обеспечивает хранение огромных объемов данных и управление ими, то Apache Solr предоставляет возможности поиска для быстрого извлечения этих данных.

> [!WARNING]
> Компоненты, поставляемые с кластером HDInsight, полностью поддерживаются корпорацией Майкрософт.
>
> Настраиваемые компоненты, такие как Solr, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. Служба поддержки Майкрософт не всегда имеет возможность устранить проблемы с пользовательскими компонентами. Может потребоваться обратиться за помощью к сообществу разработчиков открытого кода. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты, например [Hadoop](http://hadoop.apache.org/) на сайте [http://apache.org](http://apache.org).

## <a name="what-the-script-does"></a>Что делает сценарий

Этот сценарий вносит следующие изменения в кластер HDInsight:

* Устанавливает Solr 4.9 в `/usr/hdp/current/solr`.
* Создает пользователя **solrusr**, используемого для запуска службы Solr.
* Делает пользователя **solrusr** владельцем `/usr/hdp/current/solr`.
* Добавляет конфигурацию [Upstart](http://upstart.ubuntu.com/), которая автоматически запускает Solr.

## <a name="install"></a>Установка Solr с помощью действий сценария

Пример сценария для установки Solr в кластер HDInsight доступен по следующему адресу.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Чтобы создать кластер с установленной платформой Solr, следуйте указаниям в документе [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Во время создания выполните приведенные ниже действия для установки Solr.

1. В разделе __Сводка кластера__ выберите __Дополнительные параметры__, а затем — __Действия скрипта__. Используйте следующие сведения, чтобы заполнить форму.

   * **ИМЯ**: введите понятное имя для действия сценария.
   * **URI СКРИПТА**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **ГОЛОВНОЙ**: установите флажок.
   * **Рабочая роль**: установите флажок.
   * **ZooKeeper**: установите этот флажок для установки на узле Zookeeper.
   * **ПАРАМЕТРЫ**: оставьте это поле пустым.

2. В нижней части раздела **Действия скрипта** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Далее**, чтобы вернуться в колонку __Сводка кластера__.

3. На странице __Сводка кластера__ щелкните __Создать__, чтобы создать кластер.

## <a name="usesolr"></a>Как использовать Solr в HDInsight

> [!IMPORTANT]
> Действия, описанные в этом разделе, демонстрируют базовые функциональные возможности Solr. Дополнительные сведения об использовании Solr см. на [сайте Apache Solr](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Данные индекса

Выполните следующие действия, чтобы добавить демонстрационные данные в Solr и создать запрос к ним.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > В действиях, описанных ниже, для подключения к пользовательскому веб-интерфейсу Solr используется туннель SSL. Чтобы выполнить эти действия, необходимо установить туннель SSL и настроить браузер для его использования.
     >
     > Дополнительные сведения см. в документе [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

2. Используйте следующие команды, чтобы получить образец данных индекса Solr:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    В консоли отобразится результат следующего вида.

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Служебная программа `post.jar` добавляет документы **solr.xml** и **monitor.xml** в индекс.
  
3. Для отправки запроса к REST API Solr используйте следующую команду.

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Данная команда выполняет в **collection1** поиск документов, соответствующих **\*:\*** (кодируется как \*%3A\* в строке запроса). Ниже приведен документ JSON, представляющий собой пример возвращенного ответа.

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Использование панели мониторинга Solr

Панель мониторинга Solr — это веб-интерфейс для работы с Solr через веб-браузер. Прямой доступ к панели мониторинга Solr через Интернет из кластера HDInsight невозможен. Для этого следует использовать туннель SSH. Дополнительные сведения об использовании туннеля SSH см. в документе [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

Установив туннель SSH, выполните следующие действия, чтобы начать использовать панель мониторинга Solr.

1. Определите имя основного головного узла:

   1. Используйте протокол SSH, чтобы подключиться к головному узлу кластера. Например, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Дополнительные сведения об использовании протокола SSH см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Чтобы получить полное имя узла, используйте следующую команду:

        ```bash
        hostname -f
        ```

        Эта команда возвращает значение имени узла следующего вида.

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Сохраните полученное значение, так как оно понадобится позже.

2. В окне браузера введите адрес **http://HOSTNAME:8983/solr/#/**, где **HOSTNAME** — это имя, определенное на предыдущих этапах.

    Запрос направляется через туннель SSH к пользовательскому веб-интерфейсу Solr в кластере. Отображается страница, как показано на рисунке ниже.

    ![Изображение панели мониторинга Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. В левой части окна в раскрывающемся списке **Core Selector** (Базовый селектор) выберите **collection1**. В разделе **collection1** будет отображено несколько записей.

4. В разделе **collection1** выберите элемент **Запрос**. На странице поиска введите следующие значения:

   * В текстовом поле **q** введите **\*:**\*. Этот запрос возвращает все документы, индексированные в Solr. Если требуется найти в документах конкретную строку, ее также можно ввести в этом поле.
   * В текстовом поле **wt** выберите формат выходных данных. По умолчанию используется **JSON**.

     Наконец, нажмите кнопку **Execute Query** (Выполнить запрос) в нижней части панели поиска.

     ![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     В выходных данных возвращается два документа, которые вы ранее добавили в индекс. Выходные данные аналогичны приведенному ниже документу JSON.

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Запуск и остановка Solr

Чтобы вручную остановить или запустить Solr, используйте следующие команды.

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Резервное копирование индексированных данных

Чтобы выполнить архивацию данных Solr в хранилище по умолчанию для кластера, выполните следующее.

1. Подключитесь к кластеру с помощью SSH, а затем используйте следующую команду, чтобы получить имя головного узла:

    ```bash
    hostname -f
    ```

2. Чтобы создать моментальный снимок индексированных данных, используйте следующую команду. Замените **HOSTNAME** именем, полученным от предыдущей команды:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    В результате вы получите код XML следующего вида.

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Перейдите в каталог `/usr/hdp/current/solr/example/solr`. В нем находятся подкаталоги для каждой коллекции. Каждый каталог коллекции содержит каталог `data`, в котором хранится моментальный снимок этой коллекции.

4. Создайте сжатый архив папки с моментальным снимком, используя следующую команду.

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Замените значения `snapshot.20150806185338855` именем моментального снимка коллекции.

    Эта команда создаст архив **snapshot.20150806185338855.tgz**, в который будет помещено содержимое каталога **snapshot.20150806185338855**.

5. Впоследствии вы сможете сохранить архив в основном хранилище кластера, используя следующую команду:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Дополнительные сведения о резервном копировании и восстановлении Solr см. по ссылке [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Дополнительная информация

* [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.

* [Установка Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Установить Hue в кластерах HDInsight Hadoop можно при помощи настройки кластера. Hue — это набор веб-приложений, используемых для взаимодействия с кластером Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
