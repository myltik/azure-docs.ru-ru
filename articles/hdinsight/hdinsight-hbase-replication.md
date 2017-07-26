---
title: "Настройка репликации кластера HBase в виртуальных сетях — Azure | Документы Майкрософт"
description: "Сведения о том, как настроить репликацию HBase для балансировки нагрузки, обеспечения высокого уровня доступности, переноса или обновления с одной версии HDInsight на другую без простоя и аварийного восстановления."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 7a6a473b6db745563b3667da1013a8e78db8593c
ms.contentlocale: ru-ru
ms.lasthandoff: 06/10/2017


---
# <a name="configure-hbase-cluster-replication-within-virtual-networks"></a>Настройка репликации кластера HBase в виртуальных сетях

В этой статье вы узнаете, как настроить репликацию HBase в одной или между двумя виртуальными сетями.

Репликация кластера использует методологию source-push. Кластер HBase может быть исходным, кластером назначения или выполнять обе роли одновременно. Репликация выполняется асинхронно, а целью репликации в конечном итоге является согласованность. При получении источником изменения в семействе столбцов с включенной репликацией такое изменение распространяется на все кластеры назначения. При репликации данных с одного кластера на другой исходный кластер и все кластеры, которые уже потребили данные, отслеживаются для предотвращения циклических репликаций.

