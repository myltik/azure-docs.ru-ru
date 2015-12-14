<properties
	pageTitle="Подготовка кластеров HBase в виртуальной сети | Microsoft Azure"
	description="Приступите к работе с HBase в Azure HDInsight. Узнайте, как создать кластеры HDInsight HBase в виртуальной сети Azure."
	keywords=""
	services="hdinsight,virtual-network"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Подготовка кластеров HBase в виртуальной сети Azure

Узнайте, как создать кластеры Azure HDInsight HBase в [виртуальной сети Azure][1].

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Подготовка кластеров HBase в виртуальной сети Azure](hdinsight-hbase-provision-vnet.md)

Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно. К преимуществам относятся:

- прямое подключение веб-приложения к узлам кластера HBase, который обеспечивает обмен данными с помощью интерфейсов API удаленного вызова процедур (RPC) Java для HBase;
- повышение производительности без необходимости организации пропуска трафика через множество шлюзов и подсистемы балансировки нагрузки;
- возможность обработки конфиденциальной информации более безопасным способом, без необходимости организации общедоступной конечной точки.

##Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Рабочая станция с Azure PowerShell.**. Обратитесь к разделу [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Инструкции см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md). Для выполнения сценариев Azure PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Использование командлета Set-ExecutionPolicy][2].

	Перед выполнением сценариев Azure PowerShell убедитесь, что вы подключены к подписке Azure, с помощью следующего командлета:

		Add-AzureAccount

	При наличии нескольких подписок Azure используется следующий командлет для установки текущей подписки:

		Select-AzureSubscription <AzureSubscriptionName>


##Подготовка кластера HBase в виртуальной сети Azure

Перед подготовкой кластера HBase у вас должна быть виртуальная сеть Azure.

**Создание виртуальной сети с помощью классического портала Azure**

1. Войдите на [классический портал Azure][azure-portal].
2. В нижнем левом углу щелкните **СОЗДАТЬ**, затем последовательно щелкните **СЕТЕВЫЕ СЛУЖБЫ**, **ВИРТУАЛЬНАЯ СЕТЬ** и **БЫСТРОЕ СОЗДАНИЕ**.
3. Введите или выберите следующие значения:

	- **Имя** — имя вашей виртуальной сети.
	- **Адресное пространство** — выбирайте адресное пространство виртуальной сети таким образом, чтобы оно было достаточно большим для обеспечения адресов для всех узлов в кластере. В противном случае подготовка завершится с ошибкой. Для прохождения этого учебника можно выбрать любой из трех вариантов.
	- **Максимальное число ВМ** — выберите максимальное количество виртуальных машин. Это значение определяет количество возможных узлов (ВМ), которые могут быть созданы в адресном пространстве. Для прохождения этого учебника достаточно **4096 [CIDR: /20]**.
	- **Расположение** — расположение должно совпадать с расположением кластера HBase, который вы создадите.
	- **DNS-сервер** — в этом учебнике используется внутренний сервер доменных имен (DNS), предоставляемый Azure, так что вы можете выбрать **Нет**. Также поддерживаются более расширенные сетевые конфигурации с настраиваемыми DNS-серверами. Подробные указания см. в статье [Разрешение имен (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
4. Щелкните **СОЗДАТЬ ВИРТУАЛЬНУЮ СЕТЬ** в правом нижнем углу. В списке появится имя новой виртуальной сети. Подождите, пока в столбце «Состояние» не появится значение **Создано**.
5. В основной области щелкните только что созданную виртуальную сеть.
6. В верхней части страницы щелкните **ПАНЕЛЬ МОНИТОРИНГА**.
7. В разделе **сводки** найдите и запишите идентификатор виртуальной сети. Он вам понадобится при подготовке кластера HBase.
8. В верхней части страницы щелкните **НАСТРОИТЬ**.
9. В нижней части страницы указано имя подсети по умолчанию **Subnet-1**. При желании вы можете переименовать подсеть или добавить новую подсеть для кластера HBase. Запишите имя подсети, оно вам понадобится в процессе подготовки кластера.
10. Проверьте **CIDR (ЧИСЛО АДРЕСОВ)** для подсети, которая будет использоваться в кластере. Количество адресов должен быть больше, чем количество рабочих узлов плюс семь (шлюз: 2, головной узел: 2, Zookeeper: 3). Например, если вам нужен кластер HBase на 10 узлов, количество адресов для подсети должно быть больше 17 (10 + 7). В противном случае подготовка завершится с ошибкой.
11. Нажмите **Сохранить** в нижней части страницы в том случае, если вы изменяли значения для подсети.


**Добавление виртуальной машины DNS-сервера в виртуальную сеть**

DNS-сервер является необязательным, но в некоторых случаях он необходим. Процедура подробно описана в разделе [Настройка DNS между двумя виртуальными сетями Azure][hdinsight-hbase-replication-dns]. По сути, вам необходимо выполнить следующее:

1. добавить виртуальную машину Azure в виртуальную сеть;
2. задать статический IP-адрес для виртуальной машины;
3. добавить роль DNS-сервера для виртуальной машины;
4. назначить DNS-сервер в виртуальную сеть.


**Создание учетной записи хранения Azure и контейнера больших двоичных объектов для кластера**:

> [AZURE.NOTE]Кластеры HDInsight используют хранилища BLOB-объектов Azure для хранения данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с Hadoop в HDInsight](../hdinsight-use-blob-storage.md). Вам понадобится учетная запись хранения и контейнер для хранения BLOB-объектов. Расположение учетной записи хранения должно соответствовать расположению виртуальной сети и кластера.

Как и другие кластеры HDInsight, для кластера HBase требуется учетная запись хранения Azure и контейнер хранилища больших двоичных объектов в качестве файловой системы по умолчанию. Расположение учетной записи хранения должно соответствовать расположению виртуальной сети и кластера. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с Hadoop в HDInsight][hdinsight-storage]. При подготовке кластера HBase можно создать новую учетную запись хранения и контейнер или использовать уже существующие. В этой процедуре показано, как создать учетную запись хранения и контейнер хранилища больших двоичных объектов с помощью классического портала Azure.

