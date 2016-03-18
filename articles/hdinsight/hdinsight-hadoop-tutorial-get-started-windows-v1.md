<properties
   pageTitle="Учебник по Hadoop: начало работы с Hadoop в Windows"
   description="Начало работы с Hadoop в HDInsight. Узнайте, как подготовить кластеры Hadoop в Windows, выполните запрос Hive на данных и проанализируйте выходные данные в Excel."
   keywords="руководство по hadoop, hadoop в windows, кластер hadoop, изучение hadoop, запрос hive"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/25/2016"
   ms.author="nitinme"/>


# Учебник по Hadoop: начало работы с Hadoop и запросом Hive в HDInsight в Windows

> [AZURE.SELECTOR]
- [Windows](../hdinsight-hadoop-tutorial-get-started-windows-v1.md)
- [Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)

Чтобы вы смогли понять, что такое Hadoop в Windows, и начать работу с HDInsight, в этом учебнике показано, как выполнять запрос Hive на неструктурированных данных в кластере Hadoop, а затем анализировать результаты в Microsoft Excel.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Начало работы с Hadoop в HDInsight под управлением Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

## Чем полезен этот учебник по Hadoop?

Предположим, у вас есть большой набор неструктурированных данных, и необходимо выполнить запрос Hive для извлечения из них значимой информации. Именно этим мы и собираемся заняться в этом учебнике. Вот как это делается:

   !["Учебник по Hadoop: создание учетной записи; подготовка кластера Hadoop; отправка запроса Hive; анализ данных в Excel.][image-hdi-getstarted-flow]

Просмотрите демонстрационный видеоролик из этого учебника, чтобы узнать о кластерах Hadoop для HDInsight:

![Видео первого учебника Hadoop: отправка запроса Hive в кластере Hadoop и анализ результатов в Excel.][img-hdi-getstarted-video]

**[Просмотр учебника по Hadoop для HDInsight на YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**


Наряду с выпуском общедоступной версии Azure HDInsight корпорация Майкрософт выпустила также эмулятор HDInsight для Azure, ранее известный как *Microsoft HDInsight Developer Preview*. Этот продукт предназначен для сценариев разработки и поэтому поддерживает только развертывания на одном узле. Дополнительную информацию об использовании эмулятора HDInsight см. в статье [Приступая к работе с эмулятором HDInsight][hdinsight-emulator].

> [AZURE.NOTE] Инструкции по подготовка кластера HBase см. в разделе [Подготовка кластера HBase в HDInsight][hdinsight-hbase-custom-provision]. Различия баз данных, учитываемые при их выборе, см. в статье <a href="http://go.microsoft.com/fwlink/?LinkId=510237">В чем разница между Hadoop и HBase?</a>.

## Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:


- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Рабочая станция** с Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

**Предполагаемое время выполнения заданий этого учебника:** 30 минут.



##<a name="storage"></a>Создание учетной записи хранения Azure

При подготовке кластера Hadoop в HDInsight нужно указать учетную запись хранения Azure. Конкретный контейнер хранилища BLOB-объектов из этой учетной записи назначается в качестве файловой системы по умолчанию, так же как и в распределенной файловой системе Hadoop (HDFS). По умолчанию подготовка кластера HDInsight выполняется в том же центре обработки данных, в котором находится указанная учетная запись хранения. Дополнительные сведения см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]

>[AZURE.NOTE] Не используйте контейнер хранилища BLOB-объектов по умолчанию для нескольких кластеров Hadoop.

Кроме этой учетной записи хранения в процессе настройки кластера можно добавить дополнительные учетные записи хранения. Эти дополнительные учетные записи хранения могут быть из той же подписки Azure или из других подписок Azure. Указания см. в разделе [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision].

В этом учебнике используются только BLOB-объект и учетная запись хранения по умолчанию.

**Создание учетной записи хранения Azure**

1. Войдите на [классический портал Azure][azure-management-portal].
2. Щелкните **СОЗДАТЬ** в левом нижнем углу и введите значения, как показано на рисунке.

	![Классический портал Azure, на котором можно настроить новую учетную запись хранения с помощью функции быстрого создания.][image-hdi-storageaccount-quickcreate]

>[AZURE.NOTE]  Создавайте учетную запись хранения в том расположении, в котором поддерживается кластер, а именно: **Восточная Азия**, **Юго-Восточная Азия**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

Выберите новую учетную запись хранения в списке и щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы. Запишите значения в полях **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА** (или **ВТОРИЧНЫЙ КЛЮЧ ДОСТУПА** —любой из ключей будет работать). Они потребуются позже в этом учебнике. Дополнительные сведения см. в статье [Создание учетной записи хранения][azure-create-storageaccount].

##<a name="provision"></a>Подготовка кластера Hadoop

При подготовке кластера происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. В этом разделе подготавливается кластер HDInsight версии 3.1 на базе Hadoop версии 2.4. Кроме этого, можно создать кластеры Hadoop для других версий, используя классический портал Azure, командлеты HDInsight PowerShell или пакет SDK для HDInsight .NET. Указания см. в разделе [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision]. Дополнительную информацию о различных версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Версии компонентов НDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


**Подготовка кластера Hadoop**

1. Войдите на [классический портал Azure][azure-management-portal].

2. Щелкните **СОЗДАТЬ** в левом нижнем углу и введите значения, как показано на рисунке.

	![Создание кластера Hadoop в HDInsight.][image-hdi-quickcreatecluster]

<!-- COMMENTED OUT TEXT BEGINS --

4. Enter or select the following values:


	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Cluster Name</td><td>Name of the cluster.</td></tr>
	<tr><td>Cluster Size</td><td>Number of data nodes you want to deploy. The default value is 4. But the option to use 1 or 2 data nodes is also available from the drop-down list. Any number of cluster nodes can be specified by using the <strong>Custom Create</strong> option. Pricing details about the billing rates for various cluster sizes are available. Click the <strong>?</strong> symbol above the drop-down list and follow the link that appears.</td></tr>
	<tr><td>Password</td><td>The password for the <i>admin</i> account. The cluster user name "admin" is specified when you are not using the <strong>Custom Create</strong> option. Note that this is NOT the Windows Administrator account for the VMs on which the clusters are provisioned. The account name can be changed by using the <strong>Custom Create</strong> wizard.</td></tr>
	<tr><td>Storage Account</td><td>Click the drop-down list, and select the storage account that you created. <br/>

	When a storage account is chosen, it cannot be changed. If the storage account is removed, the cluster will no longer be available for use.

	The HDInsight cluster is located in the same datacenter as the storage account.
	</td></tr>
	</table>

	Keep a copy of the cluster name. You will need it later in the tutorial.


5. Click **Create HDInsight Cluster**. When the provisioning completes, the  status column shows **Running**.

-- COMMENTED OUT TEXT ENDS -->

>[AZURE.NOTE] Это шаги для подготовки кластера HDInsight версии 3.1. Чтобы создать кластер с другой версией, используйте метод **Настраиваемое создание** на портале или Azure PowerShell. Информацию о различиях между версиями кластеров см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight][hdinsight-versions]. Дополнительную информацию об использовании параметра **НАСТРАИВАЕМОЕ СОЗДАНИЕ** см. в статье [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision].


