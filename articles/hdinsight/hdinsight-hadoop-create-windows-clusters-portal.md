<properties
   pageTitle="Создание кластеров Hadoop в HDInsight | Microsoft Azure"
   	description="Научитесь создавать кластеры для Azure HDInsight с помощью портала Azure."
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
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Создание кластеров Hadoop под управлением Windows в HDInsight с помощью портала Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-create-windows-cluster-selector.md)]

Узнайте, как создать кластер Hadoop в HDInsight с помощью портала Azure. [Портал Microsoft Azure](azure-portal-overview.md) — это централизованное место, где можно подготавливать ресурсы Azure и управлять ими. Портал Azure является одним из средств, которые можно использовать для создания кластера Hadoop под управлением Linux или Windows в HDInsight. Сведения о других инструментах и функциях создания кластера приведены на вкладке в верхней части этой страницы или в разделе [Способы создания кластера](hdinsight-provision-clusters.md#cluster-creation-methods).

###Предварительные требования:

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Создание кластеров


**Создание кластера HDInsight**

1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Создать**, **Аналитика данных**, а затем — **HDInsight**.

    ![Создание нового кластера на портале Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Создание нового кластера на портале Azure")

3. Введите или выберите следующие значения:

  * **Имя кластера**: введите имя кластера. Если имя кластера доступно, рядом с ним появится зеленый флажок.
  * **Тип кластера**: выберите **Hadoop**. Другие варианты включают **HBase**, **Storm** и **Spark**.
  * **Операционная система кластера**: выберите **Windows**. Чтобы создать кластер на основе Linux, выберите **Linux**.
  * **Версия**: см. статью [Версии HDInsight](hdinsight-component-versioning.md).
  * **Подписка**: выберите подписку Azure, которая будет использоваться для создания этого кластера.
  * **Группа ресурсов**: выберите существующую группу ресурсов или создайте новую. Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).
  * **Учетные данные**: настройте имя пользователя и пароль для пользователя Hadoop (пользователя HTTP). При включении удаленного рабочего стола для кластера необходимо настроить имя пользователя удаленного рабочего стола и пароль, а также указать дату окончания срока действия учетной записи. Щелкните элемент **Выбрать** внизу страницы, чтобы сохранить изменения.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Источник данных**: выберите существующую или создайте новую учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Ценовые категории узлов**. Задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Необязательная конфигурация**: позволяет выбрать версию кластера, а также настроить другие необязательные параметры, например присоединение **виртуальной сети** и конфигурацию **внешнего метахранилища** для хранения данных Hive и Oozie. Используйте действия сценариев, чтобы настроить кластер для установки пользовательских компонентов или использовать дополнительные учетные записи хранения с кластером.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
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
	
    Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки вы можете с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.
	

5. После завершения создания щелкните элемент кластера на начальной панели, чтобы открыть колонку кластера. Колонка кластера содержит важные сведения о кластере: имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.


	![Колонка "Кластер"](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Свойства кластера")


	Ниже приведено описание значков в верхней части этой колонки и в разделе **Основные компоненты**.


	* **Параметры** и **Все параметры** — отображение колонки **Параметры** кластера с подробными сведениями о его конфигурации.
	* **Панель мониторинга**, **Панель мониторинга кластера** и **URL-адрес**: пути доступа к панели мониторинга кластера, которая представляет собой веб-портал для выполнения заданий в кластере.
	* **Удаленный рабочий стол**: позволяет включать или отключать удаленный рабочий стол на узлах кластера.
	* **Масштабировать кластер**. Позволяет изменить количество рабочих узлов для этого кластера.
	* **Удалить**: удаление кластера HDInsight.
	* **Быстрый запуск** (![значок облака и молнии = быстрый запуск](./media/hdinsight-provision-clusters/quickstart.png)). Отображает данные, необходимые для начала работы с HDInsight.
	* **Пользователи** (![значок "пользователи"](./media/hdinsight-provision-clusters/users.png)): здесь вы можете указать разрешения на доступ к _порталу управления_ этого кластера для других пользователей в своей подписке Azure.
	

		> [AZURE.IMPORTANT]Этот параметр влияет _только_ на доступ и разрешения для этого кластера на портале и не определяет пользователей, которые могут подключаться к кластеру HDInsight или отправлять в него задания.
		
	* **Теги** (![значок "теги"](./media/hdinsight-provision-clusters/tags.png)): теги позволяют задавать пары "ключ — значение" для определения пользовательской таксономии облачных служб. Например, можно создать ключ с именем __project__, а затем использовать общее значение для всех служб, связанных с определенным проектом.

##Настройка кластеров

- Обратитесь к статье [Настройка кластеров HDInsight с помощью службы начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Обратитесь к статье [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).

##Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](hdinsight-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0114_2016-->