---
title: Установка Presto в кластерах Azure HDInsight на платформе Linux | Документация Майкрософт
description: Узнайте, как устанавливать Presto и Airpal в кластерах HDInsight Hadoop на основе Linux с помощью действий сценария.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 32b7925b7414f00dfdd7d5c8a45b3601bf58942e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401820"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Установка и использование Presto в кластерах HDInsight Hadoop

Данный документ описывает процесс установки Presto на кластеры HDInsight Hadoop с помощью действия скрипта. Вы также узнаете, как устанавливать Airpal в имеющемся кластере Presto HDInsight.

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим **кластер Hadoop для HDInsight 3.5** под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в статье [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)

## <a name="what-is-presto"></a>Что такое Presto?
[Presto](https://prestodb.io/overview.html) представляет собой быстрый распределенный модуль SQL-запросов для больших данных. Presto подходит для интерактивных запросов петабайт данных. Дополнительные сведения о компонентах Presto и их совместной работе см. в статье [Presto Concepts](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst) (Концепции Presto).

> [!WARNING]
> Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба поддержки Майкрософт поможет вам выявить и устранить проблемы, связанные с этими компонентами.
> 
> Настраиваемые компоненты, такие как Presto, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты, например [Hadoop](http://hadoop.apache.org/) на сайте [http://apache.org](http://apache.org).
> 
> 


## <a name="install-presto-using-script-action"></a>Установка Presto с помощью действия скрипта

Этот раздел содержит инструкции по использованию примера сценария при создании нового кластера с помощью портала Azure. 

1. Начните подготовку кластера с помощью действий, описанных в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Убедитесь, что создаете кластер с помощью процесса создания **пользовательского** кластера. Кластер должен соответствовать следующим требованиям.

    * Это должен быть кластер Hadoop в HDInsight версии 3.5.

    * Он должен использовать хранилище Azure в качестве хранилища данных. Использование Presto в кластере с Azure Data Lake Store в качестве хранилища пока не поддерживается. 

    ![Создание кластера HDInsight с использованием настраиваемых параметров](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. В области **Дополнительные настройки** выберите **Действия скрипта** и введите следующие сведения.
   
   * **ИМЯ**: введите понятное имя для действия сценария.
   * **URI bash-скрипта**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **ГОЛОВНОЙ**: установите флажок.
   * **Рабочая роль**: установите флажок.
   * **ZOOKEEPER**: снимите этот флажок.
   * **ПАРАМЕТРЫ**: оставьте это поле пустым.


3. Внизу области **Действия скрипта** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Выбрать** внизу области **Дополнительные настройки**, чтобы сохранить конфигурацию.

4. Продолжите подготовку кластера к работе, как описано в статье [Подготовка кластеров HDInsight на основе Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > Для выполнения действий этого сценария также можно использовать Azure PowerShell, Azure CLI, пакет SDK .NET для HDInsight или шаблоны Azure Resource Manager. Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Использование Presto с HDInsight

Для работы с Presto в кластере HDInsight выполните следующие действия.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Запустите оболочку Presto, используя следующую команду.
   
        presto --schema default

3. Выполните запрос в примере таблицы **hivesampletable**, которая доступна во всех кластерах HDInsight по умолчанию.
   
        select count (*) from hivesampletable;
   
    По умолчанию соединители [Hive](https://prestodb.io/docs/current/connector/hive.html) и [TPCH](https://prestodb.io/docs/current/connector/tpch.html) для Presto уже настроены. Соединитель Hive настроен на использование установки Hive по умолчанию, поэтому все таблицы из Hive будут автоматически отображаться в Presto.

    Дополнительные сведения см. в [документации по Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Использование Airpal с Presto

[Airpal](https://github.com/airbnb/airpal#airpal) является веб-интерфейсом запросов с открытым кодом для Presto. Дополнительные сведения об Airpal см. в [документации по Airpal](https://github.com/airbnb/airpal#airpal).

Чтобы установить Airpal на граничном узле, сделайте следующее:

1. Подключитесь по протоколу SSH к головному узлу кластера HDInsight, в котором уже установлен Presto.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. После подключения выполните следующую команду.

        sudo slider registry  --name presto1 --getexp presto 
   
    Должен появиться результат, аналогичный приведенному ниже коду JSON.

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. В выходных данных запомните значение свойства **value**. Это значение потребуется при установке Airpal на граничном узле кластера. В приведенном выше примере нужным значением будет **10.0.0.12:9090**.

4. Используйте **[этот](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** шаблон для создания граничного узла кластера HDInsight и введите значения, как показано на следующем снимке экрана.

    ![Установка HDInsight Airpal в кластер Presto](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Щелкните **Приобрести**.

6. После применения изменений к конфигурации кластера можно получить доступ к веб-интерфейсу Airpal, выполнив следующие действия.

    1. В диалоговом окне кластера выберите **Приложения**.

        ![Запуск HDInsight Airpal в кластере Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. В области **Установленные приложения** щелкните **Портал** напротив airpal.

        ![Запуск HDInsight Airpal в кластере Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. При появлении запроса введите учетные данные администратора, указанные при создании кластера HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Настройка установки Presto в кластере HDInsight

Для настройки процесса установки выполните следующие действия:

1. Подключитесь по протоколу SSH к головному узлу кластера HDInsight, в котором уже установлен Presto.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Внесите изменения конфигурации в файл `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Дополнительные сведения о конфигурации см. в статье [Presto configuration for YARN-based clusters](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html) (Конфигурация Presto для кластеров на базе YARN).

3. Остановите и отмените текущий выполняемый экземпляр Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Запустите новый экземпляр Presto с настройками.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Дождитесь готовности нового экземпляра и запишите адрес координатора Presto.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Создание тестовых данных для кластеров HDInsight под управлением Presto

TPC-DS — это отраслевой стандарт для измерения производительности многих систем поддержки принятия решений, включая системы больших данных. Presto можно использовать, чтобы генерировать данные и сравнивать их с собственными данными тестирования HDInsight. Дополнительные сведения см. [здесь](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>См. также
* [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Hue является веб-интерфейсом, позволяющим легко создавать, выполнять и сохранять задания Pig и Hive.

* [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.

* [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
