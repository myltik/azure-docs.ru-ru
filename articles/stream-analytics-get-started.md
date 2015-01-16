<properties linkid="manage-services-Stream-Analytics-get-started" urlDisplayName="Get Started" pageTitle="Приступая к работе с Azure Stream Analytics | Azure" metaKeywords="" description="Приступите к использованию анализа потоков Azure, чтобы обрабатывать и преобразовывать события в концентраторе событий Azure Service Bus и сохранять результаты в базе данных SQL Azure." metaCanonical="" services="stream analytics" documentationCenter="" title="Get started with Azure Stream Analytics" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="stream analytics" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/28/2014" ms.author="jgao" />


# Приступая к работе с Azure Stream Analytics

Azure Stream Analytics является полностью управляемой службой, обеспечивающей низкую задержку и высокий уровень доступности, масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Подробную информацию см. во [введении в Azure Stream Analytics][stream.analytics.introduction] и [документации по Azure Stream Analytics][stream.analytics.documentation].

Чтобы вы могли быстро начать работу со Stream Analytics, в этом учебнике показано, как получать данные с устройства измерения температуры с [концентратора Event Hub в Azure Service Bus ][azure.event.hubs.documentation] и обрабатывать данные, передавая результаты в [базу данных SQL Azure][azure.sql.database.documentation].  На следующей диаграмме показан поток событий: поступление данных, обработка, вывод данных.
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##Создание образцовых данных концентратора событий
В этом учебнике будет использоваться приложение для демонстрации работы концентраторов событий служебной шины (образец кода в MSDN CodeGallery). В нем создается новый концентратор событий, формируются показания устройства измерения температуры, а затем эти данные передаются в концентратор событий.

###Создайте пространство имен служебной шины
Приложение-образец создаст концентратор событий в созданном пространстве имен служебной шины.  Можно использовать ранее подготовленное пространство имен служебной шины или выполнить следующие действия, чтобы создать новое.

1.	Выполните вход на [портал управления Azure][azure.management.portal].
2.	Щелкните **СОЗДАТЬ** в нижней части страницы служебной шины и следуйте указаниям, чтобы создать пространство имен. Задайте тип **MESSAGING**.
3.	Щелкните только что созданное пространство имен, затем щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ** в нижней части страницы.
4.	Скопируйте строку подключения. Она потребуется вам позже.

###Создание учетной записи хранения Azure

Для этого учебного приложения необходима учетная запись Azure Storage или эмулятор хранилища, чтобы поддерживать состояние приложения. Можно использовать уже существующую учетную запись хранения или создать новую, выполнив следующие действия. 

1.	Создайте на портале новую учетную запись хранения, щелкнув **СОЗДАТЬ**, **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, **БЫСТРО СОЗДАТЬ** и выполнив указания.
2.	Выберите созданную учетную запись хранения и щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
3.	Скопируйте имя учетной записи хранения и один из ключей доступа.

###Создание образцовых данных концентратора событий

1.	Загрузите и распакуйте архив [Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) на свою рабочую станцию.
2.	Откройте файл решения **EventHubSample.sln** в Visual Studio.
3.	Откройте **app.config**.
4.	Задайте строки подключения для служебной шины и учетной записи хранения. Имена ключей - **Microsoft.ServiceBus.ConnectionString** и **AzureStorageConnectionString**. Строка подключения учетной записи хранения будет иметь следующий формат: 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	Выполните сборку решения.
6.	Запустите приложение из папки bin.  Оно используется следующим образом. 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

В следующем примере создается новый концентратор событий под названием **devicereadings** с **16** разделами, а затем в концентратор событий отправляются **200** событий. 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###Создание политики общего доступа к концентратору событий
Несмотря на то что в пространстве имен служебной шины уже есть политика общего доступа, которую можно использовать для подключения к любым объектам в пространстве имен, мы создадим отдельную политику, действующую только для концентратора событий, чтобы повысить уровень безопасности.