##<a name="sample"></a>Запуск демонстрационных данных из портала

Успешно подготовленный кластер HDInsight предоставляет консоль запросов, содержащую коллекцию материалов для начала работы, которая предназначена для запуска примеров непосредственно с портала. Образцы могут использоваться для обучения работе с HDInsight путем пошагового прохождения некоторых базовых сценариев. Эти образцы поставляются вместе со всеми необходимыми компонентами, такими как данные для анализа и запросы для их запуска на основе этих данных. Дополнительную информацию о примерах в коллекции материалов для начала работы см. в статье [Изучение Hadoop с помощью коллекции материалов для начала работы с HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Чтобы запустить пример** с классического портала Azure, щелкните имя кластера, в котором необходимо запустить пример, а затем нажмите кнопку **Консоль запросов** в нижней части страницы. На открывшейся веб-странице выберите вкладку **Коллекция материалов для начала работы**, а затем в категории **Примеры** щелкните пример, который необходимо запустить. Следуйте указаниям на веб-странице, чтобы завершить выполнение примера. В следующей таблице содержится несколько примеров, а также дополнительная информация о функциях каждого из них.

Образец | Функция
------ | ---------------
[Анализ данных датчика][hdinsight-sensor-data-sample] | С помощью этого примера можно узнать об использовании HDInsight для обработки исторических данных, полученных системами отопления, вентиляции и кондиционирования воздуха, чтобы определить системы, которые не в состоянии надежно поддерживать заданную температуру.
[Анализ журнала веб-сайта][hdinsight-weblogs-sample] | С помощью этого примера можно узнать об использовании HDInsight для анализа файлов журнала веб-сайта, позволяющего получить представление о дневной частоте посещений веб-сайта с внешних сайтов, а также сводку об ошибках веб-сайта, с которыми столкнулись пользователи.
[Анализ тенденций Twitter](hdinsight-analyze-twitter-data.md) | С помощью этого примера можно узнать об использовании HDInsight для анализа тенденций в Twitter.



##<a name="hivequery"></a>Запуск запроса HIVE из портала
Теперь, когда у вас есть подготовленный кластер HDInsight, на следующем шаге выполняется задание Hive для запроса примера таблицы Hive. Мы используем таблицу *hivesampletable*, которая поставляется с кластерами HDInsight. Эта таблица содержит данные о производителях мобильных устройств, платформах и моделях. Запрос Hive в этой таблице извлекает данные для мобильных устройств определенного производителя.

> [AZURE.NOTE] Инструменты HDInsight для Visual Studio поставляются с пакетом SDK для Azure для .NET версии 2.5 или более поздней. Используя инструменты Visual Studio, вы можете подключить кластер HDInsight, создать таблицы Hive и выполнить Hive-запросы. Дополнительную информацию см. в статье [Начало работы со средствами HDInsight Hadoop для Visual Studio][1].

**Выполнение задания Hive с панели мониторинга кластера**

1. Войдите на [классический портал Azure][azure-management-portal].
2. На левой панели щелкните **HDINSIGHT**. Вы увидите список кластеров, включая кластер, который вы только что создали в предыдущем разделе.
3. Щелкните имя кластера, в котором необходимо запустить задание Hive, а затем нажмите кнопку **КОНСОЛЬ ЗАПРОСОВ** в нижней части страницы.
4. В новой вкладке браузера откроется веб-страница. Введите учетную запись и пароль пользователя Hadoop. Имя по умолчанию — **admin**; пароль тот же, который вы ввели при подготовке кластера. Панель мониторинга выглядит следующим образом:

	![Вкладка «Редактор Hive» на панели мониторинга кластера HDInsight.][img-hdi-dashboard]

	В верхней части страницы содержится несколько вкладок: вкладка по умолчанию — **Редактор Hive** и остальные вкладки — **Журнал заданий** и **Браузер файлов**. С помощью панели мониторинга можно отправлять запросы Hive, проверять журналы заданий Hadoop и просматривать файлы в хранилище.

	> [AZURE.NOTE] Обратите внимание, что у веб-страницы следующий URL-адрес: *&lt;имя\_кластера&gt;.azurehdinsight.net*. Таким образом, вместо открывания панели мониторинга на портале, можно открыть ее в веб-браузере, используя этот URL-адрес.

6. На вкладке **Редактор Hive** в поле **Имя запроса**ведите **HTC20**. Имя запроса представляет собой название задания. На панели запросов введите запрос Hive, как показано на рисунке:

	![Запрос, введенный в области запроса на вкладке "Редактор Hive".][img-hdi-dashboard-query-select]

4. Нажмите кнопку **Submit** (Отправить). На получение результатов уходит несколько минут. Экран обновляется каждые 30 секунд. Чтобы обновить экран, можно также нажать кнопку **Обновить**.

    ![Результаты запроса Hive, указанные в нижней части панели инструментов кластера.][img-hdi-dashboard-query-select-result]

5. Когда состояние покажет, что задание завершено, щелкните имя запроса на экране, чтобы просмотреть выходные данные. Отметьте **Время запуска задания (формат UTC)**. Оно понадобится вам позднее.

    ![Строка "Время запуска задания", указанная на вкладке "Журнал заданий" на панели мониторинга кластера HDInsight.][img-hdi-dashboard-query-select-result-output]

    На странице также отображаются **Выходные данные задания** и **Журнал заданий**. Кроме этого, можно также загрузить выходной файл (\_stdout) и файл журнала (\_stderr).


**Переход к выходному файлу**

1. На панели мониторинга кластера щелкните **Браузер файлов**.
2. Последовательно щелкните имя учетной записи, имя контейнера (совпадает с именем кластера) и **пользователь**.
3. Щелкните **admin**, а затем щелкните GUID, время последнего изменения которого немного позднее ранее отмеченного времени запуска задания. Скопируйте этот GUID. Он понадобится в следующем разделе.


   	![GUID выходного файла запроса Hive, указанный на вкладке "Браузер файлов".][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Подключение к средствам бизнес-аналитики Microsoft для Excel

С помощью надстройки Power Query для Microsoft Excel можно импортировать выходные данные задания из HDInsight в Excel, где для дальнейшего анализа результатов могут использоваться инструменты бизнес-аналитики Microsoft.

Для выполнения действий из этой части учебника требуется Excel 2013 или Excel 2010.

**Скачивание Microsoft Power Query для Excel**

- Загрузите Microsoft Power Query для Excel из [Центра загрузки Майкрософт](http://www.microsoft.com/download/details.aspx?id=39379) и установите.

**Импорт данных HDInsight**

1. Откройте Excel и создайте новую книгу.
3. Щелкните меню **Power Query**, выберите **Из других источников**, а затем щелкните **Из Microsoft Azure HDInsight**.

	![Меню "Импорт" в Power Query для Excel, открытое для Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. В поле **Имя учетной записи** введите имя учетной записи хранилища больших двоичных объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. (Это учетная запись хранения, которую вы ранее создали в этом учебнике.)
4. В поле **Ключ учетной записи** введите ключ для учетной записи хранилища больших двоичных объектов Azure, а затем нажмите кнопку **Сохранить**.
5. В области справа дважды щелкните имя большого двоичного объекта. По умолчанию имя большого двоичного объекта совпадает с именем кластера.

6. Найдите **stdout** в столбце **Имя**. Проверьте, чтобы GUID в соответствующем столбце **Путь к папке** совпадал с GUID, который вы скопировали ранее. Совпадение предполагает, что выходные данные соответствуют отправленному заданию. Щелкните **Двоичный** в столбце слева от **stdout**.

	![Поиск выходных данных в списке содержимого по GUID.][image-hdi-gettingstarted-powerquery-importdata2]

9. В верхнем левом углу щелкните **Закрыть и загрузить** для импорта результатов задания Hive в Excel.


##<a name="nextsteps"></a>Дальнейшие действия
Из этого учебника Hadoop вы узнали, как подготовить кластер Hadoop в Windows с помощью HDInsight, запустить на данных запрос Hive и импортировать результаты в Excel для дальнейшей обработки и графического отображения с использованием инструментов бизнес-аналитики. Дополнительную информацию см. в следующих учебниках:

- [Приступая к работе с инструментами HDInsight Hadoop для Visual Studio][1]
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование MapReduce с HDInsight][hdinsight-use-mapreduce]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Использование Oozie с HDInsight][hdinsight-use-oozie]
- [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#setup-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!----HONumber=AcomDC_0302_2016-->