1. Войдите на [классический портал Azure][azure-portal].
2. Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРО СОЗДАТЬ**.

3. Введите или выберите следующие значения:

	- **URL-АДРЕС** — имя учетной записи хранения.
	- **РАСПОЛОЖЕНИЕ** — расположение учетной записи хранения. Убедитесь, что оно совпадает с расположением виртуальной сети. Территориальные группы не поддерживаются.
	- **РЕПЛИКАЦИЯ** — в целях тестирования используйте параметр **Локально избыточное**, чтобы снизить затраты.

4. Щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**. Вы увидите новую учетную запись хранения в списке хранилищ.
5. Дождитесь, пока **СОСТОЯНИЕ** новой учетной записи хранения не сменится на **В сети**.
6. Щелкните новую учетную запись хранения в списке, чтобы выбрать ее.
7. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
8. Запишите значения имя учетной записи хранения и первичный ключ доступа (или вторичный ключ доступа). (Любой из ключей будет работать.) Они потребуются позже в данном руководстве.
9. В верхней части страницы щелкните **КОНТЕЙНЕР**.
10. В нижней части страницы щелкните **ДОБАВИТЬ**.
11. Введите имя контейнера. Этот контейнер будет использоваться в качестве контейнера по умолчанию для кластера HBase. По умолчанию имя контейнера по умолчанию соответствует имени кластера. Значением поля **ДОСТУП** оставьте **Частный**.  
12. Установите флажок, чтобы создать контейнер.

**Подготовка кластера HBase с помощью классического портала Azure**

> [AZURE.NOTE]Для получения информации о подготовке кластера HBase с помощью Azure PowerShell см. раздел [Подготовка кластера HBase с помощью Azure PowerShell](#powershell).

1. Войдите на [классический портал Azure][azure-portal].

2. Щелкните **СОЗДАТЬ** в нижнем левом углу, наведите указатель на **СЛУЖБЫ ДАННЫХ**, выберите **HDINSIGHT**, а затем щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

3. Введите имя кластера, выберите HBase в качестве типа кластера, выберите операционную систему Windows Server 2012, выберите версию HDInsight и щелкните кнопку со стрелкой вправо.

	![Ввод сведений для кластера HBase][img-provision-cluster-page1]


	> [AZURE.NOTE]Для кластера HBase единственным доступным вариантом ОС является Windows Server.

4. На странице **Настройка кластера** введите или выберите следующее:

	![Ввод сведений для кластера HBase](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Узлы данных</td><td>Выберите число узлов данных, которые требуется развернуть. В целях тестирования создайте кластер с одним узлом. <br />Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.</td></tr>
		<tr><td>Регион/виртуальная сеть</td><td><p>Выберите регион или виртуальную сеть Azure, если она уже создана. Для этого учебника выберите сеть, созданную ранее, а затем выберите соответствующую подсеть. Имя по умолчанию — <b>Subnet-1</b>.</p></td></tr>
		<tr><td>Размер головного узла</td><td><p>Выберите размер виртуальной машины для головного узла.</p></td></tr>
		<tr><td>Размер узла данных</td><td><p>Выберите размер виртуальной машины для узлов данных.</p></td></tr>
		<tr><td>Размер Zookeeper</td><td><p>Выберите размер виртуальной машины для узла Zookeeper.</p></td></tr>
	</table>

	>[AZURE.NOTE]Цены зависят от размера выбранных виртуальных машин. HDInsight поддерживает для узлов кластера все виртуальные машины стандартного уровня. Дополнительную информацию о том, как размер виртуальной машины влияет на цены, см. в разделе <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight Цены</a>.

	Щелкните стрелку вправо.

5. Введите имя и пароль пользователя Hadoop для этого кластера и щелкните кнопку со стрелкой вправо.

	![Указание учетной записи хранения для кластера Hadoop HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя пользователя HTTP</td>
			<td>Укажите имя пользователя кластера HDInsight.</td></tr>
		<tr><td>Пароль и подтверждение пароля HTTP</td>
			<td>Укажите пароль пользователя кластера HDInsight.</td></tr>
		<tr><td>Включить удаленный рабочий стол для кластера</td>
			<td>Установите этот флажок, чтобы указать имя, пароль и срок действия для пользователя удаленного рабочего стола, который сможет удаленно подключаться к узлам кластера после его подготовки. Удаленный рабочий стол можно также включить позже, после подготовки кластера. Указания см. в разделе <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с помощью RDP</a>.</td></tr>
	</table>

6. На странице **Учетная запись хранения** введите следующие значения:

    ![Указание учетной записи хранения для кластера Hadoop HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Учетная запись хранения</td>
			<td>Укажите учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера HDInsight. Можно выбрать один из трех вариантов:
			<ul>
				<li><strong>Использовать существующее хранилище</strong>.</li>
				<li><strong>Создать новое хранилище</strong>.</li>
				<li><strong>Использовать хранилище другой подписки</strong>.</li>
			</ul>
			</td></tr>
		<tr><td>Имя учетной записи</td>
			<td><ul>
				<li>Если используется существующее хранилище, выберите существующую учетную запись хранения для параметра <strong>Имя учетной записи</strong>. В раскрывающемся списке содержатся только те учетные записи хранения, которые расположены в том же центре обработки данных, где выполняется подготовка кластера.</li>
				<li>При выборе параметра <strong>Создать новое хранилище</strong> или <strong>Использовать хранилище из другой подписки</strong> необходимо указать имя учетной записи хранения.</li>
			</ul></td></tr>
		<tr><td>Ключ учетной записи</td>
			<td>Если используется параметр <strong>Использовать учетную запись из другой подписки</strong>, необходимо указать ключ этой учетной записи хранения.</td></tr>
		<tr><td>Контейнер по умолчанию</td>
			<td><p>Задайте контейнер по умолчанию в учетной записи хранения, которая используется в качестве файловой системы по умолчанию для кластера HDInsight. Если вы выберете <strong>Использовать существующее хранилище</strong> в поле <strong>Учетная запись хранения</strong> и в этой учетной записи нет контейнеров, то контейнер создается по умолчанию с тем же именем, что и имя кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д. Однако, если в существующей учетной записи хранения есть контейнер с именем, отличающимся от имени кластера, можно также использовать и этот контейнер.</p>
	        <p>Если создается новое хранилище или используется хранилище из другой подписки Azure, необходимо указать имя контейнера по умолчанию.</p>
	    </td></tr>
		<tr><td>Дополнительные учетные записи хранения</td>
			<td>При необходимости укажите дополнительные учетные записи хранения для кластера. HDInsight поддерживает несколько учетных записей хранения. Ограничения на дополнительные учетные записи хранения, которые могут использоваться в кластере, отсутствуют. Тем не менее, при создании кластера с использованием портала Azure можно использовать не более семи учетных записей из-за ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу <strong>учетной записи хранения</strong> к мастеру, где можно указать сведения об учетной записи. Например, на снимке экрана выше дополнительная учетная запись хранения не выбрана, поэтому в мастер не добавляется дополнительная страница.</td></tr>
	</table>

	Щелкните стрелку вправо.

7. На странице **Действия сценариев** установите флажок в правом нижнем углу. Не нажимайте кнопку **Добавить действие сценария**, так как в этом учебнике не требуется установка настраиваемого кластера.

	![Настройка действия сценария для настройки кластера HDInsight HBase][img-provision-cluster-page5]

	> [AZURE.NOTE]Эта страница используется также для настройки кластера во время установки. Дополнительную информацию см. в разделе [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).

Чтобы начать работу с новым HBase кластером, можно использовать процедуры, которые представлены в разделе [Приступая к работе с HBase с Hadoop в HDInsight](../hdinsight-hbase-get-started.md).

##Подключение к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API удаленного вызова процедур (RPC) Java для HBase

1.	Подготовьте виртуальную машину IaaS в той же виртуальной сети Azure и той же подсети. Поэтому виртуальная машина и кластер HBase будут использовать один и тот же внутренний сервер DNS для разрешения имен узлов. Чтобы сделать это, необходимо выбрать параметр **Из коллекции** и выбрать виртуальную сеть вместо центра обработки данных. Инструкции см. в разделе [Создание виртуальной машины под управлением Windows Server](../virtual-machines-windows-tutorial.md). Достаточно стандартного образа Windows Server 2012 с виртуальной машиной небольшого размера.

2.	При использовании Java-приложения для удаленного подключения к HBase необходимо использовать полное доменное имя (FQDN). Чтобы определить это, вам необходимо получить DNS-суффикс кластера HBase. Для этого воспользуйтесь Curl, чтобы создать запрос к Ambari, или удаленным рабочим столом, чтобы подключиться к кластеру.

	* **Curl** — используйте следующую команду:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		В возвращенных данных JSON найдите запись «host\_name». В ней указаны полные доменные имена узлов в кластере. Например:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		Часть доменного имени, начинающаяся с имени кластера, является DNS-суффиксом. Например, mycluster.b1.cloudapp.net.

	* **Azure PowerShell** — используйте следующий сценарий Azure PowerShell для регистрации функции **Get-ClusterDetail**, которая может быть использована для возвращения DNS-суффикса:

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		После запуска сценария Azure PowerShell используйте следующую команду, чтобы вернуть DNS-суффикс с помощью функции **Get-ClusterDetail**. При использовании этой команды укажите имя кластера HDInsight HBase, имя и пароль администратора.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		В результате будет возвращен DNS-суффикс. Например, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE]Для подключения к кластеру HBase (вы будете подключены к головному узлу) и выполнения команды **ipconfig** в командной строке для получения DNS-суффикса также можно использовать удаленный рабочий стол. Инструкции по включению протокола удаленного рабочего стола и подключению к кластеру с его помощью см. в статье [Управление кластерами Hadoop в HDInsight с помощью классического портала Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

Чтобы проверить обмен данными между виртуальной машиной и кластером HBase, используйте следующую команду `ping headnode0.<dns suffix>` на виртуальной машине. Например, ping headnode0.mycluster.b1.cloudapp.net.

Чтобы использовать эту информацию в Java-приложении, для создания приложения можно придерживаться шагов, представленных в разделе [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md). Чтобы приложение подключилось к удаленному серверу HBase, измените файл **hbase-site.xml** в этом примере, чтобы использовать полное доменное имя для ZooKeeper. Например:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Для получения дополнительной информации о разрешении имен в виртуальных сетях Azure, а также об использовании своего​ собственного DNS-сервера см. раздел [Разрешение имен (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Подготовка кластера HBase с помощью Azure PowerShell

**Подготовка кластера HBase с помощью Azure PowerShell**

1. Откройте интегрированную среду сценариев Azure PowerShell.
2. Скопируйте следующую команду и вставьте ее в окно запроса:

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Щелкните **Запустить сценарий** или нажмите клавишу **F5**.
4. Вы можете проверить кластер через классический портал Azure либо выполнить в нижней панели следующий командлет Azure PowerShell:

	Get-AzureHDInsightCluster

##Дальнейшие действия

В этом учебнике вы узнали, как подготовить к работе кластер HBase. Дополнительные сведения см. на следующих ресурсах:

- [Приступая к работе с HDInsight](../hdinsight-get-started.md)
- [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md)
- [Подготовка кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md)
- [Приступая к работе с HBase с Hadoop в HDInsight](../hdinsight-hbase-get-started.md)
- [Анализ мнений пользователей Twitter с использованием HBase в HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Обзор виртуальной сети][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://management.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Подготовка сведений для нового кластера HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Использование действия сценария для настройки кластера HBase"

<!---HONumber=AcomDC_1203_2015-->