1.	Из рабочей области служебной шины на портале щелкните имя пространства имен служебной шины.
2.	Щелкните **КОНЦЕНТРАТОРЫ СОБЫТИЙ** в верхней части страницы.
3.	Щелкните **devicereadings**, концентратор событий, используемый в этом примере.
4.	Щелкните **НАСТРОЙКА** в верхней части страницы.
5.	В разделе политик общего доступа, создайте новую политику с разрешением на **Управление**.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	В нижней части страницы щелкните **СОХРАНИТЬ**.
7.	Если концентратор событий находится в другой подписке, в которой будет задание Stream Analytics, вам потребуется скопировать и сохранить строку подключения для будущего использования.  Для этого нужно щелкнуть **ПАНЕЛЬ МОНИТОРИНГА**, а затем **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ** в нижней части страницы и сохранить строку подключения.


##Подготовка базы данных SQL Azure для хранения выходных данных
Azure Stream Analytics может выводить данные в базу SQL Azure, хранилище больших двоичных объектов Azure и концентратор событий в Azure. В этом примере вы определите задание, выводящее информацию в базу данных SQL Azure. Дополнительные сведения см. в разделе "Приступая к работе с базой данных SQL Microsoft Azure".

###Создание базы данных SQL Azure
Если у вас уже имеется база данных SQL Azure, которую можно использовать для данного примера, пропустите этот раздел.

1.	Щелкните на портале управления **СОЗДАТЬ**, **СЛУЖБЫ ДАННЫХ**, **БАЗА ДАННЫХ SQL**, **БЫСТРО СОЗДАТЬ**.  Укажите имя базы данных на существующем или новом сервере баз данных SQL.
2.	Выберите вновь созданную базу данных
3.	Щелкните **ПАНЕЛЬ МОНИТОРИНГА**, щелкните **Показать строки подключения** на правой панели страницы, затем скопируйте строку подключения **ADO.NET**. Она потребуется вам позже.  
4.	Убедитесь, что параметры брандмауэра на уровне сервера разрешают вам подключение к базе данных.  Это можно сделать путем добавления нового правила IP-адресов на вкладке "Настройка сервера". Более подробные сведения, в т. ч. и о работе с динамическими IP-адресами, см. по ссылке [http://msdn.microsoft.com/ru-ru/library/azure/ee621782.aspx](http://msdn.microsoft.com/ru-ru/library/azure/ee621782.aspx).

###Создание выходных таблиц
1.	Откройте Visual Studio или SQL Server Management Studio.
2.	Подключитесь к базе данных SQL Azure.
3.	Используйте следующие инструкции T-SQL, чтобы создать две таблицы в своей базе данных:

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] Во всех таблицах баз данных SQL для вставки данных требуются кластеризованные индексы.
	   
##Создание задания Stream Analytics

Создав концентратор событий для служебной шины Azure, базу данных SQL Azure и выходные таблицы, вы готовы создать задание Stream Analytics.

###Подготовка задания Stream Analytics
1.	Щелкните на портале управления **СОЗДАТЬ**, **СЛУЖБЫ ДАННЫХ**, **STREAM ANALYTICS**, **БЫСТРО СОЗДАТЬ**. 
2.	Укажите следующие значения и щелкните **СОЗДАТЬ ЗАДАНИЕ STREAM ANALYTICS**:

	- **ИМЯ ЗАДАНИЯ**. Введите имя задания. Возможный вариант - **DeviceTemperatures**.
	- **РЕГИОН**: Выберите регион, где должно выполняться задание. В предварительной версии Azure Stream Analytics доступна только в 2 регионах. Более подробную информацию см. в разделе [Ограничения и известные проблемы Azure Stream Analytics][stream.analytics.limitations]. Хорошим вариантом будет размещение задания и концентратора событий в одном регионе - так вы обеспечите себе наилучшую производительность и не будете должны оплачивать пересылку данных между регионами.
	- **УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ**. Выберите учетную запись хранения, которую вы хотите использовать для отслеживания данных всех заданий Stream Analytics, запущенных в этом регионе. Можно выбрать существующую учетную запись хранения или создать новую.
	
3.	Щелкните **STREAM ANALYTICS** на левой панели, чтобы открыть список задач Stream Analytics.

	![][img.stream.analytics.portal.button]
 
Появится новое задание с состоянием **НЕ ЗАПУЩЕНО**.  Обратите внимание, что кнопка **ЗАПУСК** в нижней части страницы отключена. Прежде чем запустить задание, необходимо настроить задание ввода, вывода, запрос и т. д. 

###Укажите вход данных для задания

