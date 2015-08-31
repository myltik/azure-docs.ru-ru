<properties
   pageTitle="Подготовка кластеров Apache Spark в HDInsight | Microsoft Azure"
	description="Подготовка кластеров Spark для Azure HDInsight с помощью портала Azure, Azure PowerShell, командной строки или пакета SDK HDInsight для .NET."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>
<tags
    ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="08/17/2015"
	ms.author="nitinme"/>

# Подготовка кластеров Apache Spark в HDInsight с использованием настраиваемых параметров

В большинстве случаев можно подготовить кластер Spark с помощью метода быстрого создания, как описано в разделе [Начало работы с Apache Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). В некоторых сценариях может потребоваться подготовить настраиваемый кластер. Например, может потребоваться подключить внешнее хранилище метаданных для обеспечения постоянных метаданных Hive после истечения времени существования кластера, или может потребоваться дополнительное хранилище для кластера.

Для этих и других сценариев в данной статье приводятся инструкции по использованию портала Azure, Azure PowerShell или пакета SDK .NET HDInsight для подготовки настроенного кластера Spark в HDInsight.


**Предварительные требования:**

Прежде чем переходить к выполнению действий, приведенных в этой статье, необходимо оформить подписку Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Каковы различные параметры конфигурации?

###Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища больших двоичных объектов Azure и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительную учетную запись хранения Azure, которая будет связана с кластером.

>[AZURE.NOTE] Не используйте один контейнер хранилища больших двоичных объектов для нескольких кластеров. Это не поддерживается.

Дополнительную информацию об использовании дополнительных хранилищ больших двоичных объектов см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-use-blob-storage.md).

###Хранилище мета-данных

Spark позволяет определить схему и таблицы Hive в необработанных данных. Эти схемы и метаданные таблицы можно сохранить в метахранилища. Использование метахранилища помогает сохранять метаданные Hive, чтобы не создавать повторно таблицы Hive при подготовке нового кластера. По умолчанию Hive использует встроенную базу данных для хранения подобной информации. Встроенная база данных не сохраняет метаданные при удалении кластера.

Инструкции по созданию базы данных SQL см. в статье [Создание первой базы данных SQL Azure](sql-database-get-started.md).

### Настройка кластера

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время подготовки. Такие сценарии вызываются с помощью **действия сценария**, которое является параметром конфигурации и может использоваться посредством портала Azure, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительную информацию см. в разделе [Настройка кластера HDInsight с помощью действия сценария][hdinsight-customize-cluster].


###Виртуальная сеть

[Виртуальная сеть Azure](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа «сеть-сеть» или «точка-сеть») при помощи виртуальной частной сети (VPN).

	Конфигурация «сервер-сервер» позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	Конфигурация «точка-сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера. Дополнительную информацию см. в статье [Создание виртуальной сети](virtual-networks-create-vnet.md).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает виртуальные сети на основе территориальных групп.
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

##<a id="portal"></a> Использование портала предварительной версии Azure

Кластеры Spark в HDInsight используют контейнер хранилища больших двоичных объектов Azure как файловую систему по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md). Информацию о создании учетной записи хранения Azure см. в разделе [Как создать учетную запись хранения][azure-create-storageaccount].

**Создание кластера HDInsight с использованием параметра «Настраиваемое создание»**

