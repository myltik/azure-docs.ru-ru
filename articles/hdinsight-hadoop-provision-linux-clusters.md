<properties 
   pageTitle="Подготовка кластеров Hadoop в HDInsight на платформе Linux | Azure" 
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
   ms.date="02/18/2015"
   ms.author="nitinme"/>


#Подготовка кластеров Hadoop в HDInsight на платформе Linux с использованием настраиваемых параметров (предварительная версия)

В этой статье вы познакомитесь с различными способами настраиваемой подготовки кластера Hadoop на платформе Linux в Azure HDInsight - с использованием портала управления Azure, PowerShell, программ командной строки или пакета SDК для HDInsight .NET.

## Что такое кластер HDInsight?

Интересовались ли вы когда-нибудь, что при упоминании кластеров мы всегда говорим о Hadoop или данных большого размера? Это объясняется тем, что Hadoop обеспечивает распределенную обработку данных большого размера в различных узлах кластера. Кластер имеет архитектуру ведущий/ведомый с ведущим (также называемым головным узлом или узлом имени) и любым количеством ведомых (также неназываемых узлами данных). Дополнительную информацию см. в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight Cluster][img-hdi-cluster]

Кластер HDInsight абстрагирует сведения о реализации Hadoop, в результате чего не возникает проблем взаимодействия в различными узлами кластера. При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. Дополнительную информацию см. в статье [Введение для Hadoop в HDInsight](../hdinsight-hadoop-introduction/). Данные, которые будут перепроверены, располагаются в хранилище больших двоичных объектов Azure, также называемом *Azure Storage - Blob* (или WASB), в контексте HDInsight. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage/).

В этой статье приводятся указания по различным способам подготовки кластера. Информацию о быстрой подготовке кластера см. в разделе [Приступая к работе с Azure HDInsight в Linux](../hdinsight-hadoop-linux-get-started).

**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure - это платформа на основе подписок. Командлеты HDInsight PowerShell предназначены для выполнения задач по подписке. Дополнительную информацию о получении подписки в разделе <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">"Варианты приобретения"</a>, <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">"Предложения для участников"</a>или <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">"Бесплатная пробная версия"</a>.
- Ключи SSH. Требуется для удаленного доступа к кластеру Linux по протоколу SSH. Указания по созданию ключей SSH см. в следующих статьях:
	-  На компьютере с ОС Linux - [Использование SSH с Hadoop в HDInsight на платформе Linux из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix).
	-  На компьютере с ОС Windows - [Использование SSH с Hadoop в HDInsight на платформе Linux в Windows](../hdinsight-hadoop-linux-use-ssh-windows).

## Содержание

