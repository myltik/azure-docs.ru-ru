<properties 
	pageTitle="Начало работы со средствами HDInsight для Visual Studio | Azure" 
	description="Узнайте, как установить и использовать средства HDInsight для Visual Studio, чтобы подключаться к HDInsight и выполнять запросы Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="11/12/2014" 
	ms.author="jgao" 
	editor="cgronlun"/>

# Начало работы со средствами  HDInsight Hadoop для Visual Studio

Узнайте, как установить и использовать средства HDInsight для Visual Studio, чтобы подключаться к HDInsight и выполнять запросы Hive. Дополнительные сведения об использовании HDInsight см. в разделах [Введение в HDInsight][hdinsight.introduction] и [Начало работы с HDInsight][hdinsight.get.started].

+ [Установка] 
+ [Подключение к подписке Azure]
+ [Переход на связанные ресурсы]
+ [Выполнение запросов Hive]
+ [Дальнейшие действия]


##Предварительные требования

- Рабочая станция со следующими установленными компонентами:

	- Windows 7 или Windows 8;
	- Visual Studio 2012 с [обновлением 4](http://www.microsoft.com/ru-ru/download/details.aspx?id=39305), Visual Studio 2013 с [обновлением 3](http://www.microsoft.com/ru-ru/download/details.aspx?id=43721) или [Visual Studio Express 2013](http://www.microsoft.com/ru-ru/download/details.aspx?id=43722).

	>[AZURE.NOTE] В настоящее время доступна только англоязычная версия этих средств. 


## Установка

Средства HDInsight для Visual Studio поставляются вместе с пакетом Microsoft Azure SDK. Для их установки можно использовать [установщик веб-платформы](http://go.microsoft.com/fwlink/?LinkId=255386).

![HDinsight Tools for Visual Studio Web Platform installer][1]


## Подключение к подписке Azure
Средства HDInsight для Visual Studio позволяют подключаться к кластерам HDInsight, осуществлять некоторые базовые операции управления и выполнять запросы Hive.

**Чтобы подключиться к подписке Azure:**

1.	Запустите Visual Studio.
2.	В меню **Вид** выберите **Обозреватель серверов**, чтобы открыть окно обозревателя.
3.	Разверните **Azure**, а затем нажмите кнопку кластеров **HDInsight**. 

	>[AZURE.NOTE]Окно **Список задач HDInsight** должно открыться автоматически. Если окно не открылось, это можно сделать, нажав последовательно **Другие окна** в меню **ВИД** и **Окно списка задач HDInsight**.  
4.	Введите учетные данные подписки Azure и нажмите **Войти**. Это необходимо, только если подключение к подписке Azure из Visual Studio на данной рабочей станции ранее не выполнялось.
5.	В обозревателе серверов будет представлен список существующих кластеров HDInsight. Если кластеров нет в наличии, их можно подготовить с помощью портала управления, Azure PowerShell или пакета SDK для HDInsight.  Дополнительные сведения см. в разделе [Подготовка кластеров HDInsight к работе][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Разверните кластер HDInsight. На мониторе отобразятся **Базы данных Hive**, учетная запись хранения по умолчанию и связанные учетные записи хранения. Развертывание объектов можно продолжить. 

После подключения к подписке Azure, можно будет выполнить следующее:

**Чтобы подключиться к порталу управления из Visual Studio**

- В обозревателе сервера разверните **Azure**, **кластеры HDInsight**, правой кнопкой мыши нажмите на кластер HDInsight и выберите **Управление кластера на портале Azure**.

**Чтобы задать вопросы и оставить отзыв из Visual Studio:**

- В меню **СРЕДСТВА** выберите **HDInsight** и затем нажмите **Форум MSDN** (чтобы задать вопрос) или **Оставить отзыв**.

## Переход на связанные ресурсы 

В обозревателе серверов отобразятся учетная запись хранения по умолчанию и все связанные учетные записи хранения. Разверните учетную запись хранилища по умолчанию. В результате, отобразятся контейнеры в учетной записи хранения. Учетная запись хранения по умолчанию и контейнер по умолчанию отмечены. Можно просмотреть любой контейнер, щелкнув его правой кнопкой мыши.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Выполнение запросов Hive
[Apache Hive][apache.hive] - это инфраструктура хранилища данных, созданная на основе Hadoop и обеспечивающая формирование сводных данных, запросов и анализа. Средства HDInsight для Visual Studio поддерживают выполнение запросов Hive из Visual Studio. Дополнительные сведения о Hive см. в разделе [Использование Hive с HDInsight][hdinsight.hive].

###Просмотр таблицы hivesampletable (пример таблицы Hive)
Все кластеры HDInsight поставляются с примером таблицы Hive, которая называется *hivesampletable*. Данная таблица будет использоваться для отображения списка таблиц Hive, просмотра схемы таблиц и списка строк в таблице Hive.



**Чтобы создать список таблиц Hive и просмотреть схемы таблицы Hive**

1.	В **Обозревателе серверов** последовательно разверните **Azure**, **Кластер HDInsight**, узлы кластера, **Базы данных Hive**, **По умолчанию**, а затем разверните **hivesampletable** для просмотра схемы таблицы.
4.	Щелкните **hivesampletable** правой кнопкой мыши, а затем - **Просмотреть первые 100 строк** для просмотра строк. Это эквивалентно выполнению следующего запроса Hive с помощью драйвера Hive ODBC:

		SELECT * FROM hivesampletable LIMIT 100

	Подсчет строк можно настроить. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Создание таблиц Hive

Таблицу Hive можно создать с помощью графического интерфейса пользователя (GUI) или запросов Hive. Узнать больше об использовании запросов Hive можно в разделе [Запуск запросов Hive].(#run.queries).

**Чтобы создать таблицу Hive:**

1. В **обозревателе сервера** разверните **Azure**, **Кластеры HDInsight**, кластер HDInsight, **Базы данных Hive**, щелкните правой кнопкой мыши **По умолчанию**, а затем нажмите **Создать таблицу**.
2. Настройка таблицы.
3. Нажмите кнопку **Создать таблицу**, чтобы отправить задание по созданию новой таблицы Hive.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Выполнение запросов Hive
Существует два способа создания и выполнения запросов Hive:

- Создание нерегламентированных запросов
- Создание приложения Hive

**Создание и запуск нерегламентированных запросов**

1. В **службе обозревателя** разверните вкладку **Azure**, а затем - **Кластеры HDInsight**.
2. Правой кнопкой мыши щелкните кластер, в котором вы хотите выполнить запрос, и выберите **Написать запрос Hive**. 
3. Введите запросы Hive.
4. Нажмите кнопку **Отправка** или **Отправка (расширенная)**. С помощью расширенной отправки можно настроить **Имя задания**, **Аргументы**, **Дополнительные конфигурации** и **Каталог состояния** скрипта:

	![hdinsight hadoop hive query][9]

	После отправки задания появится окно **Сводка заданий Hive**.

	![hdinsight hadoop hive query][8]
5. Используйте кнопку **Обновить**, чтобы обновлять состояние задания до его смены на **Завершено**.
6. Нажмите на ссылки в нижней части, чтобы перейти к **Запросу задания**, **Выходным данным задания** и **Журналу задания**.



**Чтобы создать и запустить решения Hive:**

1. В меню **ФАЙЛ** выберите команду **Создать**, а затем - **Проект**.
2. Выберите **HDInsight** в левой области окна, **Hive приложения** в средней области, введите свойства и нажмите **ОК**.
3. В **Обозревателе решений** дважды щелкните файл **Script.hql**, чтобы открыть его.

 
###Просмотр заданий Hive
Для всех заданий Hive можно просмотреть задания запроса, выходные данные задания и журнал задания.

**Чтобы просмотреть задания Hive**

1. В **обозревателе сервера** разверните **Azure**, а затем - **HDInsight**. 
2. Щелкните правой кнопкой мыши кластер HDInsight, а затем - элемент **Просмотр заданий Hive**. Появится список заданий Hive, запущенных на кластере. 
3. Выберите задание из списка, а затем используйте окно **Сводка заданий Hive**, чтобы открыть **Запрос задания**, **Выходные данные задания** или **Журнал задания**.

## Дальнейшие действия
В этой статье вы узнали, как подключаться к кластерам HDInsight из Visual Studio и выполнять запросы Hive. Дополнительные сведения см. в разделе:

- [Использование Hadoop Hive в HDInsight][hdinsight.hive]
- [Приступая к работе с Hadoop в HDInsight][hdinsight.get.started]
- [Отправка заданий Hadoop в HDInsight][hdinsight.submit.jobs]
- [Анализ данных Twitter с Hadoop в HDInsight][hdinsight.analyze.twitter.data]


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


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org<!--HONumber=42-->
