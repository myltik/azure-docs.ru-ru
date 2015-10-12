<properties 
	pageTitle="Использование концентраторов событий Azure с Apache Spark в HDInsight для обработки потоковых данных | Microsoft Azure" 
	description="Пошаговые инструкции по отправке потока данных в концентратор событий Azure и последующее получение этих событий в Spark с помощью записной книжки Zeppelin" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="nitinme"/>


# Потоковая передача Spark: обработка событий от концентраторов событий Azure с помощью Apache Spark в HDInsight

Потоковая передача Spark расширяет возможности основного API Spark по созданию масштабируемых, отказоустойчивых приложений для обработки потоковых данных с высокой пропускной способностью. Данные могут поступать из множества источников. В этой статье для приема данных используются концентраторы событий. Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду

В этом учебнике вы узнаете, как создать концентратор событий Azure, как организовать прием сообщений в концентратор событий с помощью консольного приложения на языке C#, а также параллельно извлекать их с помощью Zeppelin записной книжки Zeppelin, настроенной для Apache Spark в HDInsight.

> [AZURE.NOTE]Для выполнения инструкций в этой статье необходимо использовать обе версии портала Azure. Концентратор событий будет создаваться на [портале Azure](https://manage.windowsazure.com). Для работы с кластером HDInsight Spark будет использоваться [портал предварительной версии Azure](https://ms.portal.azure.com/).

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark. Инструкции см. в статье [Подготовка кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- [Концентратор событий Azure](service-bus-event-hubs-csharp-ephcs-getstarted.md)
- Рабочая станция, на которой установлено программное обеспечение Microsoft Visual Studio 2013. Инструкции см. в статье [Установка Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Создание концентратора событий Azure

1. На [портале Azure](https://manage.windowsazure.com) выберите **СОЗДАТЬ** > **Service Bus** > **Концентратор событий** > **Настраиваемое создание **.

2. В диалоговом окне **Добавление нового концентратора событий**введите **Имя концентратора событий**, выберите **Регион** для создания в нем концентратора и создайте новое пространство имен или выберите существующее. Щелкните **стрелку** для продолжения.

	![страница мастера 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Создание концентратора событий Azure")

	> [AZURE.NOTE]Для сокращения задержек и затрат в поле **Расположение** следует выбрать то же расположение, в котором находится кластер Apache Spark в HDInsight.

3. В диалоговом окне **Настройка концентратора событий** введите значения в полях **Количество разделов** и **Хранение сообщений**, а затем щелкните значок с флажком. В этом примере числу разделов присвойте значение 10, а хранению сообщений — 1. Запомните количество разделов, поскольку это значение понадобится позже.

	![страница мастера 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Укажите размер раздела и срок хранения в днях для концентратора событий")

4. Щелкните созданный концентратор событий, выберите **Настройка**, а затем создайте две политики доступа для концентратора событий.

	<table>
<tr><th>Имя</th><th>Разрешения</th></tr>
<tr><td>mysendpolicy</td><td>Отправка</td></tr>
<tr><td>myreceivepolicy</td><td>Прослушивание</td></tr>
</table>После создания разрешений выберите значок **Сохранить** в нижней части страницы. При этом создаются политики совместного доступа, которые будут использоваться для отправки (**mysendpolicy**) сообщений в этот концентратор событий и их прослушивания (**myreceivepolicy**).

	![политики](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Создание политик концентратора событий")

	
5. На этой же странице запишите ключи политики, созданные для двух политик. Сохраните эти ключи для использования в дальнейшем.

	![ключи политики](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "Сохранение ключей политики")

6. На странице **Панель мониторинга** в нижней части экрана щелкните **Сведения о подключении**, чтобы получить и сохранить строки подключения для концентратора событий с помощью двух политик.

	![ключи политики](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "Сохранение строк подключения политики")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Получение сообщений в Spark в HDInsight с помощью Zeppelin

В этом разделе мы создадим записную книжку [Zeppelin](https://zeppelin.incubator.apache.org) для получения сообщений из концентратора событий в кластер Spark в HDInsight.

### Выделение ресурсов для Zeppelin, предназначенных для приложений потоковой передачи

При создании приложений потоковой передачи с помощью Zeppelin необходимо учитывать указанные ниже рекомендации.

* **Разделы и ядра концентратора событий, выделенные для Zeppelin**. В предыдущих действиях вы создали концентратор событий с несколькими разделами. В приложении потоковой передачи Zeppelin, которое вы создадите далее, вы укажете то же самое количество разделов. Чтобы успешно выполнять потоковую передачу данных из концентратора событий с помощью Zeppelin, количество ядер, выделяемых для Zeppelin, должно в два раза превышать количество разделов в концентраторе событий.
* **Минимальное количество ядер, которые необходимо выделить для Zeppelin**. В приложении потоковой передачи, которое вы создадите далее, создайте временную таблицу, в которой вы будете хранить сообщения, передаваемые приложением. Для чтения сообщения из этой таблицы можно использовать инструкцию SQL Spark. Для успешного выполнения инструкции SQL Spark необходимо выделить Zeppelin не менее двух ядер.

Если объединить два требования выше, получится указанное ниже требование.

* Для Zeppelin необходимо выделить не менее двух ядер.
* Количество выделенных ядер всегда должно быть в два раза больше количества разделов в концентраторе событий. 

Инструкции о том, как выделять ресурсы в кластере Spark, см. в статье [Управление ресурсами кластера Apache Spark в HDInsight](hdinsight-apache-spark-resource-manager.md).

### Создание приложения потоковой передачи с помощью Zeppelin

1. На начальной панели [портала предварительной версии Azure](https://ms.portal.azure.com/) щелкните плитку кластера Spark (если она закреплена на этой панели). Кроме того, можно перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.   

2. Запустите записную книжку Zeppelin. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Записная книжка Zeppelin**. При появлении запроса введите учетные данные администратора для кластера. Следуйте инструкциям на открывшейся странице для запуска записной книжки.

2. Создайте новую записную книжку. На панели заголовка щелкните **Записная книжка** и в раскрывающемся списке выберите пункт **Создать заметку**.

	![Создание новой записной книжки Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "Создание новой записной книжки Zeppelin")

	На той же странице под заголовком **Записная книжка** отобразится новая записная книжка с именем, начинающимся с **Заметка XXXXXXXXX**. Щелкните новую записную книжку.

3. На веб-странице для новой записной книжки щелкните заголовок и при необходимости переименуйте записную книжку. Нажмите клавишу ВВОД, чтобы сохранить изменения. Кроме того, убедитесь, что в правом верхнем углу в заголовке записной книжки отображается состояние **Подключено**.

	![Состояния записной книжки Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Состояния записной книжки Zeppelin")

4. В пустой абзац новой записной книжки, который создается по умолчанию, вставьте следующий фрагмент кода и замените заполнители на данные из своей конфигурации концентратора событий. Этот фрагмент кода получает поток из концентратора событий и регистрирует поток во временной таблице с именем **mytemptable**. В следующем разделе мы запустим приложение отправителя. После этого данные можно будет считывать непосредственно из таблицы.

	> [AZURE.NOTE]В фрагменте кода ниже в качестве значения для **eventhubs.checkpoint.dir** необходимо указать каталог в контейнере хранилища, используемом по умолчанию. Если такого каталога нет, приложение потоковой передачи создаст его. Вы можете указать как полный (например, **wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/**), так и относительный путь к каталогу, например **/mycheckpointdir**.

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>Запуск приложений

1. В записной книжке Zeppelin выполните абзац с фрагментом кода. Нажмите клавиши **SHIFT+ВВОД** или кнопку **Воспроизвести** в правом верхнем углу.

	Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные будут отображаться в нижней части того же абзаца. Снимок экрана выглядит следующим образом:

	![Выходные данные фрагмента кода](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "Выходные данные фрагмента кода")

2. Запустите проект **Sender** и нажмите клавишу **ВВОД** в окне консоли, чтобы запустить функцию отправки сообщений в концентратор событий.

3. В записной книжке Zeppelin в новом абзаце введите следующий фрагмент кода для чтения сообщений, полученных в Spark.

		%sql 
		select * from mytemptable limit 10

	На снимке экрана ниже отображены полученные сообщения в таблице **mytemptable**.

	![Получение сообщений в Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Получение сообщений в записной книжке Zeppelin")

4. Перезапустите интерпретатор Spark SQL, чтобы выйти из приложения. Перейдите на вкладку **Интерпретатор** в верхней части окна, а затем для интерпретатора Spark нажмите кнопку **Перезапустить**.

	![Перезапуск интерпретатора Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Перезапуск интерпретатора Zeppelin")

##<a name="sparkstreamingha"></a>Запуск приложения потоковой передачи с высоким уровнем доступности

Использование Zeppelin для получения потоковых данных в кластер Spark в HDInsight — это отличный способ создания прототипа приложения. Тем не менее для запуска приложения потоковой передачи в производственной среде с высоким уровнем доступности и устойчивости, необходимо сделать следующее:

1. Скопируйте JAR-файл зависимостей в учетную запись хранения, связанную с кластером.
2. Создайте приложение для потоковой передачи.
3. Подключитесь к кластеру с помощью удаленного рабочего стола и скопируйте JAR-файл приложения в головной узел кластера.
3. Подключитесь к кластеру с помощью удаленного рабочего стола и запустите приложение на узле кластера.

Инструкции по выполнению этих действий и пример приложения потоковой передачи можно скачать с сайта GitHub по адресу [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Быстрый запуск: подготовка Apache Spark в HDInsight и выполнение интерактивных запросов с помощью Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Создание приложений машинного обучения с помощью Spark в HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=Oct15_HO1-->