1.	Щелкните имя задания.
2.	Щелкните **ВХОДНЫЕ ДАННЫЕ** в верхней части страницы, затем щелкните **ДОБАВИТЬ ВХОДНЫЕ ДАННЫЕ**. Открывшееся диалоговое окно поможет выполнить ряд действий для настройки входных данных.
3.	Выберите **ПОТОК ДАННЫХ** и щелкните стрелку вправо.
4.	Выберите **КОНЦЕНТРАТОР СОБЫТИЙ** и щелкните стрелку вправо.
5.	На третьей странице введите или выберите следующие значения: 

	- **ПСЕВДОНИМ ВХОДНЫХ ДАННЫХ**. Введите осмысленное имя для этого задания входных данных. Впоследствии это имя будет использоваться в запросе.
	- **КОНЦЕНТРАТОР СОБЫТИЙ**. Если созданный вами концентратор событий находится в той же подписке, что и задание Stream Analytics, выберите пространство имен, в котором расположен концентратор.

		Если ваш концентратор событий находится в другой подписке, выберите **Использовать концентратор событий из другой подписки** и введите вручную **ПРОСТРАНСТВО ИМЕН СЛУЖЕБНОЙ ШИНЫ**, **ИМЯ КОНЦЕНТРАТОРА EVENT HUB**, **ИМЯ ПОЛИТИКИ КОНЦЕНТРАТОРА СОБЫТИЙ**, **КЛЮЧ ПОЛИТИКИ КОНЦЕНТРАТОРА СОБЫТИЙ** и **КОЛИЧЕСТВО РАЗДЕЛОВ КОНЦЕНТРАТОРА СОБЫТИЙ**.  

		>[WACOM.NOTE] В этом примере используется количество разделов, заданное по умолчанию, - 16.
		
	- **ИМЯ КОНЦЕНТРАТОРА СОБЫТИЙ**. Выберите имя созданного вами концентратора событий для Azure. В данном примере мы будем использовать имя **devicereadings**.
	- **ИМЯ ПОЛИТИКИ КОНЦЕНТРАТОРА СОБЫТИЙ**. Выберите политику концентратора событий, созданную ранее в этом примере.
 
	![][img.stream.analytics.config.input]

6.	Щелкните стрелку вправо.
7.	Укажите следующие значения:

	- **ФОРМАТ СЕРИАЛИЗАТОРА СОБЫТИЙ**: JSON
	- **КОДИРОВКА**: UTF8

8.	Нажмите кнопку "Проверить", чтобы добавить этот источник и убедится, что Stream Analytics может успешно подключаться к концентратору событий.

###Укажите выходные данные задания
1.	Щелкните **ВЫХОДНЫЕ ДАННЫЕ** в верхней части страницы, затем щелкните **ДОБАВИТЬ ВЫХОДНЫЕ ДАННЫЕ**.
2.	Выберите **БАЗА ДАННЫХ SQL** и щелкните стрелку вправо.
3.	Введите или выберите следующие значения.  Используйте строку подключения ADO.NET из базы данных, чтобы заполнить следующие поля:

	- **база данных SQL**: Выберите базу данных SQL, созданную ранее в этом примере.  Если она находится в той же самой подписке, выберите базу данных из раскрывающегося списка.   В противном случае заполните вручную поля "Имя сервера" и "База данных". 
	- **ИМЯ ПОЛЬЗОВАТЕЛЯ**. Введите имя пользователя базы данных SQL.
	- **ПАРОЛЬ**. Введите пароль пользователя базы данных SQL.
	- **ТАБЛИЦА**. Укажите таблицу, в которую хотите отправлять выходные данные.  В этом примере мы будем использовать **PassthroughReadings**.

	![][img.stream.analytics.config.output]

4.	Нажмите кнопку "Проверить", чтобы создать выходные данные и убедиться, что Stream Analytics успешно подключается к указанной базе данных SQL.

###Укажите запрос задания
Stream Analytics поддерживает простую декларативную модель запроса для описания преобразований.  Дополнительные сведения о языке см. в разделе "Справочник по языку запросов Azure Stream Analytics".  

Здесь мы для начала рассмотрим простой передаваемый запрос, выводящий показания устройства для измерения температуры в таблицу базы данных SQL.

1.	В верхней части страницы щелкните **Запрос**
2.	Добавьте в редакторе следующий код:

		SELECT DeviceId, Temperature FROM input
