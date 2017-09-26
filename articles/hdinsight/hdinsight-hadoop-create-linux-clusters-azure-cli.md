---
title: "Создание кластеров Hadoop с помощью командной строки в Azure HDInsight | Документы Майкрософт"
description: "Узнайте, как создавать кластеры HDInsight с кроссплатформенного Azure CLI 1.0."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8f2fcb46789d000cd66164508f1159338dcae5f9
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017

---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Создание кластеров HDInsight с помощью интерфейса командной строки Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Это пошаговое руководство содержит инструкции по созданию кластера HDInsight 3.5 с помощью Azure CLI 1.0.

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure CLI**. Действия, описанные в этом документе, проверены с помощью Azure CLI версии 0.10.14.

    > [!IMPORTANT]
    > Инструкции, приведенные в этом документе, не подходят для Azure CLI 2.0. Azure CLI 2.0 не поддерживает создание кластера HDInsight.

## <a name="log-in-to-your-azure-subscription"></a>Вход в подписку Azure

Выполните действия, описанные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md) , и подключитесь к подписке с помощью метода **login** .

## <a name="create-a-cluster"></a>Создание кластера

Описанные ниже действия следует выполнять в командной строке, например PowerShell или Bash.

1. Выполните следующую команду для аутентификации в подписке Azure.

        azure login

    Вам будет предложено указать имя пользователя и пароль. Если подписок Azure несколько, укажите, какую подписку должны использовать команды Azure CLI, с помощью метода `azure account set <subscriptionname>` .

2. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:

        azure config mode arm

3. Создайте группу ресурсов. Эта группа ресурсов будет содержать кластер HDInsight и соответствующую учетную запись хранения.

        azure group create groupname location

    * Замените `groupname` на уникальное имя группы.

    * Замените `location` на географический регион, в котором нужно создать группу.

       Для получения списка допустимых расположений выполните команду `azure location list`, а затем воспользуйтесь одним из расположений из столбца `Name`.

4. Создайте учетную запись хранения. Эта учетная запись хранения будет использоваться как хранилище по умолчанию для кластера HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Замените `groupname` на имя группы, созданной на предыдущем этапе.

    * Замените `location` на расположение, которое использовалось на предыдущем этапе.

    * Замените `storagename` на уникальное имя учетной записи хранения.

        > [!NOTE]
        > Дополнительные сведения о параметрах, используемых в этой команде, используйте `azure storage account create -h`, чтобы открыть справку по этой команде.

5. Извлеките ключ для доступа к учетной записи хранения.

        azure storage account keys list -g groupname storagename

    * Замените `groupname` на имя группы ресурсов.
    * Замените `storagename` на имя учетной записи хранения.

     Из полученных данных сохраните значение `key` параметра `key1`.

6. Создание кластера HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Замените `groupname` на имя группы ресурсов.

    * Замените `Hadoop` типом кластера, который хотите создать. Примеры: `Hadoop`, `HBase`, `Kafka`, `Spark` или `Storm`.

     > [!IMPORTANT]
     > Кластеры HDInsight бывают разных типов, которые соответствуют рабочей нагрузке или технологии, для которой предназначен кластер. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя.

    * Замените `location` на расположение, которое использовалось на предыдущих этапах.

    * Замените `storagename` на имя учетной записи хранения.

    * Замените `storagekey` на ключ, полученный на предыдущем этапе.

    * Для параметра `--defaultStorageContainer` используйте то же имя, что и для кластера.

    * Замените `admin` и `httppassword` именем и паролем, которые нужно использовать для доступа к кластеру по протоколу HTTPS.

    * Замените `sshuser` и `sshuserpassword` именем пользователя и паролем, которые нужно использовать для доступа к кластеру по протоколу SSH.

    > [!IMPORTANT]
    > Этот пример создает кластер с 2 рабочими узлами. После создания кластера можно изменить число рабочих узлов, выполнив операцию масштабирования. Если вы планируете использовать более 32 рабочих узлов, для головного узла необходимо выбрать по крайней мере 8-ядерный процессор и 14 ГБ ОЗУ. Настроить размер головного узла можно с помощью параметра `--headNodeSize` во время создания кластера.
    >
    > Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Создание кластера требует времени, обычно около 15 минут.

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight с помощью интерфейса командной строки Azure, обратитесь к следующим статьям, чтобы научиться работать с кластером:

### <a name="hadoop-clusters"></a>Кластеры Hadoop

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Кластеры HBase

* [Начало работы с HBase в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для HBase в HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Кластеры Storm

* [Разработка приложений Java для Storm в HDInsight](hdinsight-storm-develop-java-topology.md)
* [Использование компонентов Python в Storm в HDInsight](hdinsight-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологий с помощью Storm в HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

