<properties
   	pageTitle="Создание кластеров Hadoop, HBase и Storm на базе Linux в HDInsight с помощью кроссплатформенного Azure CLI | Microsoft Azure"
   	description="Узнайте, как создать кластеры HDInsight под управлением Linux с помощью кроссплатформенного Azure CLI, шаблонов диспетчера ресурсов Azure и Azure REST API. Вы можете указать тип кластера (Hadoop, HBase или Storm) либо использовать сценарии для установки настраиваемых компонентов."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/22/2016"
   	ms.author="larryfr"/>

#Создание кластеров под управлением Linux в HDInsight с помощью Azure CLI

[AZURE.INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure CLI представляет собой кроссплатформенную службу командной строки, с помощью которой можно управлять службами Azure. Она используется вместе с шаблонами управления ресурсами Azure для создания кластера HDInsight, а также связанных учетных записей хранения и других служб.

Шаблоны управления ресурсами Azure представляют собой документы JSON, описывающие __группу ресурсов__ и все входящие в нее ресурсы (например, HDInsight). Подход на основе шаблонов позволяет определить все необходимые для HDInsight ресурсы в одном шаблоне и управлять всеми изменениями в группе с помощью __развертываний__, применяющих изменения ко всей группе.

В этом документе описан поэтапный процесс создания нового кластера HDInsight с использованием шаблона и Azure CLI.

> [AZURE.IMPORTANT] При выполнении действий, описанных в этом документе, используется стандартное количество рабочих узлов (4) для кластера HDInsight. Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
>
> Дополнительные сведения о размерах узлов и их стоимости см. в статье [Сведения о ценах на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Предварительные требования

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Интерфейс командной строки Azure__. Сведения об установке интерфейса командной строки Azure см. в разделе [Установка интерфейса командной строки Azure](../xplat-cli-install.md).

##Вход в подписку Azure

Выполните действия, описанные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md), и подключитесь к подписке с помощью метода __login__.

##Создание кластера

После установки и настройки Azure CLI в командной строке, оболочке или сеансе терминала необходимо выполнить следующие действия.

1. Выполните следующую команду для аутентификации в подписке Azure.

        azure login

    Система предложит вам указать имя пользователя и пароль. Если подписок Azure несколько, укажите, какую подписку должны использовать команды Azure CLI, с помощью метода `azure account set <subscriptionname>`.

3. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:

        azure config mode arm

4. Создайте шаблон для кластера HDInsight. Вот некоторые основные примеры шаблонов:

    * [Кластер под управлением Linux с использованием открытого ключа SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-publickey)
    * [Кластер под управлением Linux с использованием пароля для учетной записи SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)

    Оба эти шаблона также создают учетную запись хранения Azure, которую HDInsight использует по умолчанию.

    Вам потребуются файлы __azuredeploy.json__ и __azuredeploy.parameters.json__. Скопируйте эти файлы на локальный компьютер, прежде чем продолжить.

5. Откройте файл __azuredeploy.parameters.json__ в редакторе и укажите значения для элементов в разделе `parameters`.

    * __location__: центр обработки данных, в котором будут созданы ресурсы. Список допустимых расположений см. в разделе `location` файла __azuredeploy.json__.
    * __clusterName__: имя кластера HDInsight. Это имя должно быть уникальным, иначе произойдет сбой развертывания.
    * __clusterStorageAccountName__: имя учетной записи хранения Azure, которая будет создана для кластера HDInsight. Это имя должно быть уникальным, иначе произойдет сбой развертывания.
    * __clusterLoginPassword__: пароль администратора кластера. Пароль должен быть надежным, поскольку используется для доступа к веб-сайтам и службам REST в кластере.
    * __sshUserName__: имя первого пользователя SSH, созданного для этого кластера. SSH будет использоваться для удаленного доступа к кластеру с использованием этой учетной записи.
    * __sshPublicKey__: если вы используете шаблон, для которого требуется открытый ключ SSH, в эту строку необходимо добавить открытый ключ. Дополнительные сведения о создании открытых ключей и работе с ними см. в следующих статьях:

        * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: если вы используете шаблон, для которого требуется пароль SSH, в эту строку необходимо добавить пароль.

    Закончив настройку, сохраните и закройте файл.

5. Для создания пустой группы ресурсов выполните указанную ниже команду. Замените __RESOURCEGROUPNAME__ именем, которое хотите использовать для этой группы. Вместо __LOCATION__ укажите центр обработки данных, в котором необходимо создать кластер HDInsight.

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE] Если имя расположения содержит пробелы, заключите его в кавычки. Например, "Юг центральных США".

6. Чтобы создать начальное развертывание для этой группы ресурсов, выполните указанную ниже команду. Замените __PATHTOTEMPLATE__ путем к файлу шаблона __azuredeploy.json__. Замените __PATHTOPARAMETERSFILE__ на путь к файлу __azuredeploy.parameters.json__. Замените __RESOURCEGROUPNAME__ именем группы, созданной на предыдущем этапе.

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Когда вы подтвердите развертывание, должно отобразиться сообщение, похожее на следующее: `group deployment create command ok`.

7. Развертывание может занять определенное время, примерно 15 минут. Чтобы просмотреть информацию о развертывании, выполните указанную ниже команду. Замените __RESOURCEGROUPNAME__ именем группы ресурсов, созданной на предыдущем этапе.

        azure group log show -l RESOURCEGROUPNAME
    
    После завершения развертывания в поле __Состояние__ отобразится значение __Успешно__. Если во время развертывания произойдет сбой, получить дополнительные сведения об этом сбое можно будет с помощью следующей команды:

        azure group log show -l -v RESOURCEGROUPNAME

##Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight, обратитесь к следующим статьям, чтобы научиться работать с кластером:

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

<!---HONumber=AcomDC_0128_2016-->