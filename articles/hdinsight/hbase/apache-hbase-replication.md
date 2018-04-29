---
title: Настройка репликации кластера HBase в виртуальных сетях Azure | Документация Майкрософт
description: Сведения о том, как настроить репликацию HBase с одной версии HDInsight на другую для балансировки нагрузки, обеспечения высокого уровня доступности, переноса или обновления без простоя и аварийного восстановления.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: c28c48b5842deec9d9c3898c5742c3d4d473094e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Настройка репликации кластера HBase в виртуальных сетях Azure

В этой статье вы узнаете, как настроить репликацию HBase в одной или между двумя виртуальными сетями в Azure.

Репликация кластера использует методологию source-push. Кластер HBase может быть исходным, кластером назначения или выполнять обе роли одновременно. Репликация выполняется асинхронно. Целью репликации в конечном итоге является согласованность. При получении источником изменения в семействе столбцов с включенной репликацией такое изменение распространяется на все кластеры назначения. При репликации данных с одного кластера на другой исходный кластер и все кластеры, которые уже потребили данные, отслеживаются для предотвращения циклических репликаций.

В этом руководстве показано, как настроить репликацию "источник — назначение". Другие топологии кластеров см. в [справочном руководстве по Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Примеры использования репликации HBase для одной виртуальной сети:

* Балансировка нагрузки. Например, выполнения проверки или заданий MapReduce в целевом кластере и приема данных на исходном кластере.
* Добавление высокого уровня доступности.
* Перенос данных из одного кластера HBase в другой.
* Обновление кластера Azure HDInsight до другой версии.

Примеры использования репликации HBase для двух виртуальных сетей:

* Настройка аварийного восстановления.
* Балансировка нагрузки и секционирование приложения.
* Добавление высокого уровня доступности.

Кластеры можно реплицировать с помощью скриптов [действий сценария](../hdinsight-hadoop-customize-cluster-linux.md), которые можно найти на [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. См. страницу о [получении бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Настройка сред

Доступны три варианта конфигурации:

- два кластера HBase в одной виртуальной сети Azure;
- два кластера HBase в двух виртуальных сетях в одном регионе;
- два кластера HBase в двух виртуальных сетях в двух регионах (георепликация).

Чтобы помочь вам в настройке сред мы создали некоторые [шаблоны Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Если вы предпочитаете настраивать среды с помощью других методов, см. следующие статьи:

- [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Создание кластеров HBase в виртуальной сети Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Настройка одной виртуальной сети

Чтобы создать два кластера HBase в одной виртуальной сети, нажмите расположенную ниже кнопку. Шаблон хранится среди [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Настройка двух виртуальных сетей в одном регионе

Чтобы создать две виртуальные сети с пиринговой связью и два кластера HBase в одном регионе, нажмите расположенную ниже кнопку. Шаблон хранится среди [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



В этом случае необходима [пиринговая связь между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md). Этот шаблон включает пиринговую связь между виртуальными сетями.   

Репликация HBase использует IP-адреса виртуальных машин ZooKeeper. Необходимо настроить статические IP-адреса для узлов назначения ZooKeeper HBase.

**Настройка статических IP-адресов**

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева выберите **Группы ресурсов**.
3. Выберите группу ресурсов, содержащую кластер назначения HBase. Это группа ресурсов, указанная при использовании шаблона Resource Manager для создания среды. Для сужения списка можно использовать фильтр. Вы увидите список ресурсов, содержащий две виртуальные сети.
4. Выберите виртуальную сеть, содержащую кластер назначения HBase. Например, **xxxx-vnet2**. Появится три устройства с именами, которые начинаются с **nic-zookeepermode-**. Это три виртуальные машины ZooKeeper.
5. Выберите одну из виртуальных машин ZooKeeper.
6. Щелкните **IP configurations** (Конфигурации IP).
7. В списке выберите **ipConfig1**.
8. Выберите **Статический** и скопируйте или запишите фактический IP-адрес. IP-адрес нужен при запуске действия сценария для включения репликации.

  ![Репликация HDInsight HBase, статический IP-адрес узла ZooKeeper](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Повторите шаг 6, чтобы задать статический IP-адрес для двух остальных узлов ZooKeeper.

Когда вы вызываете действие сценария `hdi_enable_replication.sh` для сценария c виртуальными сетями в разных регионах, необходимо использовать параметр **-ip**.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Настройка двух виртуальных сетей в двух разных регионах

Чтобы создать две виртуальные сети в двух разных регионах и соединить их с помощью VPN-подключение, щелкните расположенное ниже изображение. Шаблон хранится среди [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Ниже приведены некоторые жестко заданные значения в шаблоне.

**VNet 1**

| Свойство | Значение |
|----------|-------|
| Расположение | Запад США |
| Имя виртуальной сети | &lt;префикс_имени_кластера>-vnet1 |
| Address space prefix | 10.1.0.0/16 |
| Имя подсети | subnet 1 |
| Subnet prefix | 10.1.0.0/24 |
| Subnet (gateway) name | GatewaySubnet (не может быть изменено) |
| Префикс подсети (шлюза). | 10.1.255.0/27 |
| Имя шлюза. | vnet1gw |
| Тип шлюза | Vpn |
| Тип VPN шлюза. | RouteBased |
| Gateway SKU | базовая; |
| Gateway IP | vnet1gwip |
| Имя кластера, | &lt;префикс_имени_кластера>1 |
| Cluster version | 3.6 |
| Cluster kind | hbase |
| Cluster worker node count | 2 |


**VNet 2**

| Свойство | Значение |
|----------|-------|
| Расположение | Восток США |
| Имя виртуальной сети | &lt;префикс_имени_кластера>-vnet2 |
| Address space prefix | 10.2.0.0/16 |
| Имя подсети | subnet 1 |
| Subnet prefix | 10.2.0.0/24 |
| Subnet (gateway) name | GatewaySubnet (не может быть изменено) |
| Префикс подсети (шлюза). | 10.2.255.0/27 |
| Имя шлюза. | vnet2gw |
| Тип шлюза | Vpn |
| Тип VPN шлюза. | RouteBased |
| Gateway SKU | базовая; |
| Gateway IP | vnet1gwip |
| Имя кластера, | &lt;префикс_имени_кластера>2 |
| Cluster version | 3.6 |
| Cluster kind | hbase |
| Cluster worker node count | 2 |

Репликация HBase использует IP-адреса виртуальных машин ZooKeeper. Необходимо настроить статические IP-адреса для узлов назначения ZooKeeper HBase. Сведения о настройке статического IP-адреса см. в разделе [Настройка двух виртуальных сетей в одном регионе](#set-up-two-virtual-networks-in-the-same-region) в этой статье.

Когда вы вызываете действие сценария `hdi_enable_replication.sh` для сценария c виртуальными сетями в разных регионах, необходимо использовать параметр **-ip**.

## <a name="load-test-data"></a>Загрузка тестовых данных

При репликации кластера необходимо указать реплицируемые таблицы. В этом разделе вы загрузите данные в исходный кластер. В следующем разделе вы включите репликацию между двумя кластерами.

Чтобы создать таблицу [Contacts](apache-hbase-tutorial-get-started-linux.md) и вставить в нее некоторые данные, следуйте инструкциям, приведенным в статье **Начало работы с примером Apache HBase в HDInsight**.

## <a name="enable-replication"></a>Включение репликации

Ниже описано, как вызвать скрипт действия сценария на портале Azure. Дополнительные сведения о том, как выполнить действие сценария с помощью Azure PowerShell и программы командной строки Azure, см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](../hdinsight-hadoop-customize-cluster-linux.md).

**Включение репликации HBase на портале Azure**

1. Войдите на [портале Azure](https://portal.azure.com).
2. Откройте исходный кластер HBase.
3. В меню кластера выберите **Действия скрипта**.
4. В верхней части страницы выберите **Отправить новое**.
5. Выберите или введите следующие сведения.

  1. **Имя:** введите **Включение репликации**.
  2. **URI bash-скрипта.** Введите **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Головной узел**. Выберите этот тип узла. Отмените выбор других типов узлов.
  4. **Параметры**: Параметры в следующем примере позволяют включить репликацию для всех существующих таблиц, а затем копировать все данные из исходного кластера в целевой.

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Используйте имя узла вместо полного доменного имени (FQDN) для DNS-имени как исходного, так и целевого кластера.

6. Нажмите кнопку **Создать**. Выполнение скрипта может занять некоторое время, особенно при использовании аргумента **-copydata**.

Ниже приведены обязательные аргументы.

|ИМЯ|ОПИСАНИЕ|
|----|-----------|
|-s, --src-cluster | Указывает DNS-имя исходного кластера HBase. например -s hbsrccluster, --src-cluster=hbsrccluster. |
|-d, --dst-cluster | Указывает DNS-имя кластера назначения (реплики) HBase. например -s dsthbcluster, --src-cluster=dsthbcluster. |
|-sp, --src-ambari-password | Указывает пароль администратора для Ambari в исходном кластере HBase. |
|-dp, --dst-ambari-password | Указывает пароль администратора для Ambari в целевом кластере HBase.|

Необязательные аргументы для этой команды.

|ИМЯ|ОПИСАНИЕ|
|----|-----------|
|-su, --src-ambari-user | Указывает имя пользователя-администратора для Ambari в исходном кластере HBase. Значение по умолчанию — **admin**. |
|-du, --dst-ambari-user | Указывает имя пользователя-администратора для Ambari в целевом кластере HBase. Значение по умолчанию — **admin**. |
|-t, --table-list | Указывает таблицы для репликации. например --table-list="table1;table2;table3". Если не указать таблицы, будут реплицированы все существующие таблицы HBase.|
|-m, --machine | Указывает головной узел, на котором будет выполняться действие сценария. Значение должно быть **hn1** или **hn0**. Мы рекомендуем использовать **hn1**, так как головной узел **hn0** обычно более загружен. Используйте этот параметр при запуске скрипта $0 как действия сценария из портала HDInsight или Azure PowerShell.|
|-ip | Этот аргумент является обязательным, если вы включаете репликацию между двумя виртуальными сетями. Этот аргумент действует как переключатель на использование статических IP-адресов узлов ZooKeeper реплицированных кластеров вместо полных доменных имен. Перед включением репликации необходимо предварительно настроить статические IP-адреса. |
|-cp, -copydata | Включает перенос существующих данных для таблиц, где включена репликация. |
|-rpm, -replicate-phoenix-meta | Включает репликацию для системных таблиц Phoenix. <br><br>*Используйте этот параметр с осторожностью.* Рекомендуется повторно создать таблицы Phoenix в реплицированных кластерах перед использованием этого скрипта. |
|-h, --help | Отображает сведения об использовании. |

Раздел `print_usage()` [скрипта](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) содержит подробное описание параметров.

После успешного развертывания действия сценария можно использовать протокол SSH, чтобы подключиться к кластеру назначения HBase, а после убедиться, что данные реплицированы.

### <a name="replication-scenarios"></a>Сценарии репликации

Ниже перечислены некоторые общие способы применения и используемые параметры.

- **Включение репликации для всех таблиц между двумя кластерами.** В этом сценарии не требуется копирование или перенос существующих данных в таблицах и не используются таблицы Phoenix. Используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Включение репликации для отдельных таблиц.** Чтобы включить репликацию для table1, table2 и table3, используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Включение репликации для отдельных таблиц и копирование существующих данных**. Чтобы включить репликацию для table1, table2 и table3, используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Включение репликации для всех таблиц с репликацией метаданных Phoenix из источника в место назначения**. Репликация метаданных Phoenix работает не идеально. Ее следует использовать с осторожностью. Используйте следующие параметры:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Копирование и перенос данных

Существует два отдельных скрипта действия сценария для копирования или переноса данных после включения репликации:

- [Скрипт для маленьких таблиц](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (таблицы размером в несколько гигабайт, ожидаемое время их копирования — менее одного часа).

- [Скрипт для больших таблиц](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (таблицы, копирование которых занимает больше одного часа).

Выполните ту же процедуру, описанную в разделе [Включение репликации](#enable-replication) для вызова действия сценария. Используйте следующие параметры:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Раздел `print_usage()` [скрипта](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) содержит подробное описание параметров.

### <a name="scenarios"></a>Сценарии

- **Копирование определенных таблиц (test1, test2 и test3) для всех строк, измененных до настоящего момента (текущая отметка времени):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Или сделайте так:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Копирование определенных таблиц с указанным интервалом времени**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Отключение репликации

Чтобы отключить репликацию, используйте другой пример действия сценария из [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Выполните ту же процедуру, описанную в разделе [Включение репликации](#enable-replication) для вызова действия сценария. Используйте следующие параметры:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

Раздел `print_usage()` [скрипта](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) содержит подробное описание параметров.

### <a name="scenarios"></a>Сценарии

- **Отключение репликации для всех таблиц:**

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  или

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Отключение репликации для определенных таблиц (table1, table2 и table3):**

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы научились настраивать репликацию HBase в пределах одной виртуальной сети или между двумя виртуальными сетями. Дополнительные сведения о HDInsight и HBase см.в следующих статьях:

* [Начало работы с примером Apache HBase в HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие BigTable, для Hadoop](./apache-hbase-overview.md)
* [Создание кластеров HBase в виртуальной сети Azure](./apache-hbase-provision-vnet.md)

