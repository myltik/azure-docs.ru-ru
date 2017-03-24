---
title: "Использование действия скрипта для установки Solr в кластере HDInsight на основе Linux | Документация Майкрософт"
description: "Узнайте, как устанавливать Solr в кластерах HDInsight Hadoop на основе Linux с помощью действий сценария."
services: hdinsight
documentationcenter: 
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
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: e270f3dfdaf248ed40d539fa1d2e69a3930d03c6
ms.lasthandoff: 01/18/2017


---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Установка и использование Solr на кластерах HDInsight Hadoop
В этой статье описано, как установить Solr в Azure HDInsight с помощью действия сценария. Solr представляет собой многофункциональную платформу поиска и предоставляет возможности поиска корпоративного уровня на основе данных, управляемых Hadoop. После установки Solr в кластере HDInsight вы также узнаете, как искать данные с помощью Solr.

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

Пример сценария, используемый в данном разделе, создает кластер Solr с определенной конфигурацией. Если вы хотите настроить кластер Solr на использование других коллекций, сегментов, схем, реплик и т. п., необходимо соответствующим образом изменить скрипт и двоичные файлы Solr.

## <a name="whatis"></a>Что такое Solr
[Apache Solr](http://lucene.apache.org/solr/features.html) — это корпоративная платформа поиска, предоставляющая эффективные инструменты полнотекстового поиска данных. Если Hadoop обеспечивает хранение огромных объемов данных и управление ими, то Apache Solr предоставляет возможности поиска для быстрого извлечения этих данных. Этот раздел содержит инструкции по настройке кластера HDInsight для установки Solr.

> [!WARNING]
> Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Техническая поддержка Майкрософт поможет вам выявить и решить проблемы, связанные с этими компонентами.
> 
> Настраиваемые компоненты, такие как Solr, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты, например [Hadoop](http://hadoop.apache.org/) на сайте [http://apache.org](http://apache.org).
> 
> 

## <a name="what-the-script-does"></a>Что делает сценарий
Этот сценарий вносит следующие изменения в кластер HDInsight:

* Устанавливает Solr в `/usr/hdp/current/solr`
* Создает пользователя **solrusr**, используемого для запуска службы Solr.
* Делает пользователя **solrusr** владельцем `/usr/hdp/current/solr`.
* Добавляет конфигурацию [Upstart](http://upstart.ubuntu.com/) , которая запускает Solr при перезапуске узла кластера. Solr также автоматически запускается на узлах кластера после установки.

## <a name="install"></a>Установка Solr с помощью действий сценария
Пример сценария для установки Solr в кластер HDInsight доступен по следующему адресу.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Этот раздел содержит инструкции по использованию примера сценария при создании нового кластера с помощью портала Azure. 

> [!NOTE]
> Для выполнения действий этого сценария также можно использовать Azure PowerShell, Azure CLI, пакет SDK .NET для HDInsight или шаблоны Azure Resource Manager. Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
> 
> 

1. Начните подготовку кластера с помощью действий, описанных в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md), но не завершайте ее.
2. В колонке **Дополнительная конфигурация** выберите **Действия скрипта** и введите следующие сведения:
   
   * **ИМЯ**: введите понятное имя для действия сценария.
   * **Универсальный код ресурса скрипта**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/r-installer-v01.sh.
   * **ГОЛОВНОЙ**: установите флажок.
   * **Рабочая роль**: установите флажок.
   * **ZooKeeper**: установите этот флажок для установки на узле Zookeeper.
   * **ПАРАМЕТРЫ**: оставьте это поле пустым.
3. В нижней части раздела **Действия скрипта** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, в нижней части колонки **Дополнительная конфигурация** нажмите кнопку **Выбрать**, чтобы сохранить дополнительные настройки.
4. Продолжите подготовку кластера к работе, как описано в статье [Подготовка кластеров HDInsight на основе Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Как использовать Solr в HDInsight
### <a name="indexing-data"></a>Индексирование данных
Необходимо начать с индексирования системой Solr нескольких файлов данных. Затем можно использовать Solr для выполнения запросов поиска в индексированных данных. Выполните следующие действия, чтобы добавить пример данных в Solr и создать запрос к ним.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:
   
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
     
     > [!IMPORTANT]
     > В действиях, описанных ниже, для подключения к веб-интерфейсу Solr используется туннель SSL. Чтобы выполнить эти действия, установите туннель SSL и настройте свой браузер для его использования.
     > 
     > Дополнительные сведения см. в статье [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).
     > 
     > 
2. Используйте следующие команды, чтобы получить образец данных индекса Solr:
   
        cd /usr/hdp/current/solr/example/exampledocs
        java -jar post.jar solr.xml monitor.xml
   
    В консоли будут выведены такие выходные данные:
   
        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624
   
    Служебная программа post.jar индексирует Solr с использованием двух примеров документов — **solr.xml** и **monitor.xml**. Они сохраняются в каталоге **collection1** в Solr.
3. Используйте следующую команду, чтобы создать запрос к интерфейсу REST API, который предоставляется Solr:
   
        curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
   
    В результате создается запрос к **collection1** на получение всех документов, соответствующих условию **\*:\*** (в строке запроса принимает вид \*%3A\*), с возвратом ответа в формате JSON. Ответ должен выглядеть примерно так:
   
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
Панель мониторинга Solr — это веб-интерфейс для работы с Solr через веб-браузер. Прямой доступ к панели мониторинга Solr через Интернет из кластера HDInsight невозможен. Для этого следует использовать туннель SSH. Дополнительные сведения об использовании туннеля SSH см. в статье [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

Установив туннель SSH, выполните следующие действия, чтобы начать использовать панель мониторинга Solr.

1. Определите имя основного головного узла:
   
   1. Используйте SSH, чтобы подключиться к кластеру через порт 22. Например, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`, где **USERNAME** — это имя пользователя SSH, а **CLUSTERNAME** — имя кластера.
      
       Дополнительные сведения об использовании SSH см. в следующих документах:
      
      * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
      * [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
   2. Чтобы получить полное имя узла, используйте следующую команду:
      
           hostname -f
      
       Вы получите приблизительно такой результат:
      
           hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
      
       Это имя узла, которое будет использоваться дальше.
2. В окне браузера введите адрес **http://HOSTNAME:8983/solr/#/**, где **HOSTNAME** — это имя, определенное на предыдущих этапах. 
   
    Этот запрос должен перенаправляться через SSH-туннель на головной узел вашего кластера HDInsight. Вы должны увидеть страницу, аналогичную показанной ниже:
   
    ![Изображение панели мониторинга Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)
3. В левой части окна в раскрывающемся списке **Core Selector** (Базовый селектор) выберите **collection1**. В разделе **collection1** будет отображено несколько записей.
4. В разделе **collection1** выберите элемент **Запрос**. На странице поиска введите следующие значения:
   
   * В текстовом поле **q** введите **\*:**\*. Это позволяет возвратить все документы, индексированные в Solr. Если требуется найти в документах конкретную строку, ее также можно ввести в этом поле.
   * В текстовом поле **wt** выберите формат выходных данных. По умолчанию используется **JSON**.
     
     Наконец, нажмите кнопку **Execute Query** (Выполнить запрос) в нижней части панели поиска.
     
     ![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)
     
     В выходных данных возвращаются два документа, которые использовались при индексировании Solr. Результат выглядит следующим образом:
     
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
Если вы хотите вручную остановить или запустить Solr, используйте следующие команды:

    sudo stop solr

    sudo start solr

## <a name="backup-indexed-data"></a>Резервное копирование индексированных данных
Опыт показывает, что стоит выполнять резервное копирование индексированных данных с узлов кластера Solr в хранилище больших двоичных объектов Azure. Для этого выполните следующие действия:

1. Подключитесь к кластеру с помощью SSH, а затем используйте следующую команду, чтобы получить имя головного узла:
   
        hostname -f
2. Чтобы создать моментальный снимок индексированных данных, используйте следующую команду. Замените **HOSTNAME** именем, полученным от предыдущей команды:
   
        curl http://HOSTNAME:8983/solr/replication?command=backup
   
    Вы должны получить примерно следующий ответ:
   
        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>
3. Затем перейдите в каталог **/usr/hdp/current/solr/example/solr**. Здесь будут размещаться подкаталоги для каждой коллекции. Каждый каталог коллекции содержит каталог **data**, в котором хранится моментальный снимок этой коллекции.
   
    Например, если вы создали индекс для образцов документов с помощью описанных выше действий, в каталоге **/usr/hdp/current/solr/example/solr/collection1/data** появится каталог с именем **snapshot.###########**, где вместо символов # указываются дата и время создания моментального снимка.
4. Создайте сжатый архив папки с моментальным снимком, используя команду, подобную следующей:
   
        tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
   
    В результате будет создан новый архив с именем **snapshot.20150806185338855.tgz**, в который будет помещено содержимое каталога **snapshot.20150806185338855**.
5. Впоследствии вы сможете сохранить архив в основном хранилище кластера, используя следующую команду:
   
    hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data
   
   > [!NOTE]
   > Возможно, вам понадобится создать отдельный каталог для хранения моментальных снимков Solr. Например, `hadoop fs -mkdir /solrbackup`.
   > 
   > 

Дополнительные сведения о резервном копировании и восстановлении Solr см. в статье [Making and restoring backups of SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores) (Создание и восстановление резервных копий SolrCore).

## <a name="see-also"></a>Дополнительные материалы
* [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Hue — это веб-интерфейс, который позволяет легко создавать, запускать и сохранять задания Pig и Hive, а также просматривать содержимое хранилища по умолчанию для вашего кластера HDInsight.
* [Установка и использование R на кластерах HDInsight Hadoop][hdinsight-install-r]. Используйте настройки кластера для установки R в кластерах HDInsight Hadoop. R — это открытый язык программирования и свободная программная среда для статистических вычислений. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.
* [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.
* [Установка Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Установить Hue в кластерах HDInsight Hadoop можно при помощи настройки кластера. Hue — это набор веб-приложений, используемых для взаимодействия с кластером Hadoop.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

