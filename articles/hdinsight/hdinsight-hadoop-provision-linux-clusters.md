<properties
   	pageTitle="Подготовка кластеров Hadoop, HBase и Storm на платформе Linux в HDInsight | Microsoft Azure"
   	description="Узнайте, как подготовить кластеры Hadoop для HDInsight на платформе Linux с помощью портала управления, командной строки и пакета SDK для .NET."
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
   	ms.date="09/21/2015"
   	ms.author="nitinme"/>


#«Подготовка кластеров Hadoop на основе Linux в HDInsight с помощью настраиваемых параметров».

В этой статье вы ознакомитесь с различными способами настраиваемой подготовки кластера Hadoop на платформе Linux в Azure HDInsight с использованием портала Azure, Azure PowerShell, интерфейса командной строки Azure или пакета SDК для HDInsight .NET.

## Что такое кластер HDInsight?

Интересовались ли вы когда-нибудь, почему мы упоминаем кластеры каждый раз, когда говорим о Hadoop или данных большого размера? Это объясняется тем, что Hadoop обеспечивает распределенную обработку данных большого размера в различных узлах кластера. Кластер имеет архитектуру «главный/рабочий» с главным узлом (также называемым головным узлом или узлом имени) и любым количеством рабочих узлов (также называемых узлами данных). Дополнительную информацию см. в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Кластер HDInsight][img-hdi-cluster]


Кластер HDInsight абстрагирует сведения о реализации Hadoop, в результате чего не возникает проблем взаимодействия в различными узлами кластера. При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Для получения дополнительной информации см. раздел [Введение для Hadoop в HDInsight](hdinsight-hadoop-introduction.md). Данные для обновления находятся в хранилище больших двоичных объектов Azure. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).


В этой статье приводятся указания по различным способам подготовки кластера. Информацию о быстрой подготовке кластера см. в статье [Приступая к работе с Azure HDInsight в Linux](../hdinsight-hadoop-linux-get-started.md).

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Ключи Secure Shell (SSH)**. Для удаленного использования кластера Linux с помощью ключа SSH вместо пароля. Использование ключа — рекомендуемый метод, поскольку он более безопасен. Указания по созданию ключей SSH см. в следующих статьях:
	-  на компьютере под управлением Linux — [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md);
	-  на компьютере под управлением Windows — [Использование SSH с кластерами HDInsight (Hadoop) под управлением Linux в Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Варианты настройки

### Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища больших двоичных объектов Azure и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительные BLOB-объекты, которые будут связаны с кластером.


Дополнительную информацию об использовании вторичных хранилищ больших двоичных объектов см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).


### Хранилище мета-данных

В метахранилище содержится информация о таблицах Hive, разделах, схемах, столбцах и т. д. Эта информация используется Hive для определения места расположения данных в распределенной файловой системе Hadoop (HDFS) или в хранилище больших двоичных объектов Azure для HDInsight. По умолчанию Hive использует встроенную базу данных для хранения подобной информации.

Во время подготовки кластера HDInsight вы можете задать базу данных SQL, которая будет выполнять роль метахранилища для Hive. Это позволит сохранить метаданные при удалении кластера, так как вся информация будет храниться во внешней базе данных SQL. Инструкции по созданию базы данных SQL см. в статье [Создание первой базы данных SQL Azure](sql-database-get-started.md).

### Настройка кластеров с помощью действия сценария

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время подготовки. Такие сценарии вызываются с помощью **действия сценария**. Дополнительную информацию см. в статье [Настройка кластера HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT]Добавить дополнительные компоненты после подготовки кластера нельзя, поскольку после повторного создания образа узла кластера такие компоненты будут недоступны. В процессе повторного создания образа компоненты, установленные с помощью действий сценариев, устанавливаются заново.

