<properties 
	pageTitle="Начало работы со средствами HDInsight для Visual Studio | Azure" 
	description="Узнайте, как установить и использовать средства HDInsight для Visual Studio, чтобы подключаться к HDInsight и выполнять запросы Hive." 
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# Начало работы со средствами  HDInsight Hadoop для Visual Studio

Узнайте, как использовать средства HDInsight для Visual Studio, чтобы подключаться к кластерам HDInsight и отправлять запросы Hive. Дополнительные сведения об использовании HDInsight см. в разделах [Введение в HDInsight][hdinsight.introduction] и [Начало работы с HDInsight][hdinsight.get.started]. Дополнительную информацию о подключении к кластеру Storm см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio][hdinsight.storm.visual.studio.tools]. 

>[WACOM.NOTE] Все функции средств HDInsight, доступные для кластеров Windows, также работают в кластерах Linux, за исключением нескольких функций, которые на сегодняшний день не поддерживаются в Humboldt, таких как отображение журналов YARN для определенных заданий Hive. 

>В последнем выпуске появились новые функции, например поддержка Intellisense в редакторе Hive, локальная проверка сценариев Hive и доступ к журналам Yarn.

##Содержание
+ [Установка] 
+ [Подключение к подписке Azure]
+ [Переход на связанные ресурсы]
+ [Выполнение запросов Hive]
+ [Дальнейшие действия]


##Предварительные требования

