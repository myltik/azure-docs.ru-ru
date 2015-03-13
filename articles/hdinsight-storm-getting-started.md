<properties 
	pageTitle="Начало работы с использованием Apache Storm c Microsoft Azure HDInsight (Hadoop)" 
	description="Узнайте, как использовать   Apache Storm для обработки данных с HDInsight (Hadoop) в режиме реального времени" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# Начало работы с использованием Storm c HDInsight (Hadoop)

Apache Storm - это масштабируемая отказоустойчивая распределенная система выполнения расчетов для обработки данных потоковой передачи в режиме реального времени. С Azure HDInsight вы можете создать облачный кластер, проводящий анализ данных в режиме реального времени с помощью Storm.

## В этом руководстве вы узнаете...

* [Как подготовить кластер Storm на HDInsight](#provision)

* [Как подключиться к кластеру](#connect)

* [Как выполнить топологию Storm](#run)

* [Как наблюдать за топологией Storm](#monitor)

* [Как остановить топологию Storm](#stop)

* [Дальнейшие действия](#next)

## Перед началом работы

Для успешного завершения этого руководства у вас должно быть следующее:

* Подписка Azure

* Azure PowerShell

* Если вы не знакомы с Apache Storm, вам следует сначала ознакомиться со статьей [Обзор Storm HDInsight](/ru-ru/documentation/articles/hdinsight-storm-overview).

## <a id="provision"></a>Подготовка кластера Storm на портале Azure

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Войдите на [портал управления Azure][azureportal]

2. Щелкните **HDInsight** слева, затем **+СОЗДАТЬ** в нижнем левом углу страницы.

3. Щелкните значок HDInsight во второй колонне, а затем выберите **Настроить**.

4. На странице **Сведения о кластере** введите имя кластера и выберите **Storm** в качестве **Типа кластера**. Щелкните стрелку для продолжения.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. Введите количество **узлов данных** для использования в этом кластере и **регион/виртуальная сеть**, в которых будет создан этот кластер. Щелкните стрелку для продолжения.

	> [AZURE.NOTE] Для сокращения затрат на кластер, приведенный в этой статье, уменьшите **Размер кластера** до 1 и удалите кластер после того, как завершите им пользоваться.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. Введите **Имя пользователя** и **Пароль** администратора, затем выберите стрелку, чтобы продолжить.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. Для **Учетной записи хранения** выберите **Создать новое хранилище** или укажите существующую запись хранения. Выберите или введите **Имя учетной записи** и **Контейнер по умолчанию** для использования.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. На странице **Действия сценариев** щелкните  **добавить действие сценария**  для предоставления сведений о пользовательском скрипте, который необходимо выполнить для настройки кластера при его создании. FНапример действие сценария можно использовать для настройки кластера для установки <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Дополнительные сведения см. в разделе [Настройка кластеров HDInsight с помощью действия сценария](/ru-ru/documentation/articles/hdinsight-hadoop-customize-cluster/). 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия сценария.</td></tr>
		<tr><td>URI-адрес сценария</td>
			<td>Укажите URI для сценария, который вызывается для настройки кластера.</td></tr>
		<tr><td>Тип узла</td>
			<td>Указывает узлы, на которых выполняется скрипт настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите параметры, если они требуются для сценария.</td></tr>
	</table>

	Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов, щелкните флажок, чтобы начать подготовку кластера.

## Использование Storm на HDInsight

Для предварительной версии Storm на HDInsight необходимо подключиться к кластеру с помощью удаленного рабочего стола для работы с Storm. Для подключения к кластеру HDInsight и выполнению команд Storm выполните следующее:

###<a id="connect"></a>Подключитесь к кластеру

1. Разрешите кластеру HDInsight подключение к удаленному рабочему столу с помощью [портала управления Azure][management]. На портале выберите ваш кластер HDInsight, затем выберите флажок "Включить удаленный рабочий стол" в нижней части страницы "Конфигурация".

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    Когда появится запрос на ввод учетных данных, введите имя пользователя и пароль для использования в сеансах подключения к удаленному рабочему столу. Вы также должны выбрать срок действия для конфигурации удаленного рабочего стола.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. После включения доступа к удаленному рабочему столу выберите "Подключить" для начала подключения. После этого будет скачан RDP-файл, который можно использовать для запуска сеанса подключения к удаленному рабочему столу.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] При подключении к удаленному компьютеру вы можете получить несколько приглашений подтвердить доверие этому сертификату.

3. После подключения используйте значок командной строки Hadoop на рабочем столе, чтобы запустить командную строку Hadoop.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. Для замены каталогов на каталог, содержащий команду Storm выполните следующую команду:

	cd %storm_home%\bin

5. Для просмотра доступных команд введите `storm` без параметров.

Кластер The HDInsight поставляется с несколькими примерами топологий Storm. Пример **WordcountTopology** используется следующим образом. Дополнительные сведения о примерах, поставляемых с HDInsight Storm, см. в разделе [Дальнейшие действия](#next).

### <a id="run"></a>Запуск топологии Storm

Для запуска **WordCountTopology** используйте следующие команды.

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Это указывает Storm о том, что мы хотим запустить топологию **wordcount** из класса **storm.starter.WordCountTopology**, который содержится в файле **storm-starter-&lt;версия>-jar-with-dependencies.jar**. Наряду с остальными примерами Storm файл расположен в папке contrib каталога %storm_home%.

> [AZURE.NOTE] Версия файла JAR, содержащего примеры, может периодически меняться. При выполнении команды укажите версию файла, предоставленного вам с кластером.

Заметьте, что после выполнения команды ничего не возвращается. **Топологии Storm после запуска выполняются, пока не будут остановлены пользователем.** Топология WordCountTopology формирует случайные предложения и подсчитывает, сколько раз она встречает каждое слово, до тех пор, пока вы ее не остановите.

### <a id="monitor"></a>Наблюдение за состоянием Storm топологии

Образец The WordCountTopology не записывает выход в каталог, но может использовать интерфейс веб-страниц Storm для обзора состояния топологии, а также выхода.

1. Подключение к кластеру HDInsight с помощью удаленного рабочего стола.

2. Откройте на рабочем столе ссылку **Storm UI**. Отобразится веб-страница с интерфейсом Storm.  В разделе **Сводка топологии** выберите запись **wordcount**.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	Отображает статистику для топологии, включая компоненты, составляющие топологию, т. е. **spouts** и **bolts**.

3. Выберите ссылку на **spout** на странице, а затем выберите номер **порта** для одной из записей в списке **Исполнители (за время)**, содержащий в столбцах **Сгенерировано** и **Передано** числа, большие 0.

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] В зависимости от количества узлов в вашем кластере и выполняемой вами топологии на начало обработки топологии может потребоваться несколько минут. Периодически обновляйте страницу до тех пор, пока значения **Сгенерированный** и **Переданный** не начнут увеличиваться.

4. Вы должны увидеть страницу с информацией, аналогичной показанной ниже.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	Из этого фрагмента можно увидеть, что spout сгенерировал 'snow white and the seven dwarfs', который был разбит на отдельные слова. Подсчитывается также, сколько раз топология обрабатывает каждое слово с начала его запуска. Например, слово 'dwarfs' было сгенерировано spout 8360 раз на момент отображения приведенных выше выходных данных.

### <a id="stop"></a>Остановка топологии Storm

Топология **WordCountTopology** будет выполняться до тех пор, пока ее остановят. Для ее остановки введите следующую команду:

	storm kill wordcount

Если посмотреть на интерфейс веб-страницы Storm непосредственно после ввода команды, можно заметить, что состояние **wordcount** в **Сводке топологии** сменилось на ЗАВЕРШЕН. Через несколько секунд топологии в разделе **Сводка топологии** более не будет.

## <a id="next"></a>Дальнейшие действия

* **Примеры файлов** - кластер HDInsight Storm предоставляет несколько примеров в каталоге **%storm_home%\contrib**. Каждый пример должен содержать следующие элементы:

	* Исходный код, например, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Документы Java, например, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* Пример, например, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Используйте команду 'jar' для извлечения исходного кода или документов Java. Например, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE] Документы Java состоят из веб-страниц. После извлечения откройте в браузере файл **index.html**.

* [Анализ данных датчика в Storm и HDInsight](/ ru-ru/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Разработка приложений для обработки потоковой передачи данных с помощью SCP.NET и C# на Storm в HDInsight](/ ru-ru/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
<!--HONumber=42-->