### Использование виртуальных сетей Azure

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа "сеть-сеть" или "точка-сеть") при помощи виртуальной частной сети (VPN).

	Конфигурация "сервер-сервер" позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	Конфигурация "точка-сеть" позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера. Дополнительную информацию см. в статье [Создание виртуальной сети](virtual-networks-create-vnet.md).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает совместные групповые виртуальные сети. Чтобы проверить, является ли существующая виртуальная сеть Azure географически привязанной, используйте командлет Azure PowerShell Get-AzureVNetConfig. Если у вашей виртуальной сети нет географической привязки, вы можете:
>
> - Экспортировать конфигурацию существующей виртуальной сети, а затем создать новую виртуальную сеть. У всех новых виртуальных сетей по умолчанию есть географическая привязка.
> - Выполнить миграцию в географически привязанную виртуальную сеть. Дополнительные сведения см. в статье [Перенос существующих служб на региональный уровень](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.
>
> На данный момент (25 августа 2015 г.) в виртуальной сети Azure можно указать только один кластер под управлением Linux.
>
> Вам не удастся использовать виртуальную сеть Azure v1 (классическую) с HDInsight под управлением Linux. Необходимо использовать виртуальную сеть v2 (диспетчер ресурсов Azure), чтобы ее можно было выбрать при создании кластера HDInsight на портале предварительной версии Azure или использовать при создании кластера в Azure CLI или Azure PowerShell.
>
> Если у вас есть ресурсы в сети v1 и вам необходимо, чтобы эти ресурсы могли получать непосредственный доступ к HDInsight через виртуальную сеть, то сведения о том, как подключить виртуальную сеть v1 к виртуальной сети v2, можно найти в статье [Подключение классических виртуальных сетей к новым виртуальным сетям](../virtual-network/virtual-networks-arm-asm-s2s.md). После установления подключения можно создать кластер HDInsight в виртуальной сети v2.

## <a id="options"></a>Варианты подготовки кластера HDInsight на платформе Linux

Кластер HDInsight Hadoop на платформе Linux можно подготовить с компьютера под управлением Linux или Windows. В следующей таблице содержится информация о подготовке параметров, доступных в разных операционных системах, а также ссылки на инструкции для каждого из них.

Подготовка кластеров на платформе Linux на компьютере под управлением этой ОС | Использование портала Azure | Использование Azure CLI | Использование Azure PowerShell | Использование пакета SDK для .NET
-----------------| --------| ------------------------| -------------------| ---------- | ---------
Linux| Щелкните [здесь](#portal) | Щелкните [здесь](#cli) | Не применяется | Не применяется
Mac OS X | Щелкните [здесь](#portal) | Щелкните [здесь](#cli) | Не применяется | Не применяется
Windows | Щелкните [здесь](#portal) | Щелкните [здесь](#cli) | Щелкните [здесь](#powershell) | Щелкните [здесь](#sdk)

### <a id="portal"></a> Использование портала Azure

Кластеры HDInsight используют контейнер хранилища больших двоичных объектов Azure как файловую систему по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure в том же центре обработки данных. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md). Информацию о создании учетной записи хранения Azure см. в разделе [Как создать учетную запись хранения](../storage-create-storage-account.md).


> [AZURE.NOTE]В настоящее время кластеры HDInsight на платформе Linux можно размещать только в регионах **Юго-Восточной Азии**, **Северной Европы**, **Восточной части США** и **Южно-центральной части США**.

**Чтобы создать кластер HDInsight, сделайте следующее.**

1. Выполните вход на [портал предварительной версии Azure](https://portal.azure.com).
2. Последовательно выберите **СОЗДАТЬ**, **Анализ данных**, а затем — **HDInsight**.

    ![Создание нового кластера на портале предварительной версии Azure](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Создание нового кластера на портале предварительной версии Azure")

3. Введите **Имя кластера**, выберите **Hadoop** в качестве **Типа кластера** и в раскрывающемся списке **Операционная система кластера** выберите **Ubuntu**. Если имя кластера доступно, рядом с ним появится зеленый флажок.


	> [AZURE.NOTE]Для подготовки кластера HBase или Storm выберите соответствующее значение **типа кластера** из раскрывающегося списка.


	![Введите имя и тип кластера](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "Введите имя и тип кластера")

4. Если у вас есть несколько подписок, выберите запись **Подписка**, чтобы выбрать подписку Azure для кластера.

5. Щелкните **Группа ресурсов**, чтобы просмотреть список существующих групп ресурсов и выбрать ту, в которой будет создан кластер. Или выберите **Создать** и введите имя новой группы ресурсов. Если новое имя группы доступно, рядом с ним появится зеленый флажок.

	> [AZURE.NOTE]Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).

6. Щелкните **Учетные данные** и введите пароль для пользователя с правами администратора. Необходимо также ввести **Имя пользователя SSH** и **ПАРОЛЬ** или **ОТКРЫТЫЙ КЛЮЧ**, которые будут использоваться для проверки подлинности пользователя SSH. Мы рекомендуем использовать открытый ключ. Нажмите кнопку **Выбрать** внизу страницы, чтобы сохранить учетные данные конфигурации.

	![Укажите учетные данные кластера](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "Укажите учетные данные кластера")

	Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Щелкните **Источник данных**, чтобы выбрать для кластера существующий источник данных или создать новый.

	![Колонка "Источник данных"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "Укажите конфигурацию источника данных")

	В настоящее время в качестве источника данных для кластера HDInsight можно выбрать учетную запись хранения Azure. Далее приводится описание элементов интерфейса, которое поможет вам лучше понять записи в колонке **Источник данных**.

	- **Метод выбора** — выберите значение **Из всех подписок**, чтобы активировать поиск учетных записей хранения во всех своих подписках. Задайте для этого параметра значение **Ключ доступа**, если вы хотите ввести **имя хранилища** и **ключ доступа** существующей учетной записи хранения.

	- **Выбрать учетную запись хранения/создать новую** — щелкните **Выбрать учетную запись хранения**, чтобы найти и выбрать существующую учетную запись хранения, которую нужно связать с кластером. Чтобы создать новую учетную запись хранения, выберите **Создать**. В появившееся поле введите имя учетной записи хранения. Если имя доступно, появится зеленый флажок.

	- **Выбрать контейнер по умолчанию**. Эта команда позволяет ввести имя контейнера по умолчанию и использовать его для кластера. Вы можете ввести любое имя, однако мы рекомендуем использовать такое же имя, как у кластера, чтобы легко распознавать, какой контейнер используется для конкретного кластера.

	- **Расположение** — географический регион, к которому будет относиться существующая или новая учетная запись хранения.

		> [AZURE.IMPORTANT]Выбранное расположение для источника данных по умолчанию будет также определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

	Нажмите **Выбрать**, чтобы сохранить конфигурацию источника данных.

8. Щелкните **Ценовые категории узла**, чтобы отобразить сведения об узлах, которые будут созданы для этого кластера. Задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.

	![Колонка "Ценовые категории узла"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "Укажите количество узлов кластера")

	Нажмите **Выбрать**, чтобы сохранить конфигурацию цен узла.

9. Щелкните **Необязательная конфигурация**, чтобы выбрать версию кластера, а также настроить другие необязательные параметры, например присоединение **виртуальной сети** и настройку **внешнего метахранилища** для хранения данных Hive и Oozie. Используйте действия сценариев, чтобы настроить кластер для установки пользовательских компонентов, или используйте с кластером дополнительные учетные записи хранения.

	* Откройте раскрывающийся список **Версия HDInsight** и выберите версию, которую вы хотите использовать для кластера. Дополнительную информацию см. в статье [Версии кластеров HDInsight](hdinsight-component-versioning.md).


	* **Виртуальная сеть** — выберите виртуальную сеть Azure и подсеть для размещения кластера в виртуальной сети.

		![Колонка "Виртуальная сеть"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "Укажите сведения о виртуальной сети")

    	>[AZURE.NOTE]Кластер HDInsight на основе Windows можно разместить только в классической виртуальной сети.


	* Щелкните **Внешние метахранилища**, чтобы указать Базу данных SQL, которую вы хотите использовать для сохранения метаданных Hive и Oozie, связанных с кластером.

		![Колонка "Пользовательские метахранилища"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "Укажите внешние метахранилища")

		Для параметра **Использовать существующую Базу данных SQL для метаданных Hive** выберите **Да**, укажите базу данных SQL, а затем введите имя пользователя и пароль для базы данных. Повторите эти шаги, если вы хотите **Использовать существующую базу данных SQL для метаданных Oozie**. Щелкайте **Выбрать**, пока не перейдете в колонку **Необязательная конфигурация**.

		>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.


	* Щелкните **Действия сценария**, если хотите использовать настраиваемый сценарий для настройки кластера во время его создания. Дополнительную информацию о действиях сценариев см. в статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md). В колонке "Действия сценария" введите данные, как показано на снимке экрана.

		![Колонка "Действие сценария"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "Укажите действие сценария")


	* Щелкните **Ключи хранилища Azure**, чтобы указать дополнительные учетные записи хранения, которые нужно связать с кластером. В колонке **Ключи хранилища Azure** нажмите кнопку **Добавить ключ к хранилищу данных**, а затем выберите существующую учетную запись хранения или создайте новую учетную запись.

		![Колонка "Дополнительное хранилище"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "Укажите дополнительные учетные записи хранения")

		Щелкайте **Выбрать**, пока не перейдете в колонку **Кластер HDInsight**.

10. В колонке **Новый кластер HDInsight** обязательно выберите параметр **Закрепить на начальной панели**, а затем нажмите кнопку **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка. Значок указывает, что выполняется подготовка кластера. После завершения подготовки вместо него будет отображаться значок HDInsight.

	| Подготовка выполняется | Подготовка завершена |
	| ------------------ | --------------------- |
	| ![Индикатор подготовки на начальной панели](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![Плитка подготовки кластера](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]Обычно создание кластера занимает около 15 минут. Отслеживать процесс подготовки можно с помощью элемента на начальной панели или записи **Уведомления** в левой части страницы.

11. После завершения подготовки щелкните плитку кластера на начальной панели, чтобы открыть колонку кластера. Колонка кластера содержит важные сведения о кластере, такие как имя, группа ресурсов, к которой он принадлежит, расположение, операционная система, URL-адрес панели мониторинга кластера и т. д.

	![Колонка "Кластер"](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "Свойства кластера")

	Ниже приведено описание значков в верхней части этой колонки и в разделе **Основные компоненты**.

	* **Параметры** и **Все параметры**. Отображают колонку **Параметры** кластера, которая содержит подробные сведения о конфигурации кластера.

	* **Панель мониторинга**, **Панель мониторинга кластера** и **URL-адрес**. Пути доступа к панели мониторинга кластера, которая представляет собой веб-портал для выполнения заданий в кластере.

	* **Secure Shell** — сведения, необходимые для доступа к кластеру с помощью протокола SSH.

	* **Удалить**. Удаление кластера HDInsight.

	* **Быстрый запуск** (![значок облака и молнии = быстрый запуск](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)). Отображает сведения, необходимые для начала работы с HDInsight.

	* **Пользователи** (![значок "пользователи"](./media/hdinsight-hadoop-provision-linux-clusters/users.png)). Здесь можно указать разрешения на доступ к _порталу управления_ этого кластера для других пользователей в своей подписке Azure.

		> [AZURE.IMPORTANT]Этот параметр влияет _только_ на доступ и разрешения для этого кластера на портале предварительной версии Azure. Он не определяет пользователей, которые могут подключаться к кластеру HDInsight или отправлять в него задания.

	* **Теги** (![значок "теги"](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)). Теги позволяют задавать пары «ключ — значение» для определения пользовательской таксономии облачных служб. Например, можно создать ключ с именем __project__, а затем использовать общее значение для всех служб, связанных с определенным проектом.

### <a id="cli"></a> Использование Azure CLI

Azure CLI представляет собой кроссплатформенную службу командной строки, с помощью которой можно управлять службами Azure. Она используется вместе с шаблонами управления ресурсами Azure для подготовки кластера HDInsight, а также связанных учетных записей хранения и других служб.

Шаблоны управления ресурсами Azure представляют собой документы JSON, описывающие __группу ресурсов__ и все входящие в нее ресурсы (например, HDInsight). Подход на основе шаблонов позволяет определить все необходимые для HDInsight ресурсы в одном шаблоне и управлять всеми изменениями в группе с помощью __развертываний__, применяющих изменения ко всей группе.

Ниже описана процедура создания нового кластера HDInsight с использованием шаблона и Azure CLI:

1. Выполните инструкции в документе [Установка и настройка Azure CLI](../xplat-cli-install.md), если интерфейс командной строки еще не установлен.

2. Выполните в командной строке, терминале или оболочке следующую команду, чтобы проверить подлинность подписки Azure:

        azure login

    Система предложит вам указать имя пользователя и пароль. Если подписок Azure несколько, укажите, какую подписку должны использовать команды Azure CLI, с помощью метода `azure account set <subscriptionname>`.

3. Переключитесь в режим диспетчера ресурсов Azure с помощью следующей команды:

        azure config mode arm

4. Создайте шаблон для кластера HDInsight. Вот некоторые основные примеры шаблонов:

    * [Кластер под управлением Linux с использованием открытого ключа SSH](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpublickey)
    * [Кластер под управлением Linux с использованием пароля для учетной записи SSH](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpassword)

    Оба эти шаблона также создают учетную запись хранения Azure, которую HDInsight использует по умолчанию.

    Вам потребуются файлы __azuredeploy.json__ и __azuredeploy.parameters.json__.

5. Откройте файл __azuredeploy.parameters.json__ в редакторе и укажите значения для элементов в разделе `parameters`:

    * __location__: центр обработки данных, в которых будут созданы ресурсы. Список допустимых расположений см. в разделе `location` файла __azuredeploy.json__.
    * __clusterName__: имя кластера HDInsight. Это имя должно быть уникальным, иначе произойдет сбой развертывания.
    * __clusterStorageAccountName__: имя учетной записи хранения Azure, которая будет создана для кластера HDInsight. Это имя должно быть уникальным, иначе произойдет сбой развертывания.
    * __clusterLoginPassword__: пароль для пользователя-администратора кластера. Пароль должен быть надежным, поскольку используется для доступа к веб-сайтам и службам REST в кластере.
    * __sshUserName__: имя первого пользователя SSH, которого нужно создать для этого кластера. SSH будет использоваться для удаленного доступа к кластеру с использованием этой учетной записи. Имя должно быть уникальным; использовать имя учетной записи, которая уже используется в кластере, нельзя. В число недопустимых имен входят root, storm и hbase.
    * __sshPublicKey__: при использовании шаблона, который требует открытого SSH-ключа, необходимо добавить открытый ключ в эту строку. Дополнительные сведения о создании открытых ключей и работе с ними см. в следующих статьях:

        * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: при использовании шаблона, который требует пароля SSH, необходимо добавить пароль в эту строку.

    Закончив настройку, сохраните и закройте файл.

5. Для создания пустой группы ресурсов выполните указанную ниже команду. Замените __RESOURCEGROUPNAME__ на имя, которое вы хотите использовать для этой группы. Замените __LOCATION__ на центр обработки данных, в котором должна быть создана эта группа.

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]Если имя расположения содержит пробелы, заключите его в двойные кавычки. Например, "Юг центральных США".

6. Чтобы создать начальное развертывание для этой группы ресурсов, выполните указанную ниже команду. Замените __PATHTOTEMPLATE__ на путь к файлу шаблона __azuredeploy.json__. Замените __PATHTOPARAMETERSFILE__ на путь к файлу __azuredeploy.parameters.json__. Замените __RESOURCEGROUPNAME__ на имя группы, созданной на предыдущем этапе.

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Когда вы примете развертывание, должно отобразиться сообщение вида `group deployment create command ok`.

7. Развертывание может занять определенное время, примерно 15 минут. Чтобы просмотреть информацию о развертывании, выполните указанную ниже команду. Замените __RESOURCEGROUPNAME__ на имя группы ресурсов, созданной на предыдущем этапе.

        azure group log show -l RESOURCEGROUPNAME

    Если во время развертывания произойдет сбой, получить дополнительные сведения об этом сбое можно будет с помощью следующей команды:

        azure group log show -l -v RESOURCEGROUPNAME

### <a id="powershell"></a>Использование Azure PowerShell

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по подготовке кластера HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Для подготовки кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

- создание группы ресурсов Azure;
- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

Для подготовки кластеров на платформе Linux нужно задать два наиболее важных параметра, в которых необходимо указать тип ОС и подробную информацию о пользователе SSH:

- Обязательно укажите для параметра **-OSType** значение **Linux**.
- Для использования SSH в удаленных сеансах кластеров можно указать пароль пользователя SSH или открытый ключ SSH. Если указать и пароль пользователя SSH, и открытый ключ SSH, ключ будет игнорироваться. Если вам нужно использовать ключ SSH для удаленных сеансов, оставьте поле пустым при запросе пароля SSH. Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


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
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]Значения, указываемые для параметра **$clusterCredentials**, используются для создания учетной записи Hadoop в кластере. Эта учетная запись будет использоваться для подключения к кластеру. Значения, указываемые для параметра **$sshCredentials**, используются для создания пользователя SSH в кластере. Используйте эту учетную запись для запуска удаленного сеанса SSH в кластере и выполнения заданий. При подготовке кластера с использованием параметра «Быстрое создание» на портале Azure имя пользователя Hadoop по умолчанию — admin, а имя пользователя SSH по умолчанию — hdiuser.

На подготовку кластера может уйти несколько минут.

![HDI.CLI.Provision][image-hdi-ps-provision]



###<a id="sdk"></a> Использование пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework. Следуйте инструкциям ниже, чтобы создать консольное приложение Visual Studio и вставить код для создания кластера.

**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2013 или 2015.
2. Создайте новый проект Visual Studio со следующими параметрами:

	|Свойство|Значение|
	|--------|-----|
	|Шаблон|Templates/Visual C#/Windows/Console Application|
	|Имя|CreateHDICluster|

5. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.
6. Чтобы установить пакеты, выполните в консоли следующую команду:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Эти команды добавляют библиотеки .NET и ссылки на них в текущий проект Visual Studio.

6. В обозревателе решений дважды щелкните файл **Program.cs**, чтобы открыть его, вставьте указанный ниже код и укажите значения для переменных:

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const string NewClusterVersion = "3.2";
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;

		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

		        private const string NewClusterSshUserName = "sshuser";
		        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
					Comment: ""rsa-key-20150731""
					AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
					gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
					yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
					WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
					pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
					zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
					---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Замените значения членов класса.

7. Нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. Появится запрос на ввод учетных данных учетной записи Azure. На подготовку кластера HDInsight может уйти несколько минут.



##<a id="nextsteps"></a>Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight Hadoop в Linux. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Работа с HDInsight в Linux](hdinsight-hadoop-linux-information.md) — узнайте об особенностях работы с кластером HDInsight в Linux.
- [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md) — узнайте, как выполнять мониторинг кластера Hadoop HDInsight под управлением Linux, используя веб-интерфейс API Ambari или REST API Ambari.
- [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md) — узнайте о различных способах выполнения заданий MapReduce в кластере.
- [Использование Hive с HDInsight](hdinsight-use-hive.md) — узнайте о различных способах выполнения запроса Hive в кластере.
- [Использование Pig с HDInsight](hdinsight-use-pig.md) — узнайте о различных способах выполнения задания Pig в кластере.
- [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md) — узнайте, как HDInsight использует хранилище больших двоичных объектов Azure для хранения данных кластеров HDInsight.
- [Загрузка данных в HDInsight](hdinsight-upload-data.md) — узнайте, как работать с данными, хранящимися в хранилище больших двоичных объектов Azure для кластера HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Отображение кластеров"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Использование Sqoop вместе с HDInsight"

<!---HONumber=Oct15_HO1-->