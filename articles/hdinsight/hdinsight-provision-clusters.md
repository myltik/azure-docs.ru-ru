<properties
   pageTitle="Создание кластеров Hadoop в HDInsight | Microsoft Azure"
   	description="Узнайте, как выполнить создание кластеров Azure HDInsight с помощью портала Azure, Azure PowerShell, командной строки или пакета SDK для HDInsight .NET."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/16/2015"
   ms.author="jgao"/>

# Создание кластеров Hadoop в HDInsight

Узнайте, как спланировать создание кластеров HDInsight.


###Предварительные требования:

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Базовые параметры конфигурации

Ниже приведены базовые параметры конфигурации для создания кластера HDInsight.

- **Имя кластера**

	Имя кластера используется для его идентификации. Оно должно соответствовать следующим требованиям:

	- поле должно представлять собой строку длиной от 3 до 63 символов;
	- поле может содержать только буквы, цифры и дефисы.

- **Название подписки**

	Кластер HDInsight привязывается к одной подписке Azure.

- **Имя группы ресурсов**

	Диспетчер ресурсов Azure (ARM) позволяет работать с ресурсами в приложении в виде группы, которая называется группой ресурсов Azure. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции. Дополнительную информацию см. в статье [Общие сведения о Диспетчере ресурсов Azure](resource-group-overview.md).
	
- **Операционная система**

	Вы можете создать кластеры HDInsight в одной из следующих операционных систем: **Windows (Windows Server 2012 R2 Datacenter)**; **Linux (Ubuntu 12.04 LTS для Linux)**. HDInsight позволяет настраивать кластеры Linux в Azure. Настройте кластер Linux, если вы знакомы с Linux или Unix, выполняете миграцию из существующего решения Hadoop на платформе Linux или хотите с легкостью выполнить интеграцию с компонентами экосистемы Hadoop, созданными для Linux. Дополнительную информацию см. в разделе [Приступая к работе с Hadoop в Linux на платформе HDInsight](hdinsight-hadoop-linux-get-started.md).