- Рабочая станция со следующими установленными компонентами:

	- Windows 7, Windows 8 или Windows 8.1
	- Visual Studio следующих версий:
		- Visual Studio 2012 Professional, Premium или Ultimate с [обновлением 4](http://www.microsoft.com/ru-ru/download/details.aspx?id=39305)
		- Visual Studio 2013 Community, Professional, Premium или Ultimate с [обновлением 4](https://www.microsoft.com/ru-ru/download/details.aspx?id=44921)
		- Предварительная версия Visual Studio 2015.

	>[WACOM.NOTE] В настоящее время доступна только англоязычная версия этих средств. 


## Установка

Средства HDInsight для Visual Studio можно установить с помощью [установщика веб-платформы Майкрософт](http://go.microsoft.com/fwlink/?LinkId=255386). Запросить средства можно с помощью ключевого слова hdinsight. Для каждой из версий Visual Studio предусмотрен один отдельный пакет. Необходимо выбрать тот, который соответствует вашей версии Visual Studio.  При установке пакета будет установлен 32-разрядный и 64-разрядный драйвер Microsoft Hive ODBC.

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] Если установлено приложение Visual Studio 2012 или 2015 и старая версия пакета SDK для Azure, необходимо вручную удалить старую версию перед установкой самой новой версии. Visual Studio 2013 поддерживает прямое обновление.

## Подключение к подписке Azure
Средства HDInsight для Visual Studio позволяют подключаться к кластерам HDInsight, осуществлять некоторые базовые операции управления и выполнять запросы Hive.

**Чтобы подключиться к подписке Azure:**

1.	Запустите Visual Studio.
2.	В меню **Вид** выберите **Обозреватель серверов**, чтобы открыть окно обозревателя.
3.	Разверните **Azure**, а затем - **HDInsight**. 

	>[WACOM.NOTE]Окно **Список задач HDInsight** должно открыться автоматически. Если окно не открылось, это можно сделать, нажав последовательно **Другие окна** в меню **ВИД** и **Окно списка задач HDInsight**.  
4.	Введите учетные данные подписки Azure и нажмите **Войти**. Это необходимо, только если подключение к подписке Azure из Visual Studio на данной рабочей станции ранее не выполнялось.
5.	В обозревателе серверов будет представлен список существующих кластеров HDInsight. Если кластеров нет в наличии, их можно подготовить с помощью портала управления, Azure PowerShell или пакета SDK для HDInsight.  Дополнительную информацию см. в разделе [Подготовка кластеров HDInsight к работе][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Разверните кластер HDInsight. Отобразятся **Базы данных Hive**, учетная запись хранения по умолчанию, связанные учетные записи хранения и **журнал службы Hadoop**. Развертывание объектов можно продолжить. 

После подключения к подписке Azure, можно будет выполнить следующее:

**Чтобы подключиться к порталу управления из Visual Studio**

- В обозревателе сервера разверните **Azure**, **HDInsight**, правой кнопкой мыши щелкните кластер HDInsight и выберите **Управление кластером на портале Azure**.

**Чтобы задать вопросы и оставить отзыв из Visual Studio:**

- В меню **СРЕДСТВА** выберите **HDInsight** и затем нажмите **Форум MSDN** (чтобы задать вопрос) или **Оставить отзыв**.

## Переход на связанные ресурсы 

В обозревателе серверов отобразятся учетная запись хранения по умолчанию и все связанные учетные записи хранения. Разверните учетную запись хранилища по умолчанию. В результате, отобразятся контейнеры в учетной записи хранения. Учетная запись хранения по умолчанию и контейнер по умолчанию отмечены. Можно просмотреть любой контейнер, щелкнув его правой кнопкой мыши.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Выполнение запросов Hive
[Apache Hive][apache.hive] - это инфраструктура хранилища данных, созданная на основе Hadoop и обеспечивающая формирование сводных данных, запросов и анализа. Средства HDInsight для Visual Studio поддерживают выполнение запросов Hive из Visual Studio. Дополнительные сведения о Hive см. в разделе [Использование Hive с HDInsight][hdinsight.hive].

Тестирование сценария Hive для кластера HDInsight занимает много времени. Оно может длиться несколько минут или более.  Средства HDInsight для Visual Studio могут проверить грамматику сценария Hive локально без подключения к кластеру в оперативном режиме.

Средства HDInsight для Visual Studio также позволяют пользователям видеть, что находится внутри задания Hive, собирая и отображая журналы Yarn определенных заданий Hive.

###Просмотр таблицы hivesampletable (пример таблицы Hive)
Все кластеры HDInsight поставляются с примером таблицы Hive, которая называется *hivesampletable*. Данная таблица будет использоваться для отображения списка таблиц Hive, просмотра схемы таблиц и списка строк в таблице Hive.



**Чтобы создать список таблиц Hive и просмотреть схемы таблицы Hive**

1.	Чтобы просмотреть схему таблицы, в **обозревателе сервера** последовательно разверните **Azure**, **HDInsight**, узлы кластера, **База данных Hive**, **По умолчанию** и **hivesampletable**.
4.	Щелкните **hivesampletable** правой кнопкой мыши, а затем - **Просмотреть первые 100 строк** для просмотра строк. Это эквивалентно выполнению следующего запроса Hive с помощью драйвера Hive ODBC:

		SELECT * FROM hivesampletable LIMIT 100

	Подсчет строк можно настроить. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Создание таблиц Hive

Таблицу Hive можно создать с помощью графического интерфейса пользователя (GUI) или запросов Hive. Узнать больше об использовании запросов Hive можно в разделе [Запуск запросов Hive](#run.queries).

**Чтобы создать таблицу Hive:**

1. В **обозревателе сервера** разверните **Azure**, **Кластеры HDInsight**, кластер HDInsight, **Базы данных Hive**, щелкните правой кнопкой мыши **По умолчанию**, а затем нажмите **Создать таблицу**.
2. Настройка таблицы.
3. Нажмите кнопку **Создать таблицу**, чтобы отправить задание по созданию новой таблицы Hive.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Проверка и выполнение запросов Hive
Существует два способа создания и выполнения запросов Hive:

- Создание ad-hoc-запросов
- Создание приложения Hive

**Создание, проверка и выполнение ad-hoc-запросов**

1. В **службе обозревателя** разверните вкладку **Azure**, а затем - **Кластеры HDInsight**.
2. Правой кнопкой мыши щелкните кластер, в котором вы хотите выполнить запрос, и выберите **Написать запрос Hive**. 
3. Введите запросы Hive. Обратите внимание, что редактор Hive поддерживает технологию Intellisense.
4. (Необязательно.) Щелкните **Проверить сценарий**, чтобы проверить сценарий на синтаксические ошибки.

	![hdinsight tools for Visual Studio local validation][10]

4. Нажмите кнопку **Отправка** или **Отправка (расширенная)**. С помощью расширенной отправки можно настроить **Имя задания**, **Аргументы**, **Дополнительные конфигурации** и **Каталог состояния** скрипта:

	![hdinsight hadoop hive query][9]

	После отправки задания появится окно **Сводка заданий Hive**.

	![hdinsight hadoop hive query][8]
5. Используйте кнопку **Обновить**, чтобы обновлять состояние задания до его смены на **Завершено**.
6. Перейдите по ссылкам в нижней части, чтобы увидеть **запрос задания**, **выходные данные задания**, **журнал задания** или **журнал Yarn**.



**Чтобы создать и запустить решения Hive:**

1. В меню **ФАЙЛ** выберите команду **Создать**, а затем - **Проект**.
2. Выберите **HDInsight** в левой области окна, **Hive приложения** в средней области, введите свойства и нажмите **ОК**.
3. В **Обозревателе решений** дважды щелкните файл **Script.hql**, чтобы открыть его. 
4. Для проверки сценария Hive, можно нажать кнопку проверки сценария, или щелкнуть правой кнопкой мыши сценарий в редакторе Hive и выбрать пункт "Проверить сценарий" в контекстном меню.

 
###Просмотр заданий Hive
Для заданий Hive можно просмотреть запрос задания, выходные данные задания, журнал задания и журнал Yarn.  См. предыдущий снимок экрана.

Самая последняя версия средства позволяет увидеть, что находится внутри задания Hive, собирая и отображая журналы Yarn. Журнал Yarn может помочь исследовать проблемы производительности. Дополнительная информация о том, как HDInsight собирает журналы YARN, см. в статье [Программный доступ к журналам приложений HDInsight][hdinsight.access.application.logs].

**Чтобы просмотреть задания Hive**

1. В **обозревателе сервера** разверните **Azure**, а затем - **HDInsight**. 
2. Щелкните правой кнопкой мыши кластер HDInsight, а затем - элемент **Просмотр заданий Hive**. Появится список заданий Hive, запущенных на кластере. 
3. Выберите задание в списке заданий, а затем используйте окно **Сводка по заданию Hive**, чтобы открыть **запрос задания**, **выходные данные задания**, **журнал задания** или **журнал Yarn**.

## Дальнейшие действия
В этой статье вы узнали, как подключаться к кластерам HDInsight из Visual Studio и выполнять запросы Hive. Дополнительную информацию см. в разделе:

- [Использование Hive в Hadoop в HDInsight][hdinsight.hive]
- [Приступая к работе с Hadoop в HDInsight][hdinsight.get.started]
- [Отправка заданий Hadoop в HDInsight][hdinsight.submit.jobs]
- [Анализ данных Twitter с помощью Hadoop в HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Установка]: #installation
[Подключение к подписке Azure]: #connect-to-your-azure-subscription
[Переход на связанные ресурсы]: #navigate-the-linked-resources
[Выполнение запросов Hive]: #run-hive-queries
[Дальнейшие действия]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
