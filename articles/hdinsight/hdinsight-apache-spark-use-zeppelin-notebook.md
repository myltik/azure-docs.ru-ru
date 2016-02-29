<properties 
	pageTitle="Использование записной книжки Zeppelin с кластером Spark в HDInsight (Linux) | Azure" 
	description="Пошаговые инструкции по использованию записных книжек Zeppelin с кластерами Spark в HDInsight на платформе Linux." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Использование записной книжки Zeppelin с кластером Spark в HDInsight (Linux)

Узнайте, как устанавливать записные книжки Zeppelin в кластеры Spark и использовать эти записные книжки.

> [AZURE.IMPORTANT] Записная книжка Zeppelin для кластера HDInsight Spark представлена только для демонстрации применения Zeppelin в среде Azure HDInsight Spark. Если вы хотите использовать записные книжки для работы с HDInsight Spark записных книжек, рекомендуем выбрать вместо Zeppelin записные книжки Jupyter. Записные книжки Jupyter также включают различные варианты ядра (такие как Scala) и постоянно совершенствуются. Инструкции по использованию записных книжек Jupyter с кластером HDInsight Spark см. в разделе [Выполнение запросов Spark SQL с помощью записной книжки Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter).

**Предварительные требования:**

* Прежде чем приступать к изучению этого учебника, необходимо оформить подписку Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
* Клиент SSH. В дистрибутивах Linux и Unix и Macintosh OS X команда `ssh` входит в состав операционной системы. Для Windows рекомендуем воспользоваться [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

	> [AZURE.NOTE] Если вы планируете использовать клиент SSH, отличный от `ssh` или PuTTY, обратитесь к документации своего клиента за информацией о настройке туннеля SSH.

* Веб-браузер, который можно настроить на использование прокси-сервера SOCKS

* __(Необязательно) __: подключаемый модуль, такой как [FoxyProxy](http://getfoxyproxy.org/,), который может применить правила маршрутизации для пропускания через туннель только определенных запросов.

	> [AZURE.WARNING] Без такого подключаемого модуля, как FoxyProxy, все запросы, сделанные через браузер, могут направляться через туннель. Это может привести к замедлению загрузки веб-страниц в браузере.

## Установка записной книжки Zeppelin в процессе создания кластера

Записную книжку Zeppelin в кластере Spark можно установить с помощью действия сценария. Действие сценария использует пользовательские скрипты для установки компонентов в кластере, которые по умолчанию недоступны. Пользовательский скрипт для установки Zeppelin на кластере Spark доступен на ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

### Использование портала Azure

Инструкции по использованию пакета HDInsight .NET SDK для выполнения действия сценария по установке записной книжки Zeppelin см. в разделе [Настройка кластеров HDInsight с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). В инструкции, приведенные в этой статье, необходимо внести несколько изменений.

* Используйте сценарий для установки записной книжки Zeppelin. Необходимый сценарий: ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* Сценарий необходимо выполнять только на головном узле.

* Сценарий не требует никаких параметров.

### Использование пакета HDInsight .NET SDK

Инструкции по использованию пакета HDInsight .NET SDK для выполнения действия сценария по установке записной книжки Zeppelin см. в разделе [Настройка кластеров HDInsight с помощью действий сценариев](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). В инструкции, приведенные в этой статье, необходимо внести несколько изменений.

* Используйте сценарий для установки записной книжки Zeppelin. Необходимый сценарий: ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* Сценарий не требует никаких параметров.

* В качестве типа создаваемого кластера укажите Spark.

### Использование Azure PowerShell

Представленный ниже фрагмент кода PowerShell позволяет создать кластер Spark в HDInsight Linux с установленной записной книжкой Zeppelin. Прежде чем продолжить, установите PowerShell. Инструкции см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Настройка туннелирования SSH для доступа к записной книжке Zeppelin

Для доступа к записным книжкам Zeppelin, работающим из кластера Spark в HDInsight Linux, используются туннели SSH. Описанные ниже действия показывают, как создать туннель SSH с помощью командной строки SSH (Linux) и PuTTY (Windows).

### Создание туннеля с помощью команды SSH (Linux)

Используйте следующую команду для создания туннеля SSH с помощью команды `ssh`. Замените __ИМЯ\_ПОЛЬЗОВАТЕЛЯ__ на имя пользователя SSH для кластера HDInsight, а __ИМЯ\_КЛАСТЕРА__ — на имя кластера HDInsight

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

В результате создается подключение, которое направляет трафик, поступающий на локальный порт 9876, в кластер по протоколу SSH. Доступны следующие параметры.

* **D 9876** — локальный порт, используемый для направления трафика через туннель;

* **C** — сжатие всех данных, так как веб-трафик преимущественно состоит из текста;

* **2** — принудительная попытка использовать только протокол SSH версии 2;

* **q** — тихий режим;

* **T** — отключение распределения псевдотелетайпа, так как выполняется только перенаправление порта;

* **n** — предотвращение считывания с STDIN, так как выполняется только перенаправление порта;

* **N** — запрет на выполнение удаленной команды, так как выполняется только перенаправление порта;

* **f** — выполнение в фоновом режиме.

При настройке кластера с ключом SSH может потребоваться использовать параметр `-i` и указать путь к закрытому ключу SSH.

После завершения команды трафик, отправленный на порт 9876 на локальном компьютере, будет направляться с использованием SSL на головной узел кластера и выглядеть так, будто его источником является головной узел.

### Создание туннеля с помощью PuTTY (Windows)

Для создания туннеля SSH с помощью PuTTY выполните следующие действия.

1. Откройте PuTTY и введите информацию о подключении. Если вы не знакомы с PuTTY, обратитесь к разделу [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md), чтобы узнать, как пользоваться PuTTY с HDInsight.

2. В разделе **Категории** в левой части диалогового окна последовательно разверните **Подключение**, **SSH** и выберите **Туннели**.

3. Введите следующую информацию в форме **Параметры, управляющие перенаправлением портов SSH**:

	* **Порт источника** — порт на стороне клиента, трафик которого нужно перенаправлять. Например, **9876**.

	* **Назначение** — адрес SSH для кластера HDInsight под управлением Linux. Например, **mycluster-ssh.azurehdinsight.net**.

	* **Динамическая** — включает динамическую маршрутизацию прокси-сервера SOCKS.

	![изображение параметров туннелирования](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Щелкните **Добавить**, чтобы добавить параметры, а затем щелкните **Открыть**, чтобы открыть подключение SSH.

5. При появлении запроса войдите на сервер. При этом будет установлен сеанс SSH и включен туннель.

### Использование туннеля из браузера

> [AZURE.NOTE] Для действий, описанных в этом разделе, используется браузер FireFox, так как он свободно доступен для систем Linux, Unix, Macintosh OS X и Windows. Другие современные браузеры, такие как Google Chrome, Microsoft Edge или Apple Safari, также должны работать, однако подключаемый модуль FoxyProxy, используемый в некоторых действиях, может быть недоступен для некоторых браузеров.

1. Настройте браузер для использования **localhost:9876** в качестве прокси-сервера **SOCKS v5**. Вот как выглядят параметры Firefox. Если используется порт, отличный от 9876, измените номер порта соответствующим образом.

	![изображение параметров Firefox](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] Если выбрать параметр **Удаленная служба DNS**, то запросы DNS будут разрешаться с помощью кластера HDInsight. Если этот параметр не выбран, запросы DNS будут разрешаться локально.

2. Чтобы проверить, что трафик направляется через туннель, зайдите на сайт [http://www.whatismyip.com/](http://www.whatismyip.com/) при включенных и выключенных параметрах прокси-сервера в Firefox. Если настройки включены, IP-адрес будет относиться к компьютеру в центре обработки данных Microsoft Azure.

### Расширения браузера

Обычно при настройке браузера для использования туннеля вы не хотите направлять через туннель весь трафик. Расширения браузера, такие как [FoxyProxy](http://getfoxyproxy.org/), поддерживают сопоставление шаблонов для запросов URL-адресов (только FoxyProxy Standard или Plus). Это позволяет отправлять через туннель только запросы для определенных URL-адресов.

Если вы установили FoxyProxy Standard, выполните следующие действия, чтобы настроить его для перенаправления через туннель только трафика для HDInsight.

1. Откройте расширение FoxyProxy в браузере. Например, в Firefox щелкните значок FoxyProxy рядом с полем адреса.

	![значок FoxyProxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Щелкните **Добавить новый прокси-сервер**, а затем выберите вкладку **Общие** и введите имя прокси-сервера **HDInsightProxy**.

	![вкладка "Общие" FoxyProxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Выберите вкладку **Сведения о прокси** и заполните следующие поля.

	* **Хост или IP-адрес** — необходимо указать значение localhost, так как мы используем туннель SSH на локальном компьютере.

	* **Порт** — порт, используемый для туннеля SSH.

	* **Прокси-сервер SOCKS** — выберите этот параметр, чтобы позволить браузеру использовать туннель в качестве прокси-сервера.

	* **SOCKS v5** — выберите этот параметр, чтобы задать требуемую версию прокси-сервера.

	![прокси-сервер FoxyProxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Выберите вкладку **Шаблоны URL-адреса**, а затем нажмите кнопку **Добавить новый шаблон**. Используйте следующие значения параметров, чтобы определить шаблон, а затем нажмите кнопку **ОК**:

	* **Имя шаблона** — **zeppelinnotebook**. Это просто понятное имя для шаблона.

	* **Шаблон URL-адреса** — ***hn0***. Определяет шаблон, который соответствует внутреннему полному доменному имени конечной точки размещения записных книжек Zeppelin. Поскольку записные книжки Zeppelin доступны только в узле headnode0 кластера, а конечной точкой обычно является `http://hn0-<string>.internal.cloudapp.net`, использование шаблона **hn0** обеспечит переадресацию запроса в конечную точку Zeppelin.

		![шаблон FoxyProxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Нажмите кнопку **ОК**, чтобы добавить прокси-сервер и закрыть окно **Параметры прокси-сервера**.

5. В верхней части диалогового окна FoxyProxy в раскрывающемся списке **Выбрать режим** выберите пункт **Использовать прокси-серверы на основе шаблонов и приоритетов**, а затем нажмите кнопку **Закрыть**.

	![выбор режима FoxyProxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

После выполнения этих действий только запросы URL-адресов, которые содержат строку __internal.cloudapp.net__, будут направляться через туннель SSL.

## Доступ к записной книжке Zeppelin

Настроив туннелирование SSH, вы можете получить доступ к записной книжке Zeppelin в кластере Spark, выполнив описанные ниже действия.

1. В веб-браузере откройте следующую конечную точку:

		http://hn0-myspar:9995

	* **hn0** обозначает headnode0.
	* **myspar** — это первые шесть букв имени кластера Spark.
	* **9995** — порт для доступа к записной книжке Zeppelin.

2. Создайте новую записную книжку. На панели заголовка щелкните элемент **Notebook**, а затем — **Создать новую заметку**.

	![Создание новой записной книжки Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Создание новой записной книжки Zeppelin")

	На той же странице под заголовком **Notebook** отобразится новая записная книжка с именем, начинающимся с **Заметка XXXXXXXXX**. Щелкните новую записную книжку.

3. На веб-странице для новой записной книжки щелкните заголовок и при необходимости переименуйте записную книжку. Нажмите клавишу ВВОД, чтобы сохранить изменения. Кроме того, убедитесь, что в правом верхнем углу в заголовке записной книжки отображается состояние **Подключено**.

	![Состояния записной книжки Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Состояния записной книжки Zeppelin")

4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\\HdiSamples\\SensorSampleData\\hvac**.

	В пустой абзац, созданный по умолчанию в новой записной книжке, вставьте следующий фрагмент кода.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Нажмите клавиши **SHIFT + ВВОД** или кнопку **Воспроизвести** для абзаца, чтобы выполнить фрагмент кода. Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные отображаются в нижней части того же абзаца. Снимок экрана выглядит следующим образом:

	![Создание временной таблицы из необработанных данных](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Создание временной таблицы из необработанных данных")

	Можно указать заголовок для каждого абзаца. В правом верхнем углу экрана щелкните значок **Параметры**, а затем щелкните элемент **Показать заголовок**.

5. Теперь вы можете выполнить инструкции Spark SQL для таблицы **hvac**. Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. Нажмите **SHIFT + ВВОД**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	Инструкция **% sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Spark SQL. Просмотреть определенные интерпретаторы вы можете на вкладке **Интерпретатор** в заголовке записной книжки.

	Выходные данные показаны на снимке экрана ниже.

	![Выполнение инструкции Spark SQL с помощью записной книжки](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Выполнение инструкции Spark SQL с помощью записной книжки")

	 Используйте параметры отображения (выделены красным прямоугольником) для переключения между различными представлениями одних и тех же выходных данных. Щелкните элемент **Параметры**, чтобы определить ключи и значения в выходных данных. На снимке экрана выше в параметр **buildingID** используется в качестве ключа, а среднее значение **temp\_diff** — как значение.

	
6. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. В следующем фрагменте кода показано, как определить переменную (**Temp**) в запросе с возможными значениями, с которыми необходимо выполнить запрос. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Выходные данные показаны на снимке экрана ниже.

	![Выполнение инструкции Spark SQL с помощью записной книжки](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Выполнение инструкции Spark SQL с помощью записной книжки")

	Для последующих запросов можно выбрать новое значение из раскрывающегося списка и повторно выполнить запрос. Щелкните **Settings** (Параметры), чтобы определить ключ и значения в выходных данных. На снимке экрана выше параметр **buildingID** используется в качестве ключа, среднее значение **temp\_diff** используется как значение, а **targettemp** — как группа.

7. Перезапустите интерпретатор Spark SQL, чтобы выйти из приложения. Перейдите на вкладку **Интерпретатор** в верхней части окна, а затем для интерпретатора Spark нажмите кнопку **Перезапустить**.

	![Перезапуск интерпретатора Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Перезапуск интерпретатора Zeppelin")


## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->