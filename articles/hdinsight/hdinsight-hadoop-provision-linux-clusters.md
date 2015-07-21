<properties
   pageTitle="Подготовка кластеров Hadoop на платформе Linux в HDInsight | Microsoft Azure"
   description="Узнайте, как подготовить кластеры Hadoop для HDInsight на платформе Linux с помощью портала управления, командной строки и пакета SDK для .NET."
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
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#Подготовка кластеров Hadoop в HDInsight на платформе Linux с использованием настраиваемых параметров (предварительная версия)

В этой статье вы ознакомитесь с различными способами настраиваемой подготовки кластера Hadoop на платформе Linux в Azure HDInsight с использованием портала Azure, Azure PowerShell, интерфейса командной строки Azure или пакета SDК для HDInsight .NET.

## Что такое кластер HDInsight?

Интересовались ли вы когда-нибудь, почему мы упоминаем кластеры каждый раз, когда говорим о Hadoop или данных большого размера? Это объясняется тем, что Hadoop обеспечивает распределенную обработку данных большого размера в различных узлах кластера. Кластер имеет архитектуру «главный/рабочий» с главным узлом (также называемым головным узлом или узлом имени) и любым количеством рабочих узлов (также называемых узлами данных). Дополнительную информацию см. в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Кластер HDInsight][img-hdi-cluster]


Кластер HDInsight абстрагирует сведения о реализации Hadoop, в результате чего не возникает проблем взаимодействия в различными узлами кластера. При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Для получения дополнительной информации см. раздел [Введение для Hadoop в HDInsight](hdinsight-hadoop-introduction.md). Данные для обновления находятся в хранилище больших двоичных объектов Azure. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).


В этой статье приводятся указания по различным способам подготовки кластера. Информацию о быстрой подготовке кластера см. в статье [Приступая к работе с Azure HDInsight в Linux](../hdinsight-hadoop-linux-get-started.md).

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Ключи Secure Shell (SSH)**. При удаленном использовании кластера на платформе Linux с помощью SSH вместо пароля рекомендуется использовать ключ, так как он является более безопасным. Указания по созданию ключей SSH см. в следующих статьях:
	-  на компьютере под управлением Linux — [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md);
	-  на компьютере под управлением Windows — [Использование SSH с кластерами HDInsight (Hadoop) под управлением Linux в Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Варианты настройки

### Кластеры в Linux

HDInsight предоставляет возможность подготовки кластеров Linux в Azure. Подготовьте кластер Linux, если вы знакомы с Linux или Unix, выполняете миграцию с существующего решения Hadoop под управлением Linux или хотите с легкостью выполнить интеграцию с компонентами экосистемы Hadoop, созданными для Linux. Дополнительную информацию об Azure HDInsight на платформе Linux см. в статье [Введение в Hadoop в HDInsight](hdinsight-hadoop-introduction.md).


### Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища больших двоичных объектов Azure и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительные BLOB-объекты, которые будут связаны с кластером.


Дополнительную информацию об использовании вторичных хранилищ больших двоичных объектов см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).


### Хранилище мета-данных

В метахранилище содержится информация о таблицах Hive, разделах, схемах, столбцах и т. д. Эта информация используется Hive для определения места расположения данных в распределенной файловой системе Hadoop (HDFS) или в хранилище больших двоичных объектов Azure для HDInsight. По умолчанию Hive использует встроенную базу данных для хранения подобной информации.

Во время подготовки кластера HDInsight вы можете задать базу данных SQL, которая будет выполнять роль метахранилища для Hive. Это позволит сохранить метаданные при удалении кластера, так как вся информация будет храниться во внешней базе данных SQL.


