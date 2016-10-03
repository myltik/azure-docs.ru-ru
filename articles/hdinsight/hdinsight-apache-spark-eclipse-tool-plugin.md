 <properties
	pageTitle="Создание приложений Spark Scala с помощью средств HDInsight в наборе средств Azure для Eclipse | Microsoft Azure"
	description="Узнайте, как создать автономное приложение Spark для работы в кластерах HDInsight Spark"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="nitinme"/>


# Создание приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для Eclipse

В этой статье приводятся пошаговые инструкции по разработке приложений Spark, написанных на языке Scala, и их отправке в кластер HDInsight Spark с помощью средств HDInsight в наборе средств Azure для Eclipse. Эти средства можно использовать различным образом:

* Для разработки и отправки приложений Scala Spark в кластер HDInsight Spark.
* Для доступа к ресурсам кластера Azure HDInsight Spark.
* Для разработки и локального запуска приложений Scala Spark.

>[AZURE.IMPORTANT] Это средство позволяет создавать и отправлять приложения только в кластер HDInsight Spark на Linux.


##Предварительные требования

* Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* Комплект SDK Java для Oracle версии 7 и 8.
	* **Пакет SDK для Java 7** используется для компиляции проектов Spark в качестве кластеров HDInsight, поддерживающих Java версии 7. Вы можете скачать пакет SDK для Java 7 [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
	* **Пакет SDK для Java 8** используется для среды выполнения интегрированной среды разработки Eclipse. Скачать эту версию можно [здесь](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Интегрированная среда разработки Eclipse. В этой статье используется Eclipse Neon. Его можно установить [отсюда](https://www.eclipse.org/downloads/).

* Интегрированная среда разработки Scala для Eclipse.
	* **Если у вас установлена интегрированная среда разработки Eclipse**, то можно добавить подключаемый модуль интегрированной среды разработки Scala, выбрав **Help** (Справка) > **Install New SoftWare** (Установить новое программное обеспечение) и добавив [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) в качестве источника для скачивания подключаемого модуля Scala для Eclipse.
	* **Если у вас не установлена интегрированная среда разработки Eclipse**, то ее можно установить непосредственно [отсюда](http://scala-ide.org/download/sdk.html). Можно скачать ZIP-файл по этой ссылке, извлечь его содержимое, перейти в папку **/eclipse** и выполнить файл **eclipse.exe**.
	
	>[AZURE.NOTE] В этом документе описаны действия для интегрированной среды разработки Eclipse с установленным подключаемым модулем Scala.

* Пакет SDK для Spark. Скачать эту версию можно [здесь](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

* Установите e(fx)clipse, перейдя на страницу [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html).


## Установка средств HDInsight в наборе средств Azure для Eclipse

Средства HDInsight для Eclipse доступны в составе набора средств Azure для Eclipse. Инструкции по установке набора средств Azure см. в разделе [Установка набора средств Azure для Eclipse](../azure-toolkit-for-eclipse-installation.md).

## Вход в подписку Azure

1. Запустите интегрированную среду разработки Eclipse и откройте Azure Explorer. В меню **Window** (Окно) в интегрированной среде разработки выберите пункт **Show View** (Показать представление) и щелкните **Other** (Другие). В открывшемся диалоговом окне разверните **Azure** и щелкните **Azure Explorer**, а затем нажмите кнопку **ОК**.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. В **Azure Explorer** щелкните правой кнопкой мыши узел **Azure**, а затем выберите пункт **Управление подписками**.

3. В диалоговом окне **Управление подписками** щелкните **Вход** и введите свои учетные данные Azure.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. После входа в диалоговом окне **Управление подписками** будут перечислены все подписки Azure, связанные с указанными учетными данными. Нажмите кнопку **Закрыть** в этом диалоговом окне.

5. На вкладке "Azure Explorer" разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## Настройка проекта Spark Scala для кластера HDInsight Spark

1. В рабочей области интегрированной среды разработки Eclipse щелкните **File** (Файл) > **New** (Создать) > **Project** (Проект).

2. В мастере **New Project** (Новый проект) разверните **HDInsight**, выберите **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)) и нажмите кнопку **Далее**.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. В диалоговом окне **New HDInsight Scala Project** (Новый проект Scala HDInsight) введите или выберите значения, как показано на рисунке ниже, после чего нажмите кнопку **Далее**.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* Введите имя проекта.
	* Убедитесь, что в поле **JRE** параметр **Use an execution environment JRE** (Использовать среду выполнения JRE) имеет значение **JavaSE 1.7**.
	* Убедитесь, что для пакета SDK для Spark задано расположение, в которое вы скачали этот пакет SDK. Ссылка для скачивания указана в разделе [Предварительные требования](#prerequisites) ранее в этой статье. Можно также скачать пакет SDK по ссылке в этом диалоговом окне, как показано на рисунке выше.

4. В следующем диалоговом щелкните вкладку **Libraries** (Библиотеки) вкладку, а затем дважды щелкните **JRE System Library [JavaSE-1.7]** (Системная библиотека JRE [JavaSE 1.7]).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. В диалоговом окне **Edit Library** (Изменение библиотеки) убедитесь, что параметр **Execution Environment** (Среда выполнения) имеет значение **JavaSE-1.7(jdk1.7.0\_79)**. Если этот параметр недоступен, то выполните следующие действия.

	1. Выберите параметр **Alternate JRE** (Альтернативная среда JRE) и проверьте, доступен ли пункт **JavaSE-1.7(jdk1.7.0\_79)**.
	2. Если нет, то нажмите кнопку **Installed JREs** (Установленные среды JRE).

		  ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. В диалоговом окне **Installed JREs** (Установленные среды JRE) нажмите кнопку **Добавить**.

		  ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)

	4. В диалоговом окне **JRE Type** (Тип JRE) выберите **Standard VM** (Стандартная виртуальная машина) и нажмите кнопку **Далее**.

		  ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. В диалоговом окне **JRE Definition** (Определение JRE) щелкните "Directory" (Каталог) и перейдите в папку для установки JDK 7, затем выберите корневую папку для **jdk1.7.0\_79**.

		  ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)

	6. Нажмите кнопку **Готово** В диалоговом окне **Installed JREs** (Установленные среды JRE) выберите только что добавленную среду JRE и нажмите кнопку **ОК**.

		   ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

	7. Добавленная среда JRE должна появиться в списке значений параметра **Execution Environment** (Среда выполнения). Нажмите кнопку **Finish** (Готово).

	  	   ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. На вкладке **Libraries** (Библиотеки) дважды щелкните **Scala Library Container[2.11.8]** (Контейнер библиотек Scala [2.11.8]). В диалоговом окне **Edit Library** (Изменение библиотеки) выберите **Fixed Scala Library container:2.10.6** (Фиксированный контейнер библиотек Scala: 2.10.6).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

	Нажимайте кнопки **Finish** (Готово), пока не покинете диалоговое окно параметров проекта.

## Создание приложения Scala для кластера HDInsight Spark

1. В уже открытой интегрированной среде разработки Eclipse в **обозревателе пакетов** разверните созданный ранее проект, щелкните правой кнопкой **src**, выберите **New** (Создать) и щелкните **Other** (Другое).

2. В диалоговом окне **Select a wizard** (Выбор мастера) разверните **Scala Wizards** (Мастера Scala), щелкните **Scala Object** (Объект Scala) и нажмите кнопку **Далее**.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. В диалоговом окне **Create New File** (Создание файла) введите имя объекта и нажмите кнопку **Finish** (Готово).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. Вставьте следующий код в текстовый редактор.

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
	
		object MyClusterApp{
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("MyClusterApp")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasbs:///HVACOut")
		  }		
		}


5. Запустите приложение в кластере HDInsight Spark.

	1. В **обозревателе пакетов** щелкните имя проекта правой кнопкой мыши и выберите пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

	2. В диалоговом окне **Spark Submission** (Отправка в Spark) введите следующие значения.

		* В поле **Cluster Name** (Имя кластера) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.

		* Необходимо выбрать артефакт из проекта Eclipse или с жесткого диска.

		* В текстовом поле **Main class name** (Имя класса Main) введите имя объекта, указанного в коде (см. рисунок ниже).

			![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

		* Поскольку для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять.

		* Нажмите кнопку **Submit** (Отправить).

	3. На вкладке **Spark Submission** (Отправка в Spark) начнет отображаться ход выполнения. Вы можете остановить приложение, щелкнув красную кнопку в окне "Spark Submission" (Отправка в Spark). Можно также просмотреть журналы для данного запуска приложения, щелкнув значок глобуса (обозначен на рисунке синей рамкой).

        ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    В следующем разделе вы узнаете, как получить доступ к выходным данным задачи, используя средства HDInsight в наборе средств Azure для Eclipse.


## Доступ к кластерам HDInsight Spark и управление ими с помощью средств HDInsight в наборе средств Azure для Eclipse

С помощью средств HDInsight можно выполнять различные операции.

### Доступ к контейнеру хранилища для кластера

1. В Azure Explorer разверните корневой узел **HDInsight**, чтобы увидеть список доступных кластеров HDInsight Spark.

3. Разверните имя кластера, чтобы увидеть учетную запись хранилища и контейнер хранилища по умолчанию для кластера.

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. Щелкните имя связанного с кластером контейнера хранилища. В правой области появится папка с именем **HVACOut**. Дважды щелкнув эту папку, вы увидите файлы **part-***. Откройте один из этих файлов для просмотра выходных данных приложения.

### Доступ к серверу журнала Spark

1. В **Azure Explorer** щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

2. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Следовательно, приложение Spark называлось **MyClusterApp**.

### Запуск портала Ambari

В **Azure Explorer** щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

### Управление подписками Azure

По умолчанию средства HDInsight в наборе средств Azure для Eclipse содержат список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, кластеры из которых вас интересуют. В **Azure Explorer** щелкните правой кнопкой мыши корневой узел **Azure**, а затем выберите пункт **Управление подписками**. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и нажмите кнопку **Закрыть**. Если вы хотите выйти из своей подписки Azure, нажмите кнопку **Выйти**.


## Запуск приложения Spark Scala на локальном компьютере

Средства HDInsight в наборе средств Azure для Eclipse позволяют запускать приложения Spark Scala локально на рабочей станции. Как правило, такие приложения не требуют доступа к ресурсам кластера, таким как контейнер хранилища, и могут запускаться и тестироваться локально.

### Предварительные требования

При запуске локального приложения Spark Scala на компьютере под управлением Windows может возникнуть исключение, описанное в [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) и связанное с отсутствием в Windows файла **WinUtils.exe**. Чтобы обойти эту ошибку, [скачайте этот исполняемый файл отсюда](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например, в папку **C:\\WinUtils\\bin**. После этого добавьте переменную среды **HADOOP\_HOME** и присвойте ей значение **C\\WinUtils**.

### Запуск локального приложения Spark Scala	 

1. Запустите Eclipse и создайте новый проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next** («Далее»).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* В левой области выберите **HDInsight**.
	* В правой области выберите **Spark on HDInsight Local Run Sample (Scala)** (Пример локального запуска Spark в HDInsight (Scala)).
	* Нажмите кнопку **Далее**.

2. Чтобы предоставить сведения о проекте, выполните шаги 3–6, как описано в предыдущем разделе [Настройка проекта приложения Spark Scala для кластера HDInsight Spark](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark кластера).

3. Шаблон добавляет пример кода (**LogQuery**) в папку **src**, который можно запустить локально на компьютере.

	![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  Щелкните правой кнопкой мыши приложение **LogQuery**, выберите пункт **Run As** (Запустить от имени) и щелкните **1 Scala Application** (1. Приложение Scala). В нижней части вкладки **Console** (Консоль) вы увидите выходные данные следующего вида.

	![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## Отзывы и известные проблемы

В настоящее время просмотр выходных данных Spark напрямую не поддерживается, и мы работаем над этим.

С любыми отзывами и предложениями (а также в случае возникновения проблем при работе с этим инструментом) обращайтесь по электронному адресу hdivstool@microsoft.com.

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

* [Использование средств HDInsight в наборе средств Azure для IntelliJ для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->