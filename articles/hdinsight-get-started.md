<properties 
	pageTitle="Приступая к работе с Hadoop с использованием Hive в HDInsight - Azure" 
	description="Приступите к работе с Hadoop в HDInsight, решением для обработки данных большого размера в облаке. Узнайте, как подготавливать кластеры, запрашивать данные с помощью Hive и выводить данные в Excel для анализа." 
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
	ms.date="10/14/2014" 
	ms.author="nitinme"/>


# Приступая к работе с Hadoop с использованием Hive в HDInsight для анализа использования мобильного телефона

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

Чтобы помочь вам быстро начать работу с HDInsight, в этом учебнике показано, как выполнить запрос Hive для извлечения полезной информации из неструктурированных данных в кластере Hadoop. Затем вы сможете проанализировать результаты в Microsoft Excel.


> [AZURE.NOTE] Если вы не знакомы с Hadoop и программами обработки данных большого размера, ознакомьтесь с такими терминами, как [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] и [Hive][apache-hive]. Чтобы разобраться, как HDInsight поддерживает Hadoop в Azure, см. раздел [Введение для Hadoop в HDInsight][hadoop-hdinsight-intro].

Наряду с выпуском общедоступной версии Azure HDInsight корпорация Майкрософт выпустила также эмулятор HDInsight для Azure, ранее известный как *Microsoft HDInsight Developer Preview*. Этот продукт предназначен для сценариев разработки и поэтому поддерживает только развертывания на одном узле. Информацию об использовании эмулятора HDInsight см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator].

> [AZURE.NOTE] Указания по подготовке кластера HBase см. в разделе [Подготовка кластера HBase в HDInsight][hdinsight-hbase-custom-provision]. См. раздел <a href="http://go.microsoft.com/fwlink/?LinkId=510237">В чем заключается разница между Hadoop и HBase?</a> чтобы понять, какой следует сделать выбор.   

## Цели учебника ##

Предположим, что у вас есть большой набор неструктурированных данных, и необходимо выполнить запросы для извлечения из него содержательной информации. Именно этим мы и собираемся заняться в этом учебнике. Вот как это делается:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

Вы также можете посмотреть видео этого учебника:

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**Предварительные требования:**

Перед началом работы с этим учебником необходимо иметь следующее:


- Подписка Azure. Дополнительные сведения о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
- Компьютер с Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

**Предполагаемое время выполнения:** 30 минут

##В этом учебнике рассматриваются следующие темы:

