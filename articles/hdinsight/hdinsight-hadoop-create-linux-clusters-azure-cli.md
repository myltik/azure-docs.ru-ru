<properties
   	pageTitle="Создание кластеров Hadoop, HBase и Storm на базе Linux в HDInsight с помощью кроссплатформенного Azure CLI | Microsoft Azure"
   	description="Узнайте, как создать кластеры HDInsight под управлением Linux с помощью кроссплатформенного Azure CLI, шаблонов диспетчера ресурсов Azure и Azure REST API. Вы можете указать тип кластера (Hadoop, HBase или Storm) либо использовать сценарии для установки настраиваемых компонентов."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="09/20/2016"
   	ms.author="larryfr"/>

#Создание кластеров под управлением Linux в HDInsight с помощью Azure CLI

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI представляет собой кроссплатформенную службу командной строки, с помощью которой можно управлять службами Azure. Она используется вместе с шаблонами управления ресурсами Azure для создания кластера HDInsight, а также связанных учетных записей хранения и других служб.

Шаблоны управления ресурсами Azure — это документы JSON, описывающие __группу ресурсов__ и все входящие в нее ресурсы (например, HDInsight). Такой подход позволяет определять все ресурсы, требуемые для работы с HDInsight, в один шаблон. Вы также можете управлять изменениями, применяемыми к группе, с помощью __развертываний__ (в этом случае изменения будут применены ко всей группе).

В этом документе описан поэтапный процесс создания нового кластера HDInsight с использованием шаблона и Azure CLI.

> [AZURE.IMPORTANT] При выполнении действий, описанных в этом документе, используется стандартное количество рабочих узлов (4) для кластера HDInsight. Если вы планируете использовать более 32 рабочих узлов (при создании или масштабировании кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
>
> Дополнительные сведения о размерах узлов и их стоимости см. в статье [Сведения о ценах на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Предварительные требования

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Интерфейс командной строки Azure__. Действия, описанные в этом документе, проверены с помощью Azure CLI версии 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Вход в подписку Azure

Выполните действия, описанные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md), и подключитесь к подписке с помощью метода __login__.

##Создание кластера

После установки и настройки Azure CLI в командной строке, оболочке или сеансе терминала сделайте следующее.

1. Выполните следующую команду для аутентификации в подписке Azure.

        azure login

    Вам будет предложено указать имя пользователя и пароль. Если подписок Azure несколько, укажите, какую подписку должны использовать команды Azure CLI, с помощью метода `azure account set <subscriptionname>`.

3. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:

        azure config mode arm

4. Создайте группу ресурсов. Эта группа ресурсов будет содержать кластер HDInsight и соответствующую учетную запись хранения.

        azure group create groupname location
        
    * Замените __groupname__ на уникальное имя для группы.
    * Замените __location__ на географический регион, в котором нужно создать группу.
    
        Для получения списка допустимых расположений выполните команду `azure location list`, а затем воспользуйтесь одним из расположений из столбца __Имя__.

5. Создайте учетную запись хранения. Эта учетная запись хранения будет использоваться как хранилище по умолчанию для кластера HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Замените __groupname__ на имя группы, созданной на предыдущем этапе.
     * Замените __location__ на расположение, которое использовалось на предыдущем этапе.
     * Замените __storagename__ на уникальное имя учетной записи хранения.
     
     > [AZURE.NOTE] Дополнительные сведения о параметрах, используемых в этой команде, используйте `azure storage account create -h`, чтобы открыть справку по этой команде.

5. Извлеките ключ для доступа к учетной записи хранения.

        azure storage account keys list -g groupname storagename
        
    * Замените __groupname__ на имя группы ресурсов.
    * Замените __storagename__ на имя учетной записи хранения.
    
    Из полученных данных сохраните значение __key__ для параметра __key1__.

6. Создание кластера HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Замените __groupname__ на имя группы ресурсов.
    * Замените __location__ на расположение, которое использовалось на предыдущем этапе.
    * Замените __storagename__ на имя учетной записи хранения.
    * Замените __storagekey__ на ключ, полученный при выполнении предыдущего шага.
    * Для параметра `--defaultStorageContainer` используйте то же имя, что и для кластера.
    * Замените __admin__ и __httppassword__ на имя пользователя и пароль, которые хотите использовать для доступа к кластеру по протоколу HTTPS.
    * Замените __sshuser__ и __sshuserpassword__ на имя пользователя и пароль, которые хотите использовать для доступа к кластеру по протоколу SSH.

    Создание кластера требует времени, обычно около 15 минут.

##Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight с помощью интерфейса командной строки Azure, обратитесь к следующим статьям, чтобы научиться работать с кластером:

###Кластеры Hadoop

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

###Кластеры HBase

* [Начало работы с HBase в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для HBase в HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Кластеры Storm

* [Разработка приложений Java для Storm в HDInsight](hdinsight-storm-develop-java-topology.md)
* [Использование компонентов Python в Storm в HDInsight](hdinsight-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологий со Storm в HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0921_2016-->