- **Тип кластера** и **размер кластера (узлы данных)**

	HDInsight позволяет клиентам развертывать различные типы кластеров для рабочих нагрузок анализа различных данных. На сегодняшний день доступны следующие типы кластеров:

	- кластеры Hadoop — для запросов и анализа;
	- кластеры HBase — для NoSQL;
	- кластеры Storm — для обработки событий в реальном времени;
	- кластеры Spark (предварительная версия) — для обработки в оперативной памяти, интерактивных запросов, потоков и машинного обучения.

	![Кластеры HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]*Кластер Azure HDInsight* также называется *кластером Hadoop в HDInsight* или *кластером HDInsight*. Иногда также используется термин *кластер Hadoop*. Все они обозначают кластеры Hadoop, размещенные в среде Microsoft Azure.

	Разные узлы кластера каждого типа могут выполнять разные роли. Клиенты могут настраивать размеры узлов в соответствии с нагрузкой в рамках определенной роли. Например, рабочие узлы кластера Hadoop можно создать с большим объемом памяти, если они активно используются для выполнения аналитических задач.

	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Кластеры Hadoop для HDInsight развертываются с двумя ролями:

	- головной узел (два узла);
	- узел данных (по крайней мере один узел).

	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Кластеры HBase для HDInsight развертываются с тремя ролями: головные серверы (два узла), региональные серверы (по крайней мере один узел) и узлы Master/Zookeeper (три узла).

	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Кластеры Storm для HDInsight развертываются с тремя ролями: узлы Nimbus (два узла), наблюдающие серверы (по крайней мере один узел) и узлы Zookeeper (три узла).


	![Роли кластера HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Кластеры Spark для HDInsight развертываются с тремя ролями: головной узел (два узла), рабочий узел (по крайней мере один узел) и узлы Zookeeper (три узла; уровень A1 — бесплатно).

	Клиенты оплачивают использование этих узлов на протяжении всего срока существования кластера. Начисление начинается сразу после создания кластера и завершается при его удалении (нельзя высвободить память, выделенную для кластеров, или приостановить их работу). На размер оплаты влияет размер кластера. Для учебных целей рекомендуется использовать один узел данных. Дополнительные сведения о ценах на HDInsight см. в статье [Цены на HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]Максимальный размер кластера зависит от подписки Azure. Чтобы увеличить лимит, обратитесь в службу поддержки по вопросам выставления счетов.

- **Версия HDInsight**

	Этот параметр используется для определения версии HDInsight для кластера. Дополнительные сведения см. в статье [Версии и компоненты кластеров Hadoop в HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).


- **Расположение (регион)**

	Кластер HDInsight должен находиться в том же расположении Azure, что и его учетная запись хранения, используемая по умолчанию.
	
	![Регионы Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Чтобы просмотреть поддерживаемые регионы, откройте раскрывающийся список **Регион** на странице [Цены на HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Размер узла**

	![размеры узла виртуальной машины hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Выберите размеры виртуальной машины для узлов. Дополнительные сведения см. в статье [Размеры для облачных служб](cloud-services-sizes-specs.md).

	Цены зависят от размера выбранных виртуальных машин. HDInsight поддерживает для узлов кластера все виртуальные машины стандартного уровня. Дополнительную информацию о том, как размер виртуальной машины влияет на цены, см. в разделе <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight Цены</a>.


- **Пользователи HDInsight**

	Кластеры HDInsight позволяют во время подготовки настроить две учетные записи пользователей.

	- Пользователь HTTP. По умолчанию используется имя admin и базовая конфигурация на портале Azure.
	- Пользователь RDP (кластеры Windows). Она используется для подключения к кластеру через RDP. При создании учетной записи необходимо задать дату окончания срока действия не позднее 90 дней с текущей даты.
	- Пользователь SSH (кластеры Linux). Она используется для подключения к кластеру через SSH. Дополнительные учетные записи пользователей SSH можно создать после создания кластера, следуя инструкциям в статье [Использование SSH с Hadoop в HDInsight на платформе Linux в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Учетная запись хранения Azure.**

	HDFS использует для хранения данных несколько локальных дисков в кластере, а HDInsight — хранилище BLOB-объектов Azure. Хранилище BLOB-объектов Azure — это надежное, универсальное решение, которое полностью интегрируется с HDInsight. С помощью интерфейса распределенной файловой системы Hadoop (HDFS) все компоненты HDInsight могут напрямую взаимодействовать со структурированными или неструктурированными данными в хранилище больших двоичных объектов. Хранение данных в хранилище BLOB-объектов позволяет безопасно и без потери пользовательских данных удалять используемые для расчетов кластеры HDInsight.

	В процессе настройки необходимо задать учетную запись хранения Azure и контейнер для хранения BLOB-объектов. В некоторых сценариях создания учетную запись и контейнер потребуется создать заранее. Кластер будет использовать этот контейнер в качестве хранилища по умолчанию. Вы также можете указать дополнительные учетные записи хранения Azure (связанные хранилища), к которым кластер получит доступ. Кластер также сможет получать доступ к любым контейнерам BLOB-объектов, настроенным с полным общим доступом для чтения или общим доступом для чтения только на уровне BLOB-объектов. Дополнительные сведения об ограничении доступа см. в статье [Управление доступом к ресурсам службы хранилища Azure](storage-manage-access-to-resources.md).

	![Хранилище HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]BLOB-объекты группируются в контейнерах следующим образом.

	![Хранилище blob-объектов Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]Не используйте один контейнер хранилища больших двоичных объектов для нескольких кластеров. Это не поддерживается.

	Дополнительную информацию об использовании дополнительных хранилищ больших двоичных объектов см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-use-blob-storage.md).

- **Метахранилище Hive/Oozie**

	Метахранилище содержит метаданные Hive и Oozie, такие как таблицы, секции, схемы и столбцы Hive. Использование метахранилища помогает сохранять метаданные Hive и Oozie, чтобы не создавать повторно таблицы Hive или задания Oozie при создании нового кластера. Для хранения подобной информации Hive по умолчанию использует встроенную базу данных SQL Azure. Встроенная база данных не сохраняет метаданные при удалении кластера. Предположим, вы создали кластер с метахранилищем Hive. и создали некоторые таблицы Hive. Если после удаления кластера вы повторно создадите его с использованием того же метахранилища Hive, то таблицы Hive, созданные в исходном кластере, будут доступны.
    
    > [AZURE.NOTE]Возможность настройки метахранилища не поддерживается для кластеров типа HBase.

## Расширенные параметры конфигурации

### Настройка кластеров HDInsight

В некоторых случаях требуется изменить файлы конфигурации.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

При повторном развертывании образа изменения в кластере не сохраняются. Дополнительные сведения см. в разделе [Перезапуск экземпляра роли из-за обновлений ОС](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Чтобы они сохранялись в течение всего времени существования кластера, можно в процессе создания изменить файлы конфигурации кластера HDInsight.

Ниже приведен пример сценария Azure PowerShell, который меняет конфигурацию Hive:

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

Вот еще несколько примеров изменения других файлов конфигурации:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Дополнительные сведения см. в статье Азима Уддина (Azim Uddin) [Дополнительная настройка при создании кластера HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Настройка кластеров с помощью действия сценария

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время создания. Такие сценарии вызываются с помощью **действия сценария**, которое является параметром конфигурации и может использоваться посредством портала, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительную информацию см. в статье [Настройка кластера HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).


### Использование виртуальных сетей Azure

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа "сеть-сеть" или "точка-сеть") при помощи виртуальной частной сети (VPN).

	Конфигурация "сервер-сервер" позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Конфигурация "точка-сеть" позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Дополнительные сведения см. в статье [Создание кластера Hadoop для виртуальной сети](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает совместные групповые виртуальные сети. Чтобы проверить, является ли существующая виртуальная сеть Azure географически привязанной, используйте командлет Azure PowerShell Get-AzureVNetConfig. Если у вашей виртуальной сети нет географической привязки, вы можете:
>
> - Экспортировать конфигурацию существующей виртуальной сети, а затем создать новую виртуальную сеть. У всех новых виртуальных сетей по умолчанию есть географическая привязка.
> - Выполнить миграцию в географически привязанную виртуальную сеть. Дополнительные сведения см. в статье [Перенос существующих служб на региональный уровень](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

## Создание с помощью портала

Описание полей см. в статьях [Базовые параметры конфигурации](#basic-configuration-options) и [Расширенные параметры конфигурации](#advanced-configuration-options).

**Создание кластера HDInsight**

1. Войдите на [портал Azure][azure-preview-portal].
2. Щелкните **СОЗДАТЬ**, **Аналитика данных**, а затем — **HDInsight**.

    ![Создание нового кластера на портале Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Создание нового кластера на портале Azure")

3. Введите или выберите следующие значения:

  * **Имя кластера**: введите имя кластера. Если имя кластера доступно, рядом с ним появится зеленый флажок.
  * **Тип кластера**: выберите **Hadoop**.
  * **Операционная система кластера**: выберите **Windows Server 2012 R2 Datacenter**.
  * **Подписка**: выберите подписку Azure, которая будет использоваться для создания этого кластера.
  * **Группа ресурсов**: выберите существующую группу ресурсов или создайте новую. Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).
  * **Учетные данные**: настройте имя пользователя и пароль для пользователя Hadoop (пользователя HTTP). При включении удаленного рабочего стола для кластера необходимо настроить имя пользователя удаленного рабочего стола и пароль, а также указать дату окончания срока действия учетной записи. Щелкните **Выбрать** внизу страницы, чтобы сохранить изменения.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Источник данных**: выберите существующую или создайте новую учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Ценовые категории узлов**: задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Необязательная конфигурация**: позволяет выбрать версию кластера, а также настроить другие необязательные параметры, например присоединение **виртуальной сети** и конфигурацию **внешнего метахранилища** для хранения данных Hive и Oozie. Используйте действия сценариев, чтобы настроить кластер для установки пользовательских компонентов или использовать дополнительные учетные записи хранения с кластером.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows based HDInsight cluster can only be placed into a classical virtual network.
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Щелкните **Создать**. Выберите **Закрепить на начальной панели**, чтобы добавить элемент кластера на начальную панель портала. Значок указывает, что выполняется создание кластера. После завершения создания вместо него будет отображаться значок HDInsight.


	| При создании | Создание завершено |
	| ------------------ | --------------------- |
	| ![Индикатор подготовки на начальной панели](./media/hdinsight-provision-clusters/provisioning.png) | ![Плитка подготовки кластера](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки можно с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.
	

5. После завершения создания щелкните элемент кластера на начальной панели, чтобы открыть колонку кластера. Колонка кластера содержит важные сведения о кластере: имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.


	![Колонка "Кластер"](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Свойства кластера")


	Ниже приведено описание значков в верхней части этой колонки и в разделе **Основные компоненты**.


	* **Параметры** и **Все параметры**. Позволяет отобразить колонку **Параметры** кластера, которая содержит подробные сведения о конфигурации кластера.  

	* **Панель мониторинга**, **Панель мониторинга кластера** и **URL-адрес**. Позволяют задать пути доступа к панели мониторинга кластера, которая представляет собой веб-портал для выполнения заданий в кластере.
	* **Удаленный рабочий стол**. Позволяет включать или отключать удаленный рабочий стол на узлах кластера.
	* **Масштабировать кластер**. Позволяет изменить количество рабочих узлов для этого кластера.
	* **Удалить**. Позволяет удалить кластер HDInsight.
	* **Быстрый старт** (![значок облака и молнии = быстрый запуск](./media/hdinsight-provision-clusters/quickstart.png)). Позволяет отобразить сведения, которые помогут начать работу с HDInsight.
	* **Пользователи (![значок "пользователи"](./media/hdinsight-provision-clusters/users.png))**. Позволяет настроить разрешения на _управление кластером_ с помощью портала для других пользователей в подписке Azure.
	

		> [AZURE.IMPORTANT]Этот параметр влияет _только_ на доступ и разрешения для этого кластера на портале предварительной версии и не определяет пользователей, которые могут подключаться к кластеру HDInsight или отправлять в него задания.
		
	* **Теги** (![значок "теги"](./media/hdinsight-provision-clusters/tags.png)). Позволяет задавать пары "ключ-значение" для определения пользовательской таксономии облачных служб. Например, вы можете создать ключ с именем __project__, а затем использовать общее значение для всех служб, связанных с определенным проектом.

## Создание с помощью шаблона ARM

Шаблоны диспетчера ресурсов Azure (ARM) упрощают развертывание и повторное развертывание кластера. В следующей процедуре создается кластер HDInsight под управлением Linux.

**Развертывание кластера с помощью шаблона ARM**

1. Сохраните JSON-файл из [приложения А](#appendix-a---arm-template) на своей рабочей станции.
2. При необходимости создайте параметры.
3. Запустите шаблон с помощью следующего сценария PowerShell:

		$subscriptionId = "<Azure Subscription ID"
		
		$newResourceGroupName = "<Azure Resource Group Name>"
		$Location = "EAST US 2" # for creating ARM group
				
		$armDeploymentName = "New-HDInsigt-Cluster-" + (Get-Date -Format MMdd)
		$newClusterName = "<HDInsight Cluster Name>"
		$clusterStorageAccountName = "<Default Storage Account Name>"
				
		# Connect to Azure
		#Login-AzureRmAccount
		#Select-AzureRmSubscription -SubscriptionId $subscriptionId
				
		# Create a resource group
		New-AzureRmResourceGroup -Name $newResourceGroupName -Location $Location
				
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$newClusterName";clusterStorageAccountName="$clusterStorageAccountName"}
				
		New-AzureRmResourceGroupDeployment `
			-Name $armDeploymentName `
			-ResourceGroupName $newResourceGroupName `
			-TemplateFile E:\HDITutorials-ARM\Create-clusters\hdinsight-arm-template.json `
			-TemplateParameterObject $parameters
				
		# List cluster
		Get-AzureRmHDInsightCluster -ResourceGroupName $newResourceGroupName -ClusterName $newClusterName 

	Скрипт PowerShell настраивает только имя кластера и имя учетной записи хранения. Вы можете задать другие значения в шаблоне ARM.
	
Сведения о развертывании шаблона ARM с помощью других методов см. в статье [Развертывание приложения с использованием шаблона Диспетчера ресурсов Azure](resource-group-template-deploy.md).


## Создание с помощью Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по подготовке кластера HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Для создания кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

- создание группы ресурсов Azure;
- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

	$subscriptionId = "<Azure Subscription ID>"
	
	$newResourceGroupName = "<Azure Resource Group Name>" $location = "<Azure Location>" # например, "Восток США 2" $newDefaultStorageAccountName = "<Azure Storage Account Name>" $newClusterName = "<Azure HDInsight Cluster Name>" $clusterSizeInNodes = 1
	
	###########################################
	# Вход в Azure
	########################################### Login-AzureRmAccount Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	###########################################
	# Создание группы ресурсов
	########################################### New-AzureRmResourceGroup -Name $newRresourceGroupName -Location $location
	
	###########################################
	# Подготовка контейнера и учетной записи хранения по умолчанию
	########################################### New-AzureRmStorageAccount -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName -Location $location
	
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName | %{ $\_.Key1 } $defaultStorageContext = New-AzureStorageContext -StorageAccountName $newDefaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey New-AzureStorageContainer -Name $newClusterName -Context $defaultStorageContext #используйте имя кластера в качестве имени контейнера
		
	###########################################
	# Создание кластера
	########################################### $httpCredential =Get-Credential -Message "Введите данные учетной записи HTTP:" New-AzureRmHDInsightCluster `
		-ResourceGroupName $newResourceGroupName ` -ClusterName $newClusterName `
		-Location $location ` -ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop ` -OSType Windows `
		-Version "3.2" ` -HttpCredential $httpCredential



## Создание с помощью пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework. Следуйте инструкциям ниже, чтобы создать консольное приложение Visual Studio и вставить код для создания кластера.

**Создание консольного приложения Visual Studio**

1. Создайте в Visual Studio новое консольное приложение C#.
2. Выполните следующую команду Nuget в окне консоли диспетчера пакетов NuGet.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. В обозревателе решений дважды щелкните файл **Program.cs**, чтобы открыть его, вставьте указанный ниже код и укажите значения для переменных:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		namespace CreateHDInsightCluster
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Azure Subscription ID");
				private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
				private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
				private const string ExistingStorageKey = "<Default Storage Account Key>";
				private const string ExistingBlobContainer = "<Default Blob Container Name>";
				private const string NewClusterName = "<HDInsight Cluster Name>";
				private const int NewClusterNumNodes = 1;
				private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
				private const OSType NewClusterOsType = OSType.Windows;
				private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
				private const string NewClusterVersion = "3.2";
				private const string NewClusterUsername = "admin";
				private const string NewClusterPassword = "<HTTP User password";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
				
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
				private static void CreateCluster()
				{
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
				{
					var authFactory = new AuthenticationFactory();
		
					var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
					if (username != null && password != null)
						account.Id = username;
		
					var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
					var accessToken =
						authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
							.AccessToken;
		
					return new TokenCloudCredentials(accessToken);
				}
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
		
				}
			}
		}

7. Нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. Появится запрос на ввод учетных данных учетной записи Azure. На подготовку кластера HDInsight может уйти несколько минут.

## Создание с помощью локальных служб интеграции SQL Server Integration Services

Кластер HDInsight также можно создать или удалить с помощью служб интеграции SQL Server Integration Services (SSIS). Пакет дополнительных компонентов Azure для служб SSIS предоставляет следующие компоненты, которые работают с кластерами HDInsight.


- [Задача создания кластера Azure HDInsight][ssisclustercreate]
- [Задача удаления кластера Azure HDInsight][ssisclusterdelete]
- [Диспетчер подключений по подпискам Azure][connectionmanager]

Узнать больше о пакете дополнительных компонентов Azure для служб SSIS можно [здесь][ssispack].


##Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](hdinsight-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Использование Sqoop с HDInsight](hdinsight-use-sqoop.md) — узнайте, как копировать данные между HDInsight и базой данных SQL или сервером SQL Server.
* [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md) — узнайте, как работать с HDInsight, используя PowerShell.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation] — узнайте больше о пакете SDK для HDInsight.



##Приложение А — шаблон диспетчера ресурсов Azure

Следующий шаблон диспетчера ресурсов Azure создает кластер Hadoop с зависимой учетной записью хранения Azure.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "username",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx

<!---HONumber=AcomDC_1203_2015-->