Убедитесь, что имя входного источника соответствует имени входных данных, указанному вами ранее.
3.	Щелкните **СОХРАНИТЬ** в нижней части страницы и выберите **ДА**, чтобы подтвердить действие.

##Запустите задание
По умолчанию задания Stream Analytics начинают считывание входящих событий с момента запуска.  Поскольку в концентраторе событий содержатся существующие данные для обработки, необходимо настроить задание так, чтобы оно использовало эти данные журнала.  

1.	В верхней части страницы щелкните **НАСТРОИТЬ**.
2.	Измените значение параметра **НАЧАЛО ВЫВОДА** на **НАСТРАИВАЕМОЕ ВРЕМЯ** и задайте время начала.  Убедитесь, что время начала предшествует времени запуска BasicEventHubSample.  
3.	Щелкните **СОХРАНИТЬ** в нижней части страницы и выберите **ДА**, чтобы подтвердить действие.
3.	Щелкните **ПАНЕЛЬ МОНИТОРИНГА** в верхней части страницы, затем щелкните **ПУСК** в нижней части страницы и выберите **ДА**, чтобы подтвердить действие.  На панели **Сводка** параметр **СТАТУС** изменится на **Идет запуск**. Процесс запуска может занять несколько минут, после чего задание перейдет в статус **Выполняется**.   


##Просмотрите выходные данные задания

1.	Подключитесь к своей базе данных SQL в Visual Studio или SQL Server Management Studio и выполните следующий запрос: 

		SELECT * FROM PassthroughReadings

2.	Вы увидите записи, соответствующие событиям чтения из концентратора событий.   

	![][img.stream.analytics.job.output1]

	Приложение BasicEventHubSample можно перезапустить, чтобы создать новые события, и увидеть в реальном времени их поступление на выход, выполнив еще раз запрос SELECT *.
	
	При наличии проблем, связанных с пропажей выходных данных или их неправильным выводом, ознакомьтесь с журналами операций для задания, ссылка на которые имеется в правой части панели мониторинга.

##Остановите задание, обновите и перезапустите его
Теперь сделаем запрос данных интереснее.
1.	Нажмите на странице **ПАНЕЛЬ МОНИТОРИНГА** или **МОНИТОР** кнопку **СТОП**.
2.	Замените на странице **ЗАПРОС** уже готовый запрос текстом, приведенным ниже, и щелкните **СОХРАНИТЬ**.

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	Этот новый запрос будет использовать в качестве отметки времени перехода события в концентратор событий, вычисляя среднюю температуру за промежуток в 5 секунд и количество событий, произошедших за этот промежуток.
3.	Нажмите на странице **ВЫВОД** кнопку **ИЗМЕНИТЬ**.  Измените выходную таблицу с PassthroughReadings на AvgReadings и щелкните значок с галочкой.

4.	Нажмите на странице **ПАНЕЛЬ МОНИТОРИНГА** кнопку **ПУСК**.

##Просмотрите выходные данные задания

1.	Подключитесь к базе данных SQL в Visual Studio или SQL Server Management Studio и выполните следующий запрос:

		SELECT * from AvgReadings

2.	Вы увидите записи для 5-секундных интервалов, в которых показаны средняя температура и количество событий для каждого устройства. 

	![][img.stream.analytics.job.output2]
 
3.	 Чтобы продолжить просмотр событий, обрабатываемых выполняемым заданием, перезапустите приложение BasicEventHubSample.







##<a name="nextsteps"></a>Дальнейшие действия
В этом учебнике вы узнали, как использовать Stream Analytics для обработки данных о погоде. Чтобы получить дополнительные сведения, ознакомьтесь со следующими статьями:


- [Введение в Azure Stream Analytics][stream.analytics.introduction]
- [Руководство разработчика по Stream Analytics][stream.analytics.developer.guide]
- [Задания по масштабированию в Azure Stream Analytics][stream.analytics.scale]
- [Ограничения и известные проблемы Azure Stream Analytics][stream.analytics.limitations]
- [Справочник по языку запросов Azure Stream Analytics][stream.analytics.query.language.reference]
- [Справочник по REST API для управления Azure Stream Analytics][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/ru-ru/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/ru-ru/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/ru-ru/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/ru-ru/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/ru-ru/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/ru-ru/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com

