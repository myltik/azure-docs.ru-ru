<properties
   pageTitle="Настраиваемая подготовка кластеров Hadoop в HDInsight | Microsoft Azure"
   description="Узнайте, как выполнить настраиваемую подготовку кластеров Azure HDInsight с помощью портала Azure, Azure PowerShell, командной строки или пакета SDK для HDInsight .NET."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров

Изучите различные способы настраиваемой подготовки кластеров Hadoop в Azure HDInsight с использованием портала Azure, Azure PowerShell, Azure CLI или пакета SDК для HDInsight .NET. Указания по подготовке кластеров HBase и кластеров Storm см. в разделах [Подготовка кластера HBase в HDInsight](../hdinsight-hbase-get-started.md) и [Приступая к работе с Storm в HDInsight](../hdinsight-storm-getting-started.md). Чтобы понять, почему следует выбрать тот или иной кластер, см. раздел <a href="http://go.microsoft.com/fwlink/?LinkId=510237">В чем разница между Hadoop и HBase?</a>.

## Что такое кластер HDInsight?

Интересовались ли вы когда-нибудь, почему мы упоминаем кластеры каждый раз, когда говорим о Hadoop или данных большого размера? Это объясняется тем, что Hadoop обеспечивает распределенную обработку данных большого размера в различных узлах кластера. Кластер имеет архитектуру ведущий/ведомый с ведущим (также называемым головным узлом или узлом имени) и любым количеством ведомых (также неназываемых узлами данных). Дополнительную информацию см. в разделе [Apache Hadoop][apache-hadoop].

![Кластер HDInsight][img-hdi-cluster]

Кластер HDInsight абстрагирует сведения о реализации Hadoop, в результате чего не возникает проблем взаимодействия в различными узлами кластера. При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Для получения дополнительной информации см. раздел [Введение для Hadoop в HDInsight](hdinsight-hadoop-introduction.md). Данные для обновления находятся в хранилище больших двоичных объектов Azure. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).

В этой статье приводятся указания по различным способам подготовки кластера. Информацию о быстрой подготовке кластера см. в разделе [Приступая к работе с Azure HDInsight](../hdinsight-get-started.md).

**Предварительные требования:**

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- Подписка Azure. Azure — это платформа на основе подписок. Командлеты Azure PowerShell для HDInsight предназначены для выполнения задач с использованием вашей подписки. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

##<a id="configuration"></a>Варианты настройки

###Кластеры в Linux

HDInsight предоставляет возможность настроить кластеры Linux в Azure. Настройте кластер Linux, если вы знакомы с Linux или Unix, выполняете миграцию из существующего решения Hadoop на платформе Linux или хотите с легкостью выполнить интеграцию с компонентами экосистемы Hadoop, созданными для Linux. Дополнительную информацию см. в разделе [Приступая к работе с Hadoop в Linux на платформе HDInsight](../hdinsight-hadoop-linux-get-started.md).

###Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища больших двоичных объектов Azure и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительную учетную запись хранения Azure, которая будет связана с кластером.

>[AZURE.NOTE]Не используйте один контейнер хранилища больших двоичных объектов для нескольких кластеров. Это не поддерживается.

Дополнительную информацию об использовании дополнительных хранилищ больших двоичных объектов см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).

###Хранилище мета-данных

Метахранилище содержит метаданные Hive и Oozie, такие как таблицы, секции, схемы и столбцы Hive. Использование метахранилища помогает сохранять метаданные Hive и Oozie, чтобы не создавать повторно таблицы Hive или задания Oozie при подготовке нового кластера. По умолчанию Hive использует встроенную базу данных для хранения подобной информации. Встроенная база данных не сохраняет метаданные при удалении кластера.

### Настройка кластера

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время подготовки. Такие сценарии вызываются с помощью **действия сценария**, которое является параметром конфигурации и может использоваться посредством портала Azure, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительную информацию см. в разделе [Настройка кластера HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).


###Виртуальная сеть

[Виртуальная сеть Azure](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа «сеть-сеть» или «точка-сеть») при помощи виртуальной частной сети (VPN).

	Конфигурация «сервер-сервер» позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Конфигурация «точка-сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Более подробную информацию можно найти в разделе [Настройка задач виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает виртуальные сети на основе территориальных групп.
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

##<a id="portal"></a> Использование портала Azure

Кластер HDInsight использует контейнер хранилища больших двоичных объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](../hdinsight-use-blob-storage.md). Дополнительную информацию о создании учетной записи хранения Azure см. в разделе [Создание учетной записи хранения](../storage-create-storage-account.md).