* [Параметры конфигурации](#configuration)
* [Параметры для подготовки кластеров HDInsight на платформе Linux](#options)
* [Дальнейшие действия](#nextsteps)

## <a id="configuration"></a>Варианты настройки

### Кластеры в Linux

HDInsight предоставляет возможность подготовки кластеров Linux в Azure. Подготовьте кластер Linux, если вы знакомы с ОС Linux или Unix, выполняете миграцию из существующего решения Hadoop на платформе Linux, или хотите с легкостью выполнить интеграцию с компонентами экосистемы Hadoop, созданными для Linux. Дополнительную информацию об Azure HDInsight на платформе Linux см. в статье [Введение для Hadoop в HDInsight](../hdinsight-hadoop-introduction). 

### Дополнительное хранилище

В процессе настройки вам необходимо задать учетную запись хранилища BLOB-объектов и контейнер по умолчанию. Кластер будет использовать эти данные для обозначения месторасположения контейнера по умолчанию. При желании можно также указать дополнительные BLOB-объекты, которые будут связаны с кластером.

Дополнительные сведения об использовании вторичных хранилищ BLOB-объектов см. в разделе [Использование хранилища BLOB-объектов с HDInsight](http://azure.microsoft.com/ documentation/articles/hdinsight-use-blob-storage/).

### Хранилище мета-данных

Хранилище мета-данных содержит информацию о таблицах Hive, разделах, схемах и т. д. Эта информация используется HIve для определения места расположения информации на HDFS (или WASB на HDInsight). По умолчанию Hive использует встроенную базу данных для хранения подобной информации.

Во время подготовки кластеров HDInsight вы можете задать базу данных SQL, которая будет выполнять роль хранилища метаданных для Hive. Это позволит сохранить метаданные при удалении кластера, поскольку вся информация будет храниться во внешней базе данных SQL.

> [WACOM.NOTE] В настоящее время возможность использования метахранилищ доступна только при подготовке HDInsight для Linux с помощью пакета SDK для .NET. Инструкции см. в разделе [Подготовка кластеров HDInsight на платформе Linux с помощью пакета SDK для .NET](#sdk).


## <a id="options"></a> Параметры подготовки кластера HDInsight на платформе Linux

Кластер HDInsight Hadoop на платформе Linux можно подготовить с компьютера под управлением ОС Linux или Windows. В следующей таблице содержится информация о подготовке параметров, доступных в разных операционных системах, а также ссылки на инструкции для каждого из них.

Подготовка кластеров на платформе Linux на компьютере под управлением этой ОС| С помощью портала управления Azure | С помощью кроссплатформенного интерфейса командной строки | С помощью пакета SDK для .NET | С помощью PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Щелкните [здесь](#portal) | Щелкните [здесь](#cli)| Неприменимо | Будет доступно в ближайшее время
Windows | Щелкните [здесь](#portal) | Щелкните [здесь](#cli) | Щелкните [здесь](#sdk) | Будет доступно в ближайшее время

### <a id="portal"></a> Использование портала управления Azure

Кластер HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight необходимо создать учетную запись хранения Azure для того же центра обработки данных. Дополнительную информацию см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage]. Сведения о создании учетной записи хранения Azure см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].


> [WACOM.NOTE] В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная Азия**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США** и **Южно-центральный регион США**.

**Чтобы создать кластер HDInsight с использованием возможности настраиваемого создания, сделайте следующее.**

1. Войдите на [портал управления Azure][azure-management-portal].
2. Щелкните **+ СОЗДАТЬ** в нижней части страницы, щелкните **СЛУЖБЫ ДАННЫХ**, щелкните **HDINSIGHT**, затем щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3. На странице **Сведения о кластере** введите или выберите следующие значения:

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя кластера</td>
			<td><p>Имя кластера. </p>
				<ul>
				<li>DNS-имя должно начинаться и заканчиваться с буквы или цифры и может содержать дефисы.</li>
				<li>Поле должно представлять собой строку от 3 до 63 символов.</li>
				</ul></td></tr>
		<tr><td>Тип кластера</td>
			<td>Для типа кластера выберите <strong>Hadoop</strong>.</td></tr>
		<tr><td>Операционная система</td>
			<td>Выберите <b>предварительную версию Ubuntu 12.04 LTS</b> для подготовки кластера HDInsight в Linux. Информацию о подготовке кластера Windows см. в статье <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/" target="_blank">Подготовка кластеров Hadoop в HDInsight на платформе Windows</a>.</td></tr>
		<tr><td>Версия HDInsight</td>
			<td>Выберите версию. Для HDInsight на платформе Linux по умолчанию используется HDInsight версии 3.2, которая использует Hadoop 2.5.</td></tr>
		</table>

	Введите или выберите значения, указанные в таблице, а затем щелкните стрелку вправо.

4. На странице **Настройка кластера** введите или выберите следующие значения:

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Узлы данных</td><td>Число узлов данных, которые требуется развернуть. В рамках тестирования создайте кластер с одним узлом. <br />Максимальный размер кластера зависит от подписки Azure. Обратитесь в службу поддержки Azure по вопросам выставления счетов для увеличения лимита.</td></tr>
	<tr><td>Регион/виртуальная сеть</td><td><p>Выберите тот же регион, что и для учетной записи хранения, созданной в предыдущей процедуре. HDInsight требует размещения учетной записи хранения в том же регионе. При последующей настройке можно выбрать только учетную запись хранения, которая находится в том же регионе, который указан здесь.</p><p>Доступны следующие регионы: <strong>Восточная Азия</strong>, <strong>Юго-Восточная Азия</strong>, <strong>Северная Европа</strong>, <strong>Западная Европа</strong>, <strong>Восток США</strong>, <strong>Запад США</strong>, <strong>Северо-центральный регион США</strong>, <strong>Южно-центральный регион США</strong><br/></p></td></tr>
	</table>



5. На странице **Настройка пользователя кластера** введите следующие значения:

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Пароль HTTP</td>
			<td>Укажите пароль для пользователя HTTP по умолчанию: <i>admin</i>.</td></tr>
		<tr><td>Имя пользователя SSH</td>
			<td>Укажите имя пользователя SSH. Это имя пользователя будет использоваться для инициирования удаленного сеанса SSH на узлах кластера HDInsight.</td></tr>
		<tr><td>Тип аутентификации SSH</td>
			<td>Укажите, что следует использовать для аутентификации пользователя SSH: пароль или ключ SSH.</td></tr>
		<tr><td>Пароль SSH</td>
			<td>Если вы выбрали пароль как тип аутентификации, укажите пароль SSH для аутентификации пользователя SSH. Вам будет предложено ввести этот пароль при попытке инициировать сеанс SSH на удаленном компьютере с ОС Linux.</td></tr>
		<tr><td>Открытый ключ SSH</td>
			<td>Если в качестве типа аутентификации выбран ключ, укажите открытый ключ SSH, который уже должен быть сформирован. При запуске сеанса SSH с примечанием в кластере Linux будет использоваться закрытый ключ, связанный с данным открытым ключом.<br>
			Указания по созданию ключа SSH на компьютере с ОС Linux см. <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">здесь</a>. Инструкции по созданию ключа SSH на компьютере Windows см. <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">здесь</a>.
		</td></tr>
		</table>

	> [WACOM.NOTE] Рекомендуется использовать для SSH аутентификацию с помощью открытого ключа SSH, так как это безопаснее, чем аутентификация с помощью пароля.

	Щелкните стрелку вправо.


6. На странице **Учетная запись хранения** введите следующие значения:

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Учетная запись хранения</td>
			<td>Укажите учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера HDInsight. Можно выбрать один из трех вариантов:
			<ul>
				<li>Использовать существующее хранилище</li>
				<li>Создать новое хранилище</li>
				<li>Использовать хранилище другой подписки</li>
			</ul>
			</td></tr>
		<tr><td>Имя учетной записи</td>
			<td><ul>
				<li>Если вы хотите использовать существующее хранилище, в поле <strong>Имя учетной записи</strong>выберите существующее имя учетной записи хранения. В раскрывающемся списке указываются только учетные записи хранения, расположенные в том же центре обработки данных, в котором выполняется подготовка кластера.</li>
				<li>Если вы выберите параметр <strong>Создать новое хранилище</strong> или <strong>Использовать хранилище другой подписки</strong> , необходимо указать имя учетной записи хранения.</li>
			</ul></td></tr>
		<tr><td>Ключ учетной записи</td>
			<td>Если вы выберите параметр <strong>Использовать хранилище другой подписки</strong> , укажите ключ учетной записи для соответствующей учетной записи хранения.</td></tr>
		<tr><td>Контейнер по умолчанию</td>
			<td><p>Задает контейнер по умолчанию в учетной записи хранения, который используется в качестве файловой системы по умолчанию для кластера HDInsight. Если вы выберите параметр <strong>Использовать существующее хранилище</strong> для поля <strong>Учетная запись хранения</strong> , и в этой учетной записи нет существующих контейнеров, то контейнер создается по умолчанию с тем же именем, что и имя кластера. Если контейнер с именем кластера уже существует, к имени контейнера будет добавлено последовательное число. Например, мой_контейнер1, мой_контейнер2 и т. д. Однако, если в существующей учетной записи хранения есть контейнер с иным, чем у кластера именем, можно также использовать и этот контейнер.</p>
            <p>Если создается новое хранилище или используется хранилище из другой подписки Azure, необходимо указать имя контейнера по умолчанию</p>
        </td></tr>
		<tr><td>Дополнительные учетные записи хранения</td>
			<td>HDInsight поддерживает несколько учетных записей хранения. Нет ограничений на дополнительную учетную запись хранения, которая может использоваться в кластере. Тем не менее, при создании кластера с использованием портала управления ограничением является семь единиц вследствие ограничений пользовательского интерфейса. Каждая дополнительная учетная запись хранения, указанная в этом поле, добавляет дополнительную страницу учетной записи хранения к мастеру, где можно указать сведения об учетной записи. Например, на следующем снимке экрана выбрана 1 дополнительная учетная запись хранения, поэтому в диалоговое окно добавляется страница 5.</td></tr>
	</table>

	Щелкните стрелку вправо.

7. Если вы решили настроить дополнительное хранилище для кластера, на странице **Учетная запись хранения** введите данные для учетной записи дополнительного хранилища:

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    Здесь снова можно выбрать существующее хранилище, создать новое хранилище или использовать хранилище из другой подписки Azure. Процедура задания значений аналогична предыдущему шагу.

    > [WACOM.NOTE] После выбора учетной записи хранения Azure для кластера HDInsight невозможно ни удалить учетную запись, ни изменить ее на другую.

 	Выбрав дополнительную учетную запись хранения, щелкните флажок, чтобы начать подготовку кластера. 

###<a id="cli"></a> Использование кроссплатфомернной командной строки

> [WACOM.NOTE] Начиная с 29.08.2014 кроссплатформенный интерфейс командной строки не может использоваться для "связывания" кластера с виртуальной сетью Azure.

Другим вариантом подготовки кластера HDInsight является интерфейс кроссплатформенной командной строки. Средство командной строки реализовано в Node.js. Его можно использовать на любой платформе, которая поддерживает Node.js, включая Windows, Mac и Linux. Средство командной строки имеет открытый исходный код.  Управлять исходным кодом можно с помощью GitHub по адресу <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>. Общее руководство по использованию интерфейса командной строки см. в разделе [Использование средств командной строки Azure для Mac и Linux][azure-command-line-tools]. Полную справочную документацию см. в разделе [Средства командной строки Windows Azure для Mac и Linux][azure-command-line-tool].

- [Настройка кроссплатформенной командной строки Azure для Linux](#clilin)
- [Настройка кроссплатформенной командной строки Azure для Windows](#cliwin)
- [Подготовка кластеров HDInsight с помощью кроссплатформенной командной строки Azure](#cliprovision)

#### <a id="clilin"></a>Настройка кроссплатформенной командной строки для Linux

Выполните следующие процедуры, чтобы настроить компьютер с ОС Linux для использования программ командной строки Azure.

- Установка кроссплатформенной командной строки с помощью NPM
- Подключение к подписке Azure

**Установка интерфейса командной строки с помощью NPM**

1.	Откройте окно терминала на компьютере с ОС Linux и выполните следующую команду:

		sudo npm install -g azure-cli

2.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Для подключения к подписке Azure**

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [WACOM.NOTE] Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker. 


1.	Откройте окно терминала.
2.	Выполните следующую команду для входа в подписку Azure.

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Команда запускает веб-страницу для загрузки с нее файла параметров публикации. Если веб-страница не открывается, щелкните ссылку в окне терминала, чтобы открыть страницу в браузере и войти на портал. 

3.	Скачайте файл параметров публикации на компьютер.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Настройка кроссплатформенной командной строки для Windows

Выполните следующие процедуры, чтобы настроить компьютер с ОС Windows для использования программ командной строки Azure.

- Установка кроссплатформенной командной строки (с помощью NPM или установщика Windows)
- Загрузите и импортируйте параметры публикации учетной записи Azure


Интерфейс командной строки можно установить, используя *Node.js Package Manager (NPM)* или установщик Windows. Майкрософт рекомендует проводить установку с использованием только одного из двух параметров.

**Установка интерфейса командной строки с помощью NPM**

1.	Перейдите к **www.nodejs.org**
2.	Щелкните **УСТАНОВИТЬ** и выполните инструкции с использованием параметров по умолчанию.
3.	Откройте **окно командой строки** (или *Azure Command Prompt*, или *Developer Command Prompt for VS2012*) на рабочей станции.
4.	Выполните следующую команду в окне командной строки.

		npm install -g azure-cli

	> [WACOM.NOTE] Если вы видите сообщение об ошибке, в котором говорится, что команда NPM не найдена, проверьте наличие в переменой среды PATH следующих путей: <i>C:\Program Files (x86)\nodejs;C:\Users\[имя_пользователя]\AppData\Roaming\npm</i> или <i>C:\Program Files\nodejs;C:\Users\[имя_пользователя]\AppData\Roaming\npm</i>

5.	Выполните следующую команду, чтобы проверить установку:

		azure hdinsight -h

	Можно использовать переключатель *-h* на различных уровнях для отображения справочной информации. Например:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Установка интерфейса командной строки с помощью установщика Windows**

1.	Перейдите по адресу: **http://azure.microsoft.com/ downloads/**.
2.	Прокрутите вниз до раздела **Средства командной строки**, щелкните **Кроссплатформенный интерфейс командной строки** и следуйте инструкциям мастера установщика веб-платформы.

**Загрузка и импорт параметров публикации**

Прежде чем использовать интерфейс командной строки, необходимо настроить связь между рабочей станцией и Azure. Сведения о подписке Azure используются интерфейсом командной строки для подключения к учетной записи. Эти сведения можно получить через портал Azure в файле параметров публикации. Затем файл параметров публикации можно импортировать как постоянный локальный параметр конфигурации, который интерфейс командной строки будет использовать для последующих операций. Вам необходимо импортировать параметры публикации только один раз.

> [WACOM.NOTE] Файл параметров публикации содержит конфиденциальную информацию. Рекомендуется удалить файл или выполнить дополнительные действия для шифрования папки, содержащей файл. На Windows измените свойства папки или используйте BitLocker.


1.	Откройте **окно командой строки**.
2.	Выполните следующую команду для загрузки файла параметров публикации.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Команда запускает веб-страницу для загрузки с нее файла параметров публикации.

3.	При появлении запроса на сохранение файла щелкните **Сохранить** и укажите место, в котором должен быть сохранен файл.
5.	В окне командной строки выполните следующую команду для импорта файла параметров публикации.

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Подготовка кластеров HDInsight с помощью кроссплатформенной командной строки Azure

Для подготовки кластера HDInsight с помощью кроссплатформенной командной строки необходимы следующие процедуры:

- Создание учетной записи хранения Azure
- Подготовка кластера


**Создание учетной записи хранения Azure**

HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure. Учетная запись хранения должна находиться в том же центре обработки данных.

- Из окна командной строки выполните следующую команду для создания учетной записи хранения Azure:

		azure storage account create [options] <StorageAccountName>

	При появление запроса на указание места, выберите местоположение, в котором может быть подготовлен кластер HDINsight. Хранилище должно находиться в одном местоположении с кластером HDInsight. В настоящее время только следующие регионы могут размещать кластеры HDInsight: **Восточная Азия**, **Юго-Восточная Азия**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США** и **Южно-центральный регион США**.  

Информацию о создании новой учетной записи хранения Azure с помощью портала управления Azure см. в разделе [Создание, управление или удаление учетной записи хранения][azure-create-storageaccount].

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	-- Отображение списка учетных записей хранения
	список учетных записей хранения Azure

	-- Отображение информации об учетной записи хранения
	отображение учетной записи хранения Azure <StorageAccountName>

	-- Отображение списка ключей для учетной записи хранения
	список ключей учетной записи хранения Azure <StorageAccountName>

Дополнительные сведения о получении сведений с помощью портала управления см. в разделе *How to: View, copy and regenerate storage access keys* в [Создание, управление или удаление учетной записи хранения][azure-create-storageaccount].

Кластер HDInsight также требует наличия контейнера в учетной записи хранения. Если указанная учетная запись хранения еще не имеет контейнера, то команда *azure hdinsight cluster create* запросит имя контейнера, а также создаст его. Однако, для создания контейнера заранее, можно использовать следующую команду:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.

**Подготовка кластера HDInsight**

- Введите в окне командной строки следующую команду:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword> --OSType Linux --SshUserName <SSH username> --SshPassword <SSH user password>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Подготовка кластера HDInsight с использованием файла конфигурации**

Как правило, необходимо подготовить кластер HDInsight, выполнить задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждой новой подготовке кластера.

- Введите в окне командной строки следующие команды:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>" --OSType Linux --SshUserName "<SSH username>" --SshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] База данных Azure SQL, используемая в качестве хранилища метаданных, должна обеспечивать подключение к другим службам Azure, в том числе Azure HDInsight. На панели мониторинга базы данных Azure SQL в правой части щелкните имя сервера. Это сервер, на котором работает экземпляр базы данных SQL. В представлении сервера щелкните **Настройка**, затем **Службы Windows Azure**, **Да** и **Сохранить**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Отображение сведений о кластере**

- Используйте следующие команды для отображения сведений о кластере:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Удаление кластера**

- Используйте следующую команду для удаления кластера:

		azure hdinsight cluster delete <ClusterName>



###<a id="sdk"></a> Использование пакета HDInsight .NET SDK
Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET.

Для подготовки кластера HDInsight с использованием пакета SDK в Linux необходимо выполнить следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).


**Создание консольного приложения Visual Studio**

1. Откройте Visual Studio 2013.

2. В меню "Файл" щелкните **Создать**, затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:

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

5. В меню **Инструменты** нажмите **Диспетчер пакетов Nuget**, а затем - **Консоль диспетчера пакетов**.

6. Для установки пакетов выполните следующие команды на консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. Двойным щелчком откройте **Program.cs** в обозревателе решений.

8. Добавьте в начало файла следующие инструкции:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. В функции Main() скопируйте и вставьте следующий код:

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
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
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
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

10. Замените переменные в начале функции main().

**Запуск приложения**

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. На подготовку кластера HDInsight может уйти несколько минут.



##<a id="nextsteps"></a> Дальнейшие действия
В этой статье вы ознакомились с несколькими способами подготовки кластера HDInsight Hadoop в Linux. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Работа с HDInsight в Linux](../hdinsight-hadoop-linux-information). Узнайте об особенностях работы с кластером HDInsight в Linux.
- [Управление кластерами HDInsight с помощью Ambari](../hdinsight-hadoop-manage-ambari). Информация о том, как отслеживать работу ПО Hadoop в кластере HDInsight на платформе Linux с помощью веб-интерфейса Ambari или Ambari REST API, а также управлять его функционированием. 
- [Использование MapReduce с HDInsight][hdinsight-use-mapreduce]. Информация о различных способах выполнения заданий MapReduce в кластере.
- [Использование Hive с HDInsight][hdinsight-use-hive]. Информация о различных способах выполнения запроса Hive в кластере.
- [Использование Pig с HDInsight][hdinsight-use-pig]. Информация о различных способах выполнения задания Pig в кластере.
- [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage). Информация о том, как HDInsight использует хранилище больших двоичных объектов Azure для хранения данных кластеров HDInsight.
- [Отправка данных в HDInsight][hdinsight-upload-data]. Узнайте, как работать с данными, хранящимися в хранилище больших двоичных объектов Azure для кластера HDInsight.

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /ru-ru/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=45--> 