* [Создание учетной записи хранения Azure](#storage)
* [Подготовка кластера HDInsight](#provision)
* [Запуск образцов из портала](#sample)
* [Запуск задания HIVE](#hivequery)
* [Дальнейшие действия](#nextsteps)

##<a name="storage"></a>Создание учетной записи хранения Azure

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Оно называется *WASB* или *Azure Storage - Blob*. WASB - это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

При подготовке кластера HDInsight указывается учетная запись хранения Azure Storage. Конкретный контейнер хранилища BLOB-объектов из этой учетной записи назначается в качестве файловой системы по умолчанию, так же как и в HDFS. По умолчанию подготовка кластера HDInsight выполняется в том же центре обработки данных, в котором находится указанная учетная запись хранения.

Помимо этой учетной записи хранения в процессе настройки кластера HDInsight можно добавить дополнительные учетные записи хранения. Эти дополнительные учетные записи хранения могут быть из той же подписки Azure или из других подписок Azure. Указания см. в разделе [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision]. 

Чтобы упростить этот учебник, используются только контейнер BLOB-объектов и учетная запись хранения по умолчанию. На практике файлы данных обыкновенно хранятся в заданной учетной записи хранения.

**Создание учетной записи хранения Azure**

1. Войдите на [портал управления Azure][azure-management-portal].
2. Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРО СОЗДАТЬ**.

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. Введите сведения в поля **URL-АДРЕС**, **РАСПОЛОЖЕНИЕ** и **РЕПЛИКАЦИЯ**, а затем щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**. Территориальные группы не поддерживаются. Вы увидите новую учетную запись хранения в списке хранилищ.

	>[AZURE.NOTE]  Если для подготовки кластера HDInsight используется параметр "Быстрое создание", как в этом учебнике, то расположение при подготовке кластера не указывается. Вместо этого по умолчанию кластер располагается в том же центре обработки данных, что и учетная запись хранения. Таким образом, необходимо создать учетную запись хранения в тех местах, в которых поддерживается кластер, а именно:  **Восточная Азия**, **Юго-Восточная Азия**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

4. Дождитесь, пока **СОСТОЯНИЕ** новой учетной записи хранения не изменится на **В сети**.
5. Выберите новую учетную запись хранения в списке и щелкните **УПР. КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
7. Запишите значение параметров **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА** (или **ВТОРИЧНЫЙ КЛЮЧ ДОСТУПА**. Любой из ключей будет работать).  Они потребуются позже в данном руководстве.


Дополнительную информацию см. в разделах
[Создание учетной записи хранения][azure-create-storageaccount] и [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Подготовка кластера HDInsight

При подготовке кластера HDInsight выполняется подготовка вычислительных ресурсов Azure, которые содержат Hadoop и соответствующие приложения. В этом разделе подготавливается кластер HDInsight версии 3.1 на базе Hadoop версии 2.4. Также можно создать кластеры Hadoop и для других версий, использующих портал Azure, командлеты HDInsight PowerShell или пакет SDK HDInsight .NET. Указания см. в разделе [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision]. Для получения дополнительной информации о различных версиях HDInsight и их соглашениях SLA см. страницу [Версии компонентов НDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Подготовка кластера HDInsight к работе** 

1. Войдите на [портал управления Azure][azure-management-portal]. 

2. Щелкните **HDInsight** слева, чтобы просмотреть состояние кластеров в вашей учетной записи. На следующем снимке экрана кластеры HDInsight отсутствуют.

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. Щелкните **СОЗДАТЬ** в левой нижней части, а затем последовательно выберите **Службы данных**, **HDInsight** и **Hadoop**.

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. Введите или выберите следующие значения:

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Имя кластера,</td><td>Имя кластера</td></tr>
	<tr><td>Размер кластера</td><td>Число узлов данных, которые требуется развернуть. Значение по умолчанию - 4. Но кластеры с 1 или 2 узлами данных также доступны в раскрывающемся списке. Можно указать любое количество узлов кластера <strong>с помощью параметра "Настраиваемое создание"</strong> . Доступны сведения о стоимости использования для различных размеров кластеров. Щелкните символ <strong>?</strong> над раскрывающимся списком и перейдите по ссылке во всплывающей подсказке.</td></tr>
	<tr><td>Пароль</td><td>Пароль для <i>admin</i> учетной записи. Если не используется параметр "Настраиваемое создание", для имени пользователя кластера указывается <strong>admin</strong> . Обратите внимание, что это НЕ учетная запись администратора Windows для ВМ, на которых происходит подготовка кластеров. Имя учетной записи можно изменить с помощью мастера <strong>Настраиваемое создание</strong> мастера.</td></tr>
	<tr><td>Учетная запись хранения</td><td>Выберите созданную вами учетную запись хранения в раскрывающемся списке. <br/>

	После выбора учетной записи хранения изменить ее невозможно. Если удалить учетную запись хранения, кластер будет недоступен для использования.

	Кластер HDInsight размещается в том же центре обработки данных, что и учетная запись хранения. 
	</td></tr>
	</table>

	Сохраните копию имени кластер.  Они потребуются позже в данном руководстве.

	
5. Щелкните **Создать кластер HDInsight**. После завершения процесса подготовки в столбце состояния будет отображаться значение **Работает**.

	>[AZURE.NOTE] В результате указанной выше процедуры создается кластер HDInsight версии 3.1. Чтобы создать другие версии кластеров, используйте метод настраиваемого создания на портале управления или Azure PowerShell. Дополнительную информацию о различиях между версиями кластеров см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][hdinsight-versions]. Для получения дополнительной информации о параметре **НАСТРАИВАЕМОЕ СОЗДАНИЕ** см. раздел [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision].


##<a name="sample"></a>Запуск образцов из портала

Успешно подготовленный кластер HDInsight предоставляет консоль запросов, предназначенную для запуска образцов непосредственно из портала. Образцы могут использоваться для обучения работе с HDInsight путем пошагового прохождения некоторых базовых сценариев. Эти образцы поставляются вместе со всеми необходимыми компонентами, такими как данные для анализа и запросы для их запуска на основе этих данных. 

**Чтобы запустить образец** из портала управления Azure, щелкните имя кластера, в котором необходимо запустить образцы, а затем нажмите кнопку **Консоль запросов** в нижней части страницы. На открывшейся веб-странице выберите вкладку **Начало работы с коллекцией**, а затем в категории **Образцы** щелкните образец, который необходимо запустить. Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец. Чтобы узнать больше о функциях каждого образца, щелкните указанные ниже ссылки.

Образец | Функции
------ | ---------------
[Анализ данных датчика][hdinsight-sensor-data-sample] | Узнайте, как использовать HDInsight для обработки исторических данных, полученных системами отопления, вентиляции и кондиционирования воздуха, чтобы определить системы, которые не в состоянии надежно поддерживать заданную температуру.
[Анализ журнала веб-сайта][hdinsight-weblogs-sample] | Узнайте, как использовать HDInsight для анализа файлов журнала веб-сайта, чтобы получить представление о дневной частоте посещений веб-сайта из внешних сайтов, а также получить сводку об ошибках веб-сайта, с которыми столкнулись пользователи.


##<a name="hivequery"></a>Запуск запроса HIVE из портала
Теперь, когда у вас есть подготовленный кластер HDInsight, на следующем шаге выполняется задание Hive для запроса примера таблицы Hive, *hivesampletable*, которая поставляется вместе с кластерами HDInsight. Эта таблица содержит данные о производителях мобильных устройств, платформах и моделях. Мы запрашиваем таблицу, чтобы извлечь данные для мобильных устройств определенного производителя.

> [AZURE.NOTE] Инструменты HDInsight для Visual Studio поставляются с пакетом SDK для Azure для .NET версии 2.5 или более поздней.  Используя инструменты Visual Studio, вы можете подключить кластер HDInsight, создать таблицы Hive и выполнить Hive-запросы.  Дополнительную информацию см. в разделе [Приступая к работе с инструментами HDInsight Hadoop для Visual Studio][1].

**Выполнение задания Hive с панели мониторинга кластера**

1. Войдите на [портал управления Azure][azure-management-portal]. 
2. На левой панели щелкните **HDINSIGHT**. Вы увидите список созданных кластеров, в том числе кластер, созданный вами в последнем разделе.
3. Щелкните имя кластера, в котором необходимо запустить задание Hive, а затем нажмите кнопку **КОНСОЛЬ ЗАПРОСОВ** в нижней части страницы. 
4. Откроется веб-страница на отдельной вкладке браузера. Введите учетную запись и пароль пользователя Hadoop.  Имя по умолчанию - **admin**; пароль тот же, который вы ввели при подготовке кластера.  Панель мониторинга выглядит следующим образом:

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	В верхней части имеются несколько вкладок.  Вкладка по умолчанию - **Редактор Hive**, остальные вкладки - **Журнал заданий** и **Браузер файлов**.  С помощью панели мониторинга можно отправлять запросы Hive, проверять журналы заданий и просматривать файлы WASB.

	> [AZURE.NOTE] Обратите внимание, что у веб-страницы следующий URL-адрес: *&lt;имя_кластера&gt;.azurehdinsight.net*. Таким образом, вместо открывания панели мониторинга с портала управления, можно открыть ее из веб-браузера, используя этот URL-адрес.

6. На вкладке **Редактор Hive** в поле **Имя запроса** введите **HTC20**.  Имя запроса представляет собой название задания.

7. В панели запросов введите следующий запрос: 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. Нажмите кнопку **Submit** (Отправить). На получение результатов уходит несколько минут. Экран обновляется каждые 30 секунд. Чтобы обновить экран, можно также нажать кнопку **Обновить**.

    После завершения экран будет выглядеть следующим образом:

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. Чтобы увидеть выходные данные, щелкните имя запроса на экране. Запишите значение параметра **Время запуска задания (формат UTC)**. Оно понадобится вам позднее. 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    На странице также отображаются области **Выходные данные задания** и **Журнал заданий**. Кроме этого, можно также скачать выходной файл (\_stdout) и файл журнала \(_stderr).


	> [AZURE.NOTE] Пока открыта вкладка **Редактор Hive**, в таблице **Сеанс задания** на этой вкладке приведены завершенные или выполняемые задания. В таблице не перечисляются задания, если перейти с этой страницы. Таблица **Журнал заданий** выводит список всех завершенных или выполняемых заданий.
 

**Переход к выходному файлу**

1. На панели мониторинга кластера щелкните расположенный в верхней части элемент **Браузер файлов**. 
2. Последовательно щелкните имя учетной записи хранения, имя контейнера (совпадает с именем кластера) и **пользователь**.
3. Щелкните **администратор**, а затем GUID, время последнего изменения которого немного позднее ранее отмеченного времени запуска задания. Запишите этот GUID. Он понадобится в следующем разделе.


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Подключение к средствам бизнес-аналитики Microsoft 

С помощью надстройки Power Query для Microsoft Excel можно импортировать выходные данные задания из HDInsight в Excel, где для дальнейшего анализа результатов могут использоваться средства бизнес-аналитики Microsoft. 

Для выполнения действий из этой части учебника требуется Excel 2010 или Excel 2013. 

**Скачивание Microsoft Power Query для Excel**

- Скачайте надстройку Microsoft Power Query для Microsoft Excel из [Центра загрузки Майкрософт](http://www.microsoft.com/ru-ru/download/details.aspx?id=39379) и установите ее.

**Импорт данных HDInsight**

1. Откройте Excel и создайте новую пустую книгу.
3. Щелкните меню **Power Query** и последовательно щелкните **Из других источников**, **Из Azure HDInsight**.

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Введите **Имя учетной записи** учетной записи хранилища BLOB-объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. Это учетная запись хранения, созданная вами ранее в этом учебнике.
4. Введите **Ключ учетной записи** для учетной записи хранилища BLOB-объектов Azure, а затем нажмите кнопку **Сохранить**. 
5. В области навигатора в правой части окна дважды щелкните имя контейнера хранилища BLOB-объектов. По умолчанию имя контейнера совпадает с именем кластера. 

6. В столбце **Имя** найдите **stdout**. Проверьте, чтобы GUID в соответствующем столбце **Путь к папке** совпадал с GUID, который вы записали ранее. Совпадение предполагает, что выходные данные соответствуют отправленному заданию. Щелкните **Бинарное** слева от **stdout**.

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. Щелкните **Закрыть и загрузить** в верхнем левом углу, чтобы импортировать результаты задания Hive в Excel.


##<a name="nextsteps"></a>Дальнейшие действия
Из этого учебника вы узнали, как подготовить кластер с помощью HDInsight, запустить на нем задание MapReduce и импортировать результаты в Excel для дальнейшей обработки и отображения с использованием средств бизнес-аналитики. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с инструментами HDInsight Hadoop для Visual Studio][1]
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование MapReduce с HDInsight][hdinsight-use-mapreduce]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Использование Oozie с HDInsight][hdinsight-use-oozie]
- [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!--HONumber=42-->