> [AZURE.NOTE]В настоящее время возможность использования метахранилищ доступна только при подготовке HDInsight для Linux с помощью пакета SDK для .NET. Указания см. в разделе [Подготовка кластеров HDInsight на платформе Linux с помощью пакета SDK для .NET](#sdk).



## <a id="options"></a>Варианты подготовки кластера HDInsight на платформе Linux

Кластер HDInsight Hadoop на платформе Linux можно подготовить с компьютера под управлением Linux или Windows. В следующей таблице содержится информация о подготовке параметров, доступных в разных операционных системах, а также ссылки на инструкции для каждого из них.

Подготовка кластеров на платформе Linux на компьютере под управлением этой ОС| Использование портала Azure | Использование интерфейса командной строки Azure | Использование пакета SDK для .NET | Использование Azure PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Щелкните [здесь](#portal) | Щелкните [здесь](#cli)| Не применяется | Не применяется
Windows | Щелкните [здесь](#portal) | Щелкните [здесь](#cli) | Щелкните [здесь](#sdk) | Щелкните [здесь](#powershell)

### <a id="portal"></a> Использование портала Azure

Кластеры HDInsight используют контейнер хранилища больших двоичных объектов Azure как файловую систему по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure в том же центре обработки данных. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md). Информацию о создании учетной записи хранения Azure см. в разделе [Как создать учетную запись хранения](../storage-create-storage-account.md).


> [AZURE.NOTE]В настоящее время кластеры HDInsight на платформе Linux можно размещать только в регионах **Юго-Восточной Азии**, **Северной Европы**, **Восточной части США** и **Южно-центральной части США**.

**Создание кластера HDInsight с использованием параметра «Настраиваемое создание»**

1. Войдите на [портал Azure][azure-management-portal].
2. В нижней части страницы щелкните **+СОЗДАТЬ**, затем последовательно щелкните **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице **Подробные сведения о кластере** введите или выберите следующие значения:

	![Укажите подробную информацию о кластере Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя кластера</td>
		<td><p>Имя кластера. </p>
			<ul>
			<li>DNS-имя должно начинаться и заканчиваться буквенно-цифровым символом и может содержать дефисы.</li>
			<li>Поле должно представлять собой строку от 3 до 63 символов.</li>
			</ul></td></tr>
	<tr><td>Тип кластера</td>
		<td>Выберите <strong>Hadoop</strong>.</td></tr>
	<tr><td>Операционная система</td>
		<td>Выберите <b>предварительную версию Ubuntu 12.04 LTS</b> для подготовки кластера HDInsight в Linux. Информацию о подготовке кластера на платформе Windows см. в статье <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Подготовка кластеров Hadoop на платформе Windows в HDInsight</a>.</td></tr>
	<tr><td>Версия HDInsight</td>
		<td>Выберите версию. Для HDInsight на платформе Linux по умолчанию используется HDInsight версии 3.2, которая использует Hadoop 2.6.</td></tr>
	</table>

	Введите или выберите значения, указанные в таблице, а затем щелкните стрелку вправо.

4. На странице **Настройка кластера** введите или выберите следующие значения:

	![Укажите подробную информацию о кластере Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Узлы данных</td><td>Число узлов данных, которые требуется развернуть. В рамках тестирования создайте кластер с одним узлом. <br />Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.</td></tr>
	<tr><td>Регион/виртуальная сеть</td><td><p>Выберите тот же регион, в котором ранее была создана учетная запись хранения. Для кластеров HDInsight требуется, чтобы учетная запись хранения находилась в том же регионе. При последующей настройке можно выбрать только учетную запись хранения, которая находится в том же регионе, который указан здесь.</p></td></tr>
	<tr><td>Размер головного узла</td><td><p>Выберите размер виртуальной машины для головного узла.</p></td></tr>
	<tr><td>Размер узла данных</td><td><p>Выберите размер виртуальной машины для узлов данных.</p></td></tr>
	</table>

	>[AZURE.NOTE]Цены зависят от размера выбранных виртуальных машин. HDInsight поддерживает для узлов кластера все виртуальные машины стандартного уровня. Дополнительную информацию о том, как размер виртуальной машины влияет на цены, см. в разделе <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight Цены</a>.


5. На странице **Настройка пользователя кластера** введите следующие значения:

    ![Укажите подробную информацию о пользователе кластера Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Пароль HTTP</td>
		<td>Укажите пароль для пользователя HTTP по умолчанию: <strong>admin</strong>.</td></tr>
	<tr><td>Имя пользователя SSH</td>
		<td>Укажите имя пользователя SSH. Это имя пользователя будет использоваться для инициирования удаленного сеанса SSH на узлах кластера HDInsight.</td></tr>
	<tr><td>Тип аутентификации SSH</td>
		<td>Укажите, что следует использовать для аутентификации пользователя SSH: пароль или ключ SSH.</td></tr>
	<tr><td>Пароль SSH</td>
		<td>Если в качестве типа аутентификации выбран пароль, укажите пароль SSH для аутентификации пользователя SSH. Вам будет предложено ввести этот пароль при попытке инициировать сеанс SSH на удаленном компьютере с ОС Linux.</td></tr>
	<tr><td>Открытый ключ SSH</td>
		<td>Если в качестве типа аутентификации выбран ключ, укажите открытый ключ SSH, который уже должен быть сформирован. При запуске сеанса SSH с узлом в кластере Linux будет использоваться закрытый ключ, связанный с данным открытым ключом.<br>
		Указания по созданию ключа SSH на компьютере под управлением Linux см. <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">здесь</a>. Указания по созданию ключа SSH на компьютере под управлением Windows см. <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">здесь</a>.
	</td></tr>
	<tr><td>Ввести куст или метахранилище Oozie</td>
		<td>Установите флажок, чтобы указать базу данных SQL, которая будет использоваться в качестве метахранилища Hive или Oozie, в том же центре обработки данных, где расположен кластер. Если флажок установлен, на следующих страницах мастера нужно указать информацию о базе данных SQL Azure. Это может оказаться полезным, если необходимо сохранить метаданные заданий Hive/Oozie даже после удаления кластера.</td></tr>
	</td></tr>
	</table>

	> [AZURE.NOTE]Мы советуем использовать для SSH аутентификацию с помощью открытого ключа SSH, так как это безопаснее, чем аутентификация с помощью пароля.

	Щелкните стрелку вправо.

6. На странице **Настройка метахранилища Hive или Oozie** введите следующие значения:

    ![Укажите подробную информацию о пользователе кластера Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Укажите базу данных SQL Azure, которая будет использоваться как метахранилище Hive или Oozie. Вы можете указать одну и ту же базу данных для метахранилища Hive и Oozie. Эта база данных SQL должна находиться в том же центре обработки данных, что и кластер HDInsight. В списке перечислены только базы данных SQL, которые находятся в том же центре обработки данных, который указан на странице <strong>Подробные сведения о кластере</strong>. Укажите также имя пользователя и пароль для подключения к выбранной базе данных SQL Azure.

    >[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

    Щелкните стрелку вправо.


6. На странице **Учетная запись хранения** введите следующие значения:


    ![Укажите учетную запись хранения для кластера Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

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
			<li>Если используется существующее хранилище, выберите для параметра <strong>Имя учетной записи</strong> существующую учетную запись хранения. В раскрывающемся списке содержатся только те учетные записи хранения, которые расположены в том же центре обработки данных, где выполняется подготовка кластера.</li>
			<li>Если используется параметр <strong>Создать новое хранилище</strong> или <strong>Использовать хранилище из другой подписки</strong>, необходимо указать имя учетной записи хранения.</li>
		</ul></td></tr>
	<tr><td>Ключ учетной записи</td>
		<td>Если используется параметр <strong>Использовать учетную запись из другой подписки</strong>, необходимо указать ключ этой учетной записи хранения.</td></tr>
	<tr><td>Контейнер по умолчанию</td>
		<td><p>Задайте контейнер по умолчанию в учетной записи хранения, которая используется в качестве файловой системы по умолчанию для кластера HDInsight. Если вы выберете <strong>Использовать существующее хранилище</strong> в поле <strong>Учетная запись хранения</strong> и в этой учетной записи нет контейнеров, то контейнер создается по умолчанию с тем же именем, что и имя кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д. Однако, если в существующей учетной записи хранения есть контейнер с именем, отличающимся от имени кластера, можно также использовать и этот контейнер.</p>
        <p>Если создается новое хранилище или используется хранилище из другой подписки Azure, необходимо указать имя контейнера по умолчанию.</p>
    </td></tr>
	<tr><td>Дополнительные учетные записи хранения</td>
		<td>HDInsight поддерживает несколько учетных записей хранения. Ограничения на дополнительные учетные записи хранения, которые могут использоваться в кластере, отсутствуют. Тем не менее, при создании кластера с использованием портала Azure можно использовать не более семи учетных записей из-за ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу <strong>учетной записи хранения</strong> к мастеру, где можно указать информацию об учетной записи. Например, на следующем снимке экрана выбрана одна дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 5.</td></tr>
	</table>

	Щелкните стрелку вправо.

7. Если вы решили настроить дополнительное хранилище для кластера, на странице **Учетная запись хранения** введите данные для дополнительной учетной записи хранения:

	![Укажите дополнительную информацию о хранилище для кластера HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    Здесь снова можно выбрать существующее хранилище, создать новое хранилище или использовать хранилище из другой подписки Azure. Процедура задания значений аналогична предыдущему шагу.


    > [AZURE.NOTE]После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни задать ее для другого кластера.


 	Выбрав дополнительную учетную запись хранения, щелкните флажок, чтобы начать подготовку кластера.

###<a id="cli"></a> Использование интерфейса командной строки Azure (Azure CLI)

Другим вариантом подготовки кластера HDInsight является использование интерфейса командной строки Azure. Интерфейс командной строки (CLI) Azure реализован в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Интерфейс командной строки Azure можно установить из следующих расположений:

- **пакет SDK для Node.js** — <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>;
- **Интерфейс командной строки Azure CLI для Mac, Linux и Windows** — <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>.  

Общее руководство по использованию интерфейса командной строки Azure см. в статье [Интерфейс командной строки Azure для Mac, Linux и Windows](../xplat-cli.md).

Приведенные ниже инструкции помогут в установке интерфейса командной строки Azure на базе Linux и Windows с дальнейшим использованием командной строки для подготовки кластера.

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

	Можно использовать переключатель **-h** на различных уровнях для отображения справочной информации. Например:

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

	![HDI. Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Эта команда запускает веб-страницу, с которой нужно скачать файл параметров публикации. Если веб-страница не открывается, щелкните ссылку в окне терминала, чтобы открыть страницу в браузере и войти на портал.

3.	Скачайте файл параметров публикации на компьютер.
4.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Настройка интерфейса командной строки Azure для Windows

Выполните следующие процедуры, чтобы настроить компьютер с ОС Windows для использования интерфейса командной строки Azure (Azure CLI).

- Установите интерфейс командной строки Azure с помощью NPM или установщика Windows
- Загрузите и импортируйте параметры публикации учетной записи Azure


Интерфейс командной строки Azure можно установить с помощью NPM или установщика Windows. Майкрософт советует выполнять установку, используя только один из двух вариантов.

**Чтобы установить интерфейс командной строки Azure с помощью NPM**

1.	Перейдите к **www.nodejs.org**
2.	Нажмите **УСТАНОВИТЬ** и следуйте указаниям, используя параметры по умолчанию.
3.	Откройте **окно командной строки** (или **окно командной строки Azure**, или **окно командной строки разработчика VS2012**) на своей рабочей станции.
4.	Выполните следующую команду в окне командной строки:

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Если появилось сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменной среды **PATH** следующих путей: <i>C:\Program Files (x86)\nodejs;C:\Users[имя_пользователя]\AppData\Roaming\npm</i> или <i>C:\Program Files\nodejs;C:\Users[имя_пользователя]\AppData\Roaming\npm</i>.


5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель **-h** на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Чтобы установить интерфейс командной строки Azure с помощью установщика Windows**

1.	Перейдите в **http://azure.microsoft.com/downloads/**.
2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Интерфейс командной строки Azure** и следуйте инструкциям мастера установщика веб-платформы.

**Скачивание и импорт параметров публикации**

Прежде чем использовать интерфейс командной строки Azure, необходимо настроить связь между рабочей станцией и Azure. Информация о подписке Azure используется интерфейсом командной строки Azure для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который будет использоваться интерфейсом командной строки Azure для последующих операций. Вам необходимо импортировать параметры публикации только один раз.


> [AZURE.NOTE]Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. В Windows измените свойства папки или используйте BitLocker.



1.	Откройте окно командной строки.
2.	Выполните следующую команду, чтобы скачать файл параметров публикации:

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Эта команда запускает веб-страницу, с которой нужно скачать файл параметров публикации.


3.	При появлении запроса на сохранение файла щелкните **Сохранить** и укажите место, в котором должен быть сохранен файл.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]


#### <a id="cliprovision"></a>Подготовка кластеров HDInsight с помощью интерфейса командной строки Azure

Подготовка кластера HDInsight с использованием Azure CLI предусматривает следующие процедуры:

- Создание учетной записи хранения Azure
- Подготовка кластера


**Создание учетной записи хранения Azure**

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуется учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

- Выполните следующую команду из окна командной строки для создания учетной записи хранения Azure:

		azure storage account create [options] <StorageAccountName>


	При появлении запроса на указание расположения выберите то, в котором может быть подготовлен кластер HDInsight на платформе Linux. Хранилище должно находиться в одном местоположении с кластером HDInsight.


Информацию о создании новой учетной записи хранения Azure с помощью портала Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account.md).

Если у вас уже есть учетная запись хранения, но вы не знаете ни имени, ни ключа этой учетной записи, можно использовать следующие команды для получения нужной информации:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Дополнительную информацию о получении данных с использованием портала Azure см. в разделе «Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу» статьи [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account.md).

Кластер HDInsight также требует наличия контейнера в учетной записи хранения. Если в указанной учетной записи хранения еще нет контейнера, команда **azure hdinsight cluster create** запросит имя контейнера и создаст его. Однако, для создания контейнера заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

- Введите в окне командной строки следующую команду:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]Для параметров **--userName** и **--password** необходимо указать значения для пользователя Hadoop. Для пользователя Hadoop нужно всегда указывать значение --userName как admin.

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Подготовка кластера HDInsight с использованием файла конфигурации**

Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки Azure предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

- Введите в окне командной строки следующие команды:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Отображение сведений о кластере**

- Используйте следующие команды для отображения сведений о кластере:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Удаление кластера**

- Используйте следующую команду для удаления кластера:

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a>Использование Azure PowerShell
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по подготовке кластера HDInsight с помощью Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Дополнительную информацию об использовании Azure PowerShell с HDInsight см. в статье [Администрирование HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в разделе [Справочная документация по командлетам PowerShell для HDInsight][hdinsight-powershell-reference].

Для подготовки кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

- Создание учетной записи хранения Azure
- Создание контейнера BLOB-объектов Azure
- Создание кластера HDInsight

Вы можете выполнять сценарии с помощью консоли Windows PowerShell или интегрированной среды сценариев (ISE) Windows PowerShell.

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight. В настоящее время кластеры HDInsight на платформе Linux можно размещать только в регионах **Юго-Восточной Азии**, **Северной Европы**, **Восточной части США** и **Южно-центральной части США**.

**Для подключения к учетной записи Azure**

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

Для подготовки кластеров на платформе Linux нужно задать два наиболее важных параметра, в которых необходимо указать тип ОС и подробную информацию о пользователе SSH:

- Обязательно укажите для параметра **-OSType** значение **Linux**.
- Для использования SSH в удаленных сеансах кластеров можно указать пароль пользователя SSH или открытый ключ SSH. Если указать и пароль пользователя SSH, и открытый ключ SSH, ключ будет игнорироваться. Если вам нужно использовать ключ SSH для удаленных сеансов, оставьте поле пустым при запросе пароля SSH.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster


		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]Значения, указываемые для параметра **$clusterCredentials**, используются для создания учетной записи Hadoop в кластере. Эта учетная запись будет использоваться для подключения к кластеру. Значения, указываемые для параметра **$sshCredentials**, используются для создания пользователя SSH в кластере. Используйте эту учетную запись для запуска удаленного сеанса SSH в кластере и выполнения заданий. При подготовке кластера с использованием параметра «Быстрое создание» на портале Azure имя пользователя Hadoop по умолчанию — admin, а имя пользователя SSH по умолчанию — hdiuser.

На подготовку кластера может уйти несколько минут.

![HDI.CLI.Provision][image-hdi-ps-provision]

**Подготовка кластера HDInsight с использованием настраиваемых параметров конфигурации**

При подготовке кластера можно использовать другие параметры конфигурации, такие как подключение к нескольким контейнерам хранилищ больших двоичных объектов Azure или использование базы данных SQL Azure для метахранилищ Hive и Oozie. Это позволяет разделить срок службы данных и метаданных от срока службы кластера.

Для подготовки кластеров на платформе Linux нужно задать два наиболее важных параметра, в которых необходимо указать тип ОС и подробную информацию о пользователе SSH:

- Обязательно укажите для параметра **-OSType** значение **Linux**.
- Для использования SSH в удаленных сеансах кластеров можно указать пароль пользователя SSH или открытый ключ SSH. Если указать и пароль пользователя SSH, и открытый ключ SSH, ключ будет игнорироваться. Если вам нужно использовать ключ SSH для удаленных сеансов, оставьте поле пустым при запросе пароля SSH.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster

		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore

		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных SQL Azure в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, а затем **Службы Azure**, **Да** и **Сохранить**.

На подготовку кластера может уйти несколько минут.

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a> Использование пакета SDK для HDInsight .NET
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework.

Для подготовки кластера HDInsight с использованием пакета SDK в Linux необходимо выполнить следующие процедуры:

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


9. В функции **Main()** скопируйте и вставьте следующий код:

	> [AZURE.NOTE]Укажите в качестве расположения один из следующих регионов, так как только в них можно размещать кластеры HDInsight на платформе Linux: **Юго-Восточная Азия**, **Северная Европа**, **Восток США**, **Южно-Центральный регион США**. Указанная учетная запись хранения также должна находиться в этом регионе.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;


		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Замените переменные в начале функции **Main()**.

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.



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




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Отображение кластеров"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Использование Sqoop вместе с HDInsight"
 

<!---HONumber=62-->