В этом руководстве показано, как настроить репликацию "источник — назначение". Другие топологии кластеров см. в документе [Справочное руководство по Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Примеры использования репликации HBase для одной виртуальной сети:

* Балансировка нагрузки, например выполнения проверки или заданий MapReduce в целевом кластере и приема данных на исходном кластере.
* высокую доступность;
* Перенос данных из одного кластера HBase в другой.
* Обновление кластера Azure HDInsight до другой версии.

Примеры использования репликации HBase для двух виртуальных сетей:

* Аварийное восстановление
* Балансировка нагрузки и секционирование приложения.
* высокую доступность;

Репликация кластеров осуществляется с помощью скриптов [действий сценария](hdinsight-hadoop-customize-cluster-linux.md), которые можно найти на веб-сайте [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="configure-the-environments"></a>Настройка сред

Доступны три варианта конфигурации:

- два кластера HBase в одной виртуальной сети Azure;
- два кластера HBase в двух виртуальных сетях в одном регионе;
- два кластера HBase в двух виртуальных сетях в двух регионах (георепликация).

Чтобы упростить настройку сред, мы создали несколько [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Если вы предпочитаете настраивать среды с помощью других методов, см. следующие статьи:

- [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Создание кластеров HBase в виртуальной сети Azure](hdinsight-hbase-provision-vnet.md)

### <a name="configure-one-virtual-network"></a>Настройка одной виртуальной сети

Чтобы создать два кластера HBase в одной виртуальной сети, нажмите расположенную ниже кнопку. Шаблон хранится среди [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>Настройка двух виртуальных сетей в одном регионе

Чтобы создать две виртуальные сети с пиринговой связью и два кластера HBase в одном регионе, нажмите расположенную ниже кнопку. Шаблон хранится среди [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



В этом случае необходима [пиринговая связь между виртуальными сетями](../virtual-network/virtual-network-peering-overview.md). Этот шаблон включает пиринговую связь между виртуальными сетями.   

Репликация HBase использует IP-адреса виртуальных машин ZooKeeper. Необходимо настроить статические IP-адреса для узлов назначения ZooKeeper HBase.

**Настройка статических IP-адресов**

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева щелкните **Группы ресурсов**.
3. Выберите группу ресурсов, содержащую кластер назначения HBase. Это группа ресурсов, указанная при использовании шаблона Resource Manager для создания среды. Для сужения списка можно использовать фильтр. Вы увидите список ресурсов, содержащий две виртуальные сети.
4. Выберите виртуальную сеть, содержащую кластер назначения HBase. Например, щелкните **xxxx-vnet2**. Вы увидите три устройства с именами, которые начинаются с **nic-zookeepermode -**. Это три виртуальные машины ZooKeeper.
5. Щелкните одну из виртуальных машин ZooKeeper.
6. Щелкните **IP configurations** (Конфигурации IP).
7. В списке выберите **ipConfig1**.
8. Щелкните **Статический** и введите фактический IP-адрес. IP-адрес потребуется вам при выполнении действия сценария для включения репликации.

  ![репликация HDInsight HBase, статический IP-адрес узла ZooKeeper](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Повторите шаг 6, чтобы задать статический IP-адрес для двух остальных узлов ZooKeeper.

Для сценария между виртуальными сетями необходимо использовать параметр **-ip** при вызове действия сценария **hdi_enable_replication.sh**.

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>Настройка двух виртуальных сетей в двух регионах

Чтобы создать две виртуальные сети в двух регионах, нажмите расположенную ниже кнопку. Шаблон хранится в общедоступном контейнере больших двоичных объектов Azure.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Создайте VPN-шлюз между двумя виртуальными сетями. Инструкции см. в статье [Создание виртуальной сети с подключением типа "сеть — сеть" с помощью портала Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Репликация HBase использует IP-адреса виртуальных машин ZooKeeper. Необходимо настроить статические IP-адреса для узлов назначения ZooKeeper HBase. Сведения о настройке статического IP-адреса см. в разделе "Настройка двух виртуальных сетей в одном регионе" этой статьи.

Для сценария между виртуальными сетями необходимо использовать параметр **-ip** при вызове действия сценария **hdi_enable_replication.sh**.

## <a name="load-test-data"></a>Загрузка тестовых данных

При репликации кластера необходимо указать реплицируемые таблицы. В этом разделе вы загрузите данные в исходный кластер. В следующем разделе вы включите репликацию между двумя кластерами.

Чтобы создать таблицу **контактов** и вставить в нее данные, следуйте инструкциям, приведенным в статье [Руководство по HBase. Приступая к работе с Apache HBase на Hadoop под управлением Linux в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Включение репликации

Ниже показано, как вызвать скрипт действия сценария на портале Azure. Дополнительные сведения о том, как выполнить действие сценария с помощью Azure PowerShell и интерфейса командной строки Azure, см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

**Включение репликации HBase на портале Azure**

1. Войдите на [портал Azure](https://portal.azure.com).
2. Откройте исходный кластер HBase.
3. В меню кластера щелкните **Действия скрипта**.
4. В верхней части колонки щелкните **Отправить новое**.
5. Выберите или введите следующие сведения.

  - **Имя:** введите **Включение репликации**.
  - **URL-адрес bash-скрипта:** введите **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  - **Головной узел:** выбран. Отмените выбор других типов узлов.
  - **Параметры:** параметры в следующем примере позволяют включить репликацию для всех существующих таблиц и копировать все данные из исходного кластера в целевой.

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. Щелкните **Создать**. Выполнение скрипта может занять некоторое время, особенно при использовании аргумента -copydata.

Ниже приведены обязательные аргументы.

|Имя|Описание|
|----|-----------|
|-s, --src-cluster | Укажите DNS-имя исходного кластера HBase, например -s hbsrccluster, --src-cluster=hbsrccluster. |
|-d, --dst-cluster | Укажите DNS-имя кластера назначения (реплики) HBase, например -s dsthbcluster, --src-cluster=dsthbcluster. |
|-sp, --src-ambari-password | Укажите пароль администратора для Ambari в исходном кластере HBase. |
|-dp, --dst-ambari-password | Укажите пароль администратора для Ambari в целевом кластере HBase.|

Необязательные аргументы для этой команды.

|Имя|Описание|
|----|-----------|
|-su, --src-ambari-user | Укажите имя пользователя-администратора для Ambari в исходном кластере HBase. Значение по умолчанию — **admin**. |
|-du, --dst-ambari-user | Укажите имя пользователя-администратора для Ambari в целевом кластере HBase. Значение по умолчанию — **admin**. |
|-t, --table-list | Укажите таблицы для репликации, например --table-list="table1;table2;table3". Если не указать таблицы, будут реплицированы все существующие таблицы HBase.|
|-m, --machine | Укажите головной узел, на котором будет выполняться действие сценария. Значение должно быть hn1 или hn0. Так как узел hn0 обычно более загружен, рекомендуется использовать hn1. Используйте этот параметр при запуске скрипта $0 как действия сценария из портала HDInsight или Azure PowerShell.|
|-ip | Этот аргумент является обязательным, если вы включаете репликацию между двумя виртуальными сетями. Этот аргумент действует как переключатель на использование статических IP-адресов узлов ZooKeeper реплицированных кластеров вместо полных доменных имен. Статические IP-адреса должны быть предварительно настроены перед включением репликации. |
|-cp, -copydata | Включает перенос существующих данных для таблиц, где включена репликация. |
|-rpm, -replicate-phoenix-meta | Включает репликацию для системных таблиц Phoenix. <br><br>*Используйте этот параметр с осторожностью.* Рекомендуется повторно создать таблицы Phoenix в реплицированных кластерах перед использованием этого скрипта. |
|-h, --help | Отображает сведения об использовании. |

Раздел [скрипта](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) print_usage() содержит подробное объяснение параметров.

После успешного развертывания действия сценария можно использовать протокол SSH, чтобы подключиться к кластеру назначения HBase и убедиться, что данные реплицированы.

### <a name="replication-scenarios"></a>Сценарии репликации

Ниже перечислены некоторые общие способы применения и используемые параметры.

- **Включение репликации для всех таблиц между двумя кластерами.** Этот сценарий не требует копирования и переноса существующих данных в таблицах и не использует таблицы Phoenix. Используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Включение репликации для отдельных таблиц.** Чтобы включить репликацию для table1, table2 и table3, используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Включение репликации для отдельных таблиц и копирование существующих данных.** Чтобы включить репликацию для table1, table2 и table3, используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Включение репликации для всех таблиц с репликацией метаданных Phoenix из источника в место назначения.** Репликация метаданных Phoenix работает не идеально, ее следует использовать с осторожностью.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Копирование и перенос данных

Существует два отдельных скрипта действия сценария для копирования или переноса данных после включения репликации:

- [Скрипт для небольших таблиц](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (размером в несколько гигабайт, ожидаемое время копирования — менее часа).

- [Скрипт для больших таблиц](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (копирование занимает больше часа).

Можно выполнить процедуру, описанную в разделе [Включение репликации](#enable-replication), чтобы вызвать действие сценария со следующими параметрами:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Раздел [скрипта](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) print_usage() содержит подробное описание параметров.

### <a name="scenarios"></a>Сценарии

- **Копирование определенных таблиц (test1, test2 и test3) для всех строк, измененных до настоящего момента (текущая отметка времени):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  или

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Копирование определенных таблиц с указанным интервалом времени:**

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Отключение репликации

Чтобы отключить репликацию, используйте другой скрипт действия сценария, расположенный на веб-сайте [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Можно выполнить процедуру, описанную в разделе [Включение репликации](#enable-replication), чтобы вызвать действие сценария со следующими параметрами:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

Раздел [скрипта](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) print_usage() содержит подробное объяснение параметров.

### <a name="scenarios"></a>Сценарии

- **Отключение репликации для всех таблиц:**

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  или

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **Отключение репликации для определенных таблиц (table1, table2 и table3):**

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы изучили настройку репликации HBase между двумя центрами обработки данных. Для получения дополнительных сведений о HDInsight и HBase см. следующие ресурсы:

* [Руководство по HBase. Приступая к работе с Apache HBase на Hadoop под управлением Windows в HDInsight][hdinsight-hbase-get-started]
* [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие с BigTable, для Hadoop][hdinsight-hbase-overview]
* [Создание кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]
* [Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight][hdinsight-hbase-twitter-sentiment]
* [Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