1. Выполните вход на [портал предварительной версии Azure](https://portal.azure.com).
2. Выберите последовательно **СОЗДАТЬ**, **Анализ данных**, а затем — **HDInsight**.

	![Создание нового кластера на портале предварительной версии Azure](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Создание нового кластера на портале предварительной версии Azure")

3. Введите **Имя кластера**, выберите **Hadoop** в качестве **Типа кластера**, а в раскрывающемся списке **Операционная система кластера** выберите **Windows Server 2012 R2 Datacenter**. Если имя кластера доступно, рядом с ним появится зеленый флажок.

	![Введите имя и тип кластера](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Введите имя и тип кластера")

4. Если у вас есть несколько подписок, выберите запись **Подписка**, чтобы выбрать подписку Azure для кластера.

5. Щелкните **Группа ресурсов**, чтобы просмотреть список существующих групп ресурсов и выбрать ту, в которой будет создан кластер. Или выберите **Создать** и введите имя новой группы ресурсов. Если новое имя группы доступно, рядом с ним появится зеленый флажок.

	> [AZURE.NOTE]Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).

6. Выберите **Учетные данные**, затем введите данные в поля **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**. Если необходимо включить удаленный рабочий стол на узле кластера, выберите **Да** для параметра **Включить удаленный рабочий стол**. Укажите, когда истекает срок доступа кластера к удаленному рабочему столу, и предоставьте имя пользователя и пароль для пользователя удаленного рабочего стола. Нажмите кнопку **Выбрать** внизу страницы, чтобы сохранить учетные данные конфигурации.

	![Укажите учетные данные кластера](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Укажите учетные данные кластера")

7. Щелкните **Источник данных**, чтобы выбрать для кластера существующий источник данных или создать новый.

	![Колонка «Источник данных»](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Укажите конфигурацию источника данных")

	В настоящее время в качестве источника данных для кластера HDInsight можно выбрать учетную запись хранения Azure. Следующие элементы интерфейса помогут вам понять записи в колонке **Источник данных**.

	- **Метод выбора** — выберите значение **Из всех подписок**, чтобы активировать поиск учетных записей хранения во всех своих подписках. Задайте для этого параметра значение **Ключ доступа**, если вы хотите ввести **имя хранилища** и **ключ доступа** существующей учетной записи хранения.

	- **Выбрать учетную запись хранения/создать новую** — щелкните **Выбрать учетную запись хранения**, чтобы найти и выбрать существующую учетную запись хранения, которую нужно связать с кластером. Чтобы создать новую учетную запись хранения, выберите **Создать**. В появившееся поле введите имя учетной записи хранения. Если имя доступно, появится зеленый флажок.

	- **Выбрать контейнер по умолчанию** — позволяет ввести имя контейнера по умолчанию и использовать его для кластера. Вы можете ввести любое имя, однако мы рекомендуем использовать такое же имя, как у кластера, чтобы легко распознавать, какой контейнер используется для конкретного кластера.

	- **Расположение** — географический регион, к которому будет относиться существующая или новая учетная запись хранения.

		> [AZURE.IMPORTANT]Выбранное расположение для источника данных по умолчанию будет также определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

	Нажмите **Выбрать**, чтобы сохранить конфигурацию источника данных.

8. Щелкните **Ценовые категории узла**, чтобы отобразить сведения об узлах, которые будут созданы для этого кластера. Задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.

	![Колонка «Ценовые категории узла»](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Укажите количество узлов кластера")

	Нажмите **Выбрать**, чтобы сохранить конфигурацию цен узла.

9. Щелкните **Необязательная конфигурация**, чтобы выбрать версию кластера, а также настроить другие необязательные параметры, например присоединение **виртуальной сети** и настройку **внешнего метахранилища** для хранения данных Hive и Oozie. Используйте действия сценариев, чтобы настроить кластер для установки пользовательских компонентов, или используйте с кластером дополнительные учетные записи хранения.

	* Откройте раскрывающийся список **Версия HDInsight** и выберите версию, которую вы хотите использовать для кластера. Дополнительную информацию см. в статье [Версии кластеров HDInsight](hdinsight-component-versioning.md).

	* Щелкните **Виртуальная сеть**, чтобы предоставить сведения о конфигурации для настройки кластера как части виртуальной сети. В колонке **Виртуальная сеть** щелкните **Виртуальная сеть** и выберите сеть, которую необходимо использовать. Точно также выберите **Подсеть** для сети и нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию виртуальной сети.

		![Колонка «Виртуальная сеть»](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Укажите сведения о виртуальной сети")

	* Щелкните **Внешние метахранилища**, чтобы указать базу данных SQL, которую вы хотите использовать для сохранения метаданных Hive и Oozie, связанных с кластером.

		![Колонка «Пользовательские метахранилища»](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Укажите внешние метахранилища")

		Для параметра **Использовать существующую базу данных SQL для метаданных Hive** выберите **Да**, укажите базу данных SQL, а затем введите имя пользователя и пароль для базы данных. Повторите эти шаги, если вы хотите **Использовать существующую базу данных SQL для метаданных Oozie**. Щелкайте **Выбрать**, пока не перейдете в колонку **Необязательная конфигурация**.

    >[AZURE.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

	* Щелкните **Действия сценария**, если вы хотите использовать настраиваемый сценарий для настройки кластера во время его создания. Дополнительную информацию о действиях сценариев см. в статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md). В колонке «Действия сценария» введите данные, как показано на снимке экрана.

		![Колонка «Действие сценария»](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Укажите действие сценария")

		Нажмите кнопку **Выбрать**, чтобы сохранить изменения, внесенные в конфигурацию действия сценария.

	* Щелкните **Ключи хранилища Azure**, чтобы указать дополнительные учетные записи хранения, которые нужно связать с кластером. В колонке **Ключи хранилища Azure** нажмите кнопку **Добавить ключ к хранилищу данных**, а затем выберите существующую учетную запись хранения или создайте новую учетную запись.

		![Колонка «Дополнительное хранилище»](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Укажите дополнительные учетные записи хранения")

		Щелкайте **Выбрать**, пока не перейдете в колонку **Кластер HDInsight**.

10. В колонке **Новый кластер HDInsight** обязательно выберите параметр **Закрепить на начальной панели**, а затем нажмите кнопку **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка. Значок указывает, что выполняется подготовка кластера. После завершения подготовки вместо него будет отображаться значок HDInsight.

	| Подготовка выполняется | Подготовка завершена |
	| ------------------ | --------------------- |
	| ![Индикатор подготовки на начальной панели](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Плитка подготовки кластера](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки можно с помощью плитки на начальной панели или записи **Уведомления** в левой части страницы.

11. После завершения подготовки щелкните плитку кластера на начальной панели, чтобы открыть колонку кластера. Колонка кластера содержит важные сведения о кластере, такие как имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.

	![Колонка «Кластер»](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Свойства кластера")

	Следующие элементы интерфейса помогут вам понять, что означают значки в верхней части этой колонки, а также в разделах **Основные компоненты** и **Быстрые ссылки**.

	* **Параметры** и **Все параметры** — отображают колонку **Параметры** кластера, которая содержит подробные сведения о конфигурации кластера.

	* **Панель мониторинга** и **URL-адрес** — пути доступа к панели мониторинга кластера, которая представляет собой веб-портал для запуска заданий в кластере.

	* **Удаленный рабочий стол** — позволяет включать или отключать удаленный рабочий стол на узлах кластера.

	* **Масштабировать кластер** — позволяет изменить количество рабочих узлов для этого кластера.

	* **Удалить** — удаление кластера HDInsight.

	* **Быстрый запуск** (![значок облака и молнии = быстрый запуск](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)) — отображает сведения, необходимые для начала работы с HDInsight.

	* **Пользователи** (![значок пользователей](./media/hdinsight-apache-spark-provision-clusters/users.png)) — здесь можно указать разрешения на доступ к _порталу управления_ этого кластера для других пользователей в своей подписке Azure.

		> [AZURE.IMPORTANT]Этот параметр влияет _только_ на доступ и разрешения для этого кластера на портале предварительной версии Azure. Он не определяет пользователей, которые могут подключаться к кластеру HDInsight или отправлять в него задания.

	* **Теги** (![значок тега](./media/hdinsight-apache-spark-provision-clusters/tags.png)) — теги позволяют задавать пары «ключ — значение» для определения пользовательской таксономии облачных служб. Например, можно создать ключ с именем __project__, а затем использовать общее значение для всех служб, связанных с определенным проектом.

	* **Панель мониторинга кластера** — открывает колонку панели мониторинга кластера, откуда можно запустить саму панель мониторинга кластера или записные книжки Zeppelin и Jupyter.


##<a id="powershell"></a>Использование Azure PowerShell

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по подготовке кластера HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Для подготовки кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

- Создание группы ресурсов Azure
- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Spark


	![HDI.CLI.Provision](./media/hdinsight-apache-spark-provision-clusters/HDI.ps.provision.png)


## Использование пакета SDK HDInsight для .NET на основе ARM
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework. Следуйте инструкциям ниже, чтобы создать консольное приложение Visual Studio и вставить код для создания кластера.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2013.

2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.

3. В окне **Новый проект** введите или выберите следующие значения.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Свойство</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Значение</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Категория</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Шаблон</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консольное приложение</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.

5. В меню **Сервис** выберите **Диспетчер пакетов Nuget**, затем выберите **Управление пакетами Nuget для решений**. В диалоговом окне введите в поле поиска в запрос **HDInsight**. Установите следующие компоненты из результатов поиска:

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Выполните поиск по запросу «Проверка подлинности Azure» и установите **Microsoft.Azure.Common.Authentication** из результатов поиска.

6. В обозревателе решений дважды щелкните файл **Program.cs**, чтобы открыть его, вставьте следующий код и укажите значения для переменных:


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
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
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. Появится запрос на ввод учетных данных учетной записи Azure. На подготовку кластера HDInsight может уйти несколько минут.


##<a id="nextsteps"></a>Дальнейшие действия

* См. статью [Выполнение интерактивного анализа данных с помощью Spark в HDInsight и средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md), где описано использование Apache Spark в HDInsight с помощью средств бизнес-аналитики, таких как Power BI и Tableau.
* См. статью [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning.md), где описано создание приложений машинного обучения с помощью Apache Spark в HDInsight.
* См. статью [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md), где описано создание приложений потоковой передачи с помощью Apache Spark в HDInsight.
* См. статью [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md), где описано использование диспетчера ресурсов для управления ресурсами, выделенными для кластера Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Использование Sqoop вместе с HDInsight"

<!---HONumber=August15_HO8-->