> [AZURE.NOTE]В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

**Создание кластера HDInsight с использованием параметра «Настраиваемое создание»**

1. Войдите на [портал Azure][azure-management-portal].
2. В нижней части страницы щелкните **+СОЗДАТЬ**, затем последовательно щелкните **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице **Сведения о кластере** введите или выберите следующие значения:

	![Указание подробной информации о кластере Hadoop HDInsight][image-customprovision-page1]

    <table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя кластера,</td>
		<td><p>Имя кластера. </p>
			<ul>
			<li>DNS-имя должно начинаться и заканчиваться буквой или цифрой и может содержать дефисы.</li>
			<li>Поле должно представлять собой строку от 3 до 63 знаков.</li>
			</ul></td></tr>
	<tr><td>Тип кластера</td>
		<td>Для типа кластера выберите <strong>Hadoop</strong>.</td></tr>
	<tr><td>Операционная система</td>
		<td>Выберите <b>Windows Server 2012 R2 Data Center</b>, чтобы подготовить кластер Windows. Чтобы подготовить кластер Linux, см. раздел <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Подготовка кластеров Hadoop под управлением Linux в HDInsight</a>.</td></tr>
	<tr><td>Версия HDInsight</td>
		<td>Выберите версию. Для Hadoop по умолчанию используется HDInsight версии 3.1, которая использует Hadoop 2.4.</td></tr>
	</table>Введите или выберите значения, указанные в таблице, а затем щелкните стрелку вправо.

4. На странице **Настройка кластера** введите или выберите следующие значения:

	![Указание подробной информации о кластере Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Имя</th><th>Значение</th></tr>
<tr><td>Узлы данных</td><td>Число узлов данных, которые требуется развернуть. В рамках тестирования создайте кластер с одним узлом. <br />Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.</td></tr>
<tr><td>Регион/виртуальная сеть</td><td><p>Выберите тот же регион, что и для учетной записи хранения, созданной в предыдущей процедуре. Для HDInsight требуется, чтобы учетная запись хранения находилась в том же регионе. При последующей настройке можно будет выбрать только учетную запись хранения, которая находится в том же регионе, который указан здесь.</p><p>Доступные регионы: <strong>Восточная Азия</strong>, <strong>Юго-Восточная Азия</strong>, <strong>Северная Европа</strong>, <strong>Западная Европа</strong>, <strong>Восток США</strong>, <strong>Запад США</strong>, <strong>Северо-центральный регион США</strong>, <strong>Южно-центральный регион США</strong>.<br/>Если вы создали виртуальную сеть Azure, то можете задать сеть, которую будет использовать кластер HDInsight.</p><p>Дополнительную информацию о виртуальных сетях Azure см. в разделе <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Задачи конфигурации виртуальной сети</a>.</p></td></tr>
<tr><td>Размер головного узла</td><td><p>Выберите размер виртуальной машины для головного узла.</p></td></tr>
<tr><td>Размер узла данных</td><td><p>Выберите размер виртуальной машины для узлов данных.</p></td></tr>
</table>>[AZURE.NOTE]Цены зависят от размера выбранных виртуальных машин. HDInsight поддерживает для узлов кластера все виртуальные машины стандартного уровня. Дополнительную информацию о том, как размер виртуальной машины влияет на цены, см. в разделе <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight Цены</a>.


5. На странице **Настройка пользователя кластера** введите следующие значения:

    ![Указание подробной информации о пользователе кластера Hadoop HDInsight и метахранилище](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя пользователя HTTP</td>
		<td>Укажите имя пользователя кластера HDInsight.</td></tr>
	<tr><td>Пароль и подтверждение пароля HTTP</td>
		<td>Укажите пароль пользователя кластера HDInsight.</td></tr>
	<tr><td>Включить удаленный рабочий стол для кластера</td>
		<td>Установите этот флажок, чтобы указать имя, пароль и срок действия для пользователя удаленного рабочего стола, который сможет удаленно подключаться к узлам кластера после его подготовки. Удаленный рабочий стол можно также включить позже, после подготовки кластера. Указания см. в разделе <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с помощью RDP</a>.</td></tr>
	<tr><td>Ввести метахранилище Hive или Oozie</td>
		<td>Установите флажок, чтобы указать базу данных SQL, которая будет использоваться в качестве метахранилища Hive или Oozie, в том же центре обработки данных, где расположен кластер. Если флажок установлен, на следующих страницах мастера нужно указать информацию о базе данных SQL Azure. Это может оказаться полезным, если необходимо сохранить метаданные заданий Hive/Oozie даже после удаления кластера.</td></tr>
	</td></tr>
</table>Щелкните стрелку вправо.

6. На странице **Настройка метахранилища Hive или Oozie** введите следующие значения:

    ![Указание подробной информации о пользователе кластера Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Укажите базу данных SQL Azure, которая будет использоваться как метахранилище Hive или Oozie. Вы можете указать одну и ту же базу данных для метахранилища Hive и Oozie. Эта база данных SQL должна находиться в том же центре обработки данных, что и кластер HDInsight. В списке перечислены только базы данных SQL, которые находятся в том же центре обработки данных, который указан на странице <strong>Подробные сведения о кластере</strong>. Укажите также имя пользователя и пароль для подключения к выбранной базе данных SQL Azure.

    >[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

    Щелкните стрелку вправо.


7. На странице **Учетная запись хранения** введите следующие значения:

    ![Указание учетной записи хранения для кластера Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

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
		<td>HDInsight поддерживает несколько учетных записей хранения. Ограничения на дополнительные учетные записи хранения, которые могут использоваться в кластере, отсутствуют. Тем не менее, при создании кластера с использованием портала Azure можно использовать не более семи учетных записей из-за ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу учетной записи хранения к мастеру, где можно указать сведения об учетной записи. Например, на следующем снимке экрана выбрана 1 дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 5.</td></tr>
</table>Щелкните стрелку вправо.

7. Если вы решили настроить дополнительное хранилище для кластера, на странице **Учетная запись хранения** введите данные для учетной записи дополнительного хранилища:

	![Указание информации дополнительном о хранилище для кластера HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    Здесь снова можно выбрать существующее хранилище, создать новое хранилище или использовать хранилище из другой подписки Azure. Процедура задания значений аналогична предыдущему шагу.

    > [AZURE.NOTE]После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни изменить ее на другую.

8. На странице **Действия сценариев** щелкните **Добавить действие сценария**, чтобы указать информацию о пользовательском сценарии, который необходимо выполнить для настройки кластера при его создании. Дополнительную информацию см. в разделе [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md).

	![Настройка действия сценария для настройки кластера HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя</td>
		<td>Укажите имя для действия сценария.</td></tr>
	<tr><td>URI-адрес сценария</td>
		<td>Укажите URI для сценария, который вызывается для настройки кластера.</td></tr>
	<tr><td>Тип узла</td>
		<td>Укажите узлы, на которых выполняется скрипт настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только узлы данных</b>.
	<tr><td>Параметры</td>
		<td>Укажите параметры, если они требуются для сценария.</td></tr>
</table>Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов, щелкните флажок, чтобы начать подготовку кластера.

##<a id="powershell"></a> Использование Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по подготовке кластера HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE]Сценарии в этом разделе можно использовать для настройки кластеров HDInsight под виртуальную сеть Azure, однако саму виртуальную сеть они создать не смогут. Дополнительную информацию о создании виртуальной сети Azure см. в разделе [Задачи конфигурации виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Для подготовки кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

Вы можете выполнять сценарии с помощью консоли Windows PowerShell или интегрированной среды сценариев (ISE) Windows PowerShell.

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight.

**Подключение к учетной записи Azure**

		Add-AzureAccount

Появится запрос на ввод учетных данных учетной записи Azure.

**Создание учетной записи хранения Azure**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа этой учетной записи, можно использовать следующие команды Windows PowerShell для получения нужной информации:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Создание контейнера хранилища больших двоичных объектов Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

> [AZURE.NOTE]Для изменения значений переменных в кластере HDInsight мы советуем использовать только командлеты Azure PowerShell. Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений. Значения настроек, заданные с помощью Azure PowerShell, будут сохранены при установке исправлений.

- Выполните следующие команды в окне консоли Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]Команды $hadoopUserName и $hadoopUserPassword используются для создания учетной записи пользователя Hadoop для кластера. Эта учетная запись будет использоваться для подключения к кластеру и выполнения заданий. При подготовке кластера с использованием параметра «Быстрое создание» на портале Azure имя пользователя Hadoop по умолчанию — admin. Не путайте эту учетную запись с учетной записью пользователя для подключения к удаленному рабочему столу. Учетная запись пользователя для подключения к удаленному рабочему столу должна отличаться от учетной записи пользователя Hadoop. Дополнительную информацию см. в разделе [Управление кластерами Hadoop в HDInsight с использованием портала управления Azure][hdinsight-admin-portal].

	На подготовку кластера может уйти несколько минут.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Подготовка кластера HDInsight с использованием настраиваемых параметров конфигурации**

При подготовке кластера можно использовать другие параметры конфигурации, такие как подключение к нескольким контейнерам хранилища больших двоичных объектов Azure, использование виртуальной сети или использование базы данных SQL Azure для метахранилищ Hive и Oozie. Это позволяет разделить время существования данных и метаданных и время существования кластера.

> [AZURE.NOTE]Для изменения значения переменных в кластере HDInsight мы советуем использовать только командлеты Windows PowerShell. Изменения, внесенные в настройки Hadoop, соединенном с кластером через удаленный рабочий стол, могут быть "затерты" в случае установки исправлений. Значения настроек, заданные с помощью Azure PowerShell, будут сохранены при установке исправлений.

- Выполните следующие команды в окне Windows PowerShell:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

**Отображение списка кластеров HDInsight**

- Выполните следующие команды в окне консоли Azure PowerShell, чтобы указать кластер HDInsight в списке и убедиться, что он был успешно создан:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> С помощью интерфейса командной строки Azure

> [AZURE.NOTE]С 8.29.2014 интерфейс командной строки Azure не может использоваться для связывания кластера с виртуальной сетью Azure.

Другим вариантом подготовки кластера HDInsight является использование интерфейса командной строки Azure. Интерфейс командной строки Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. CLI можно установить из следующих расположений:

- **пакет SDK для Node.js** — <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>;
- **Azure CLI** — <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Общее руководство по использованию интерфейса командной строки Azure CLI см. в статье [Интерфейс командной строки Azure для Mac, Linux и Windows](../xplat-cli.md).

Приведенные ниже инструкции помогут в установке Azure CLI на базе Linux и Windows с дальнейшим использованием командной строки для подготовки кластера.

- [Настройка Azure CLI для Linux](#clilin)
- [Настройка Azure CLI для Windows](#cliwin)
- [Подготовка кластеров HDInsight с помощью Azure CLI](#cliprovision)

#### <a id="clilin"></a>Настройка интерфейса командной строки Azure для Linux

Выполните следующие процедуры, чтобы настроить компьютер с ОС Linux для использования интерфейса командной строки Azure (Azure CLI):

- Установите интерфейс командной строки Azure с помощью диспетчера пакетов Node.js (NPM)
- Подключение к подписке Azure

**Чтобы установить интерфейс командной строки Azure с помощью NPM**

1.	Откройте окно терминала на компьютере с ОС Linux и выполните следующую команду:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Подключение к подписке Azure**

Прежде чем использовать интерфейс командной строки Azure, необходимо настроить связь между рабочей станцией и Azure. Информация о подписке Azure используется интерфейсом командной строки Azure для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который будет использоваться интерфейсом командной строки Azure для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [AZURE.NOTE]Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. В Windows измените свойства папки или используйте шифрование дисков BitLocker.


1.	Откройте окно терминала.
2.	Выполните следующую команду для входа в подписку Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Эта команда запускает веб-страницу, с которой нужно скачать файл параметров публикации. Если веб-страница не открывается, щелкните ссылку в окне терминала, чтобы открыть страницу в браузере и войти на портал.

3.	Скачайте файл параметров публикации на компьютер.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Настройка интерфейса командной строки Azure для Windows

Выполните следующие процедуры, чтобы настроить компьютер под управлением Windows для использования интерфейса командной строки Azure:

- Установите интерфейс командной строки Azure (с помощью NPM или установщика Windows)
- Загрузите и импортируйте параметры публикации учетной записи Azure


Интерфейс командной строки Azure можно установить с помощью NPM или установщика Windows. Корпорация Майкрософт рекомендует установку с использованием только одного из двух параметров.

**Чтобы установить интерфейс командной строки Azure с помощью NPM**

1.	Перейдите к **www.nodejs.org**
2.	Нажмите **УСТАНОВИТЬ** и следуйте указаниям, используя параметры по умолчанию.
3.	Откройте **окно командной строки** (или *окно командной строки Azure*, или *окно командной строки разработчика VS2012*) на своей рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Если появилось сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменной среды PATH следующих путей: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[имя_пользователя]\\AppData\\Roaming\\npm</i> или <i>C:\\Program Files\\nodejs;C:\\Users[имя_пользователя]\\AppData\\Roaming\\npm</i>.

5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Чтобы установить интерфейс командной строки Azure с помощью установщика Windows**

1.	Перейдите в **http://azure.microsoft.com/downloads/**.2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Интерфейс командной строки Azure** и следуйте инструкциям мастера установщика веб-платформы.

**Скачивание и импорт параметров публикации**

Прежде чем использовать интерфейс командной строки Azure, необходимо настроить связь между рабочей станцией и Azure. Информация о подписке Azure используется интерфейсом командной строки Azure для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который будет использоваться интерфейсом командной строки Azure для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [AZURE.NOTE]Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.


1.	Откройте **окно командой строки**.
2.	Выполните следующую команду, чтобы скачать файл параметров публикации:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Эта команда запускает веб-страницу, с которой нужно скачать файл параметров публикации.

3.	При появлении запроса на сохранение файла щелкните **Сохранить** и укажите место, в котором должен быть сохранен файл.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Подготовка кластеров HDInsight с помощью Azure CLI

Подготовка кластера HDInsight с использованием Azure CLI предусматривает следующие процедуры:

- Создание учетной записи хранения Azure
- Подготовка кластера

**Создание учетной записи хранения Azure**

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуется учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

- В окне командной строки выполните следующую команду для создания учетной записи хранения Azure:

		azure storage account create [options] <StorageAccountName>

	При появление запроса расположения выберите местоположение, в котором может быть подготовлен кластер HDInsight. Хранилище должно находиться в одном местоположении с кластером HDInsight. В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

Сведения о создании новой учетной записи хранения Azure с помощью портала Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account.md).

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Дополнительную информацию о получении данных с использованием портала Azure см. в разделе *Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу* статьи [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account.md).

Для кластера HDInsight также требуется контейнер в учетной записи хранения. Если в указанной учетной записи хранения еще нет контейнера, то команда *azure hdinsight cluster create* запрашивает у вас имя контейнера, а также создает его. Однако, для создания контейнера заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

- Введите в окне командной строки следующую команду:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Подготовка кластера HDInsight с использованием файла конфигурации**

Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки Azure предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

- Введите в окне командной строки следующие команды:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Отображение сведений о кластере**

- Используйте следующие команды для отображения сведений о кластере:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Удаление кластера**

- Используйте следующую команду для удаления кластера:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Использование пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework.

Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Более подробные инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).


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

5. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.

6. Чтобы установить пакеты, выполните в консоли следующую команду:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл.

8. Добавьте в начало файла следующие инструкции using:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. В функции Main() скопируйте и вставьте следующий код:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Замените переменные в начале функции Main().

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.



##<a id="nextsteps"></a>Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](../hdinsight-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Использование Sqoop с HDInsight](hdinsight-use-sqoop.md) — узнайте, как копировать данные между HDInsight и базой данных SQL или сервером SQL Server.
* [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md) — узнайте, как работать с HDInsight, используя PowerShell.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation] — узнайте больше о пакете SDK для HDInsight.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli.md

[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Указание подробной информации о кластере Hadoop HDInsight"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Указание подробной информации о пользователях кластера Hadoop HDInsight"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Указание данных учетной записи хранения для кластера Hadoop HDInsight"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Указание данных дополнительной учетной записи хранения для кластера Hadoop HDInsight"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Настройка действия сценария для настройки кластера HDInsight"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Отображение кластеров"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: hdinsight-use-sqoop.md "Использование Sqoop вместе с HDInsight"
 

<!---HONumber=July15_HO3-->