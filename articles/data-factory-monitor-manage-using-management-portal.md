<properties 
	pageTitle="Мониторинг фабрики данных Azure и управление ею с помощью портала предварительной версии Azure" 
	description="Узнайте, как отслеживать и контролировать созданные фабрики данных Azure с помощью портала управления Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/10/2015" 
	ms.author="spelluru"/>


# Мониторинг фабрики данных Azure с помощью портала предварительной версии Azure

- [Просмотр всех фабрик данных в подписке Azure](#AllDataFactories)
- [Просмотр информации о фабрике данных](#DataFactoryDetails)
- [Просмотр представления схемы фабрики данных](#DataFactoryDiagram)
- [Просмотр связанных служб в фабрике данных](#DataFactoryLinkedServices)
- [Просмотр информации о связанной службе](#DataFactoryLinkedService) 
- [Просмотр наборов данных в фабрике данных](#DataFactoryDatasets)
- [Просмотр информации о наборе данных](#DataFactoryDataset)
- [Просмотр информации о срезе](#DataFactorySlice) 
- [Просмотр выполнения всех действий для среза](#DataFactoryActivtyRuns) 
- [Просмотр информации о выполнения действия](#DataFactoryActivtyRunDetails)
- [Детальный обзор операций - события за прошлую неделю](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Просмотр всех фабрик данных в подписке Azure

- Войдите на [портал предварительной версии Azure][azure-preview-portal].
- Щелкните раздел **ОБЗОР** слева и выберите команду **Фабрики данных**.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	Если пункт **Фабрики данных** не отображается, щелкните **Показать все**, а затем выберите команду **Фабрики данных** в колонке **Обзор**.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- В колонке **Фабрики данных** должны отобразиться все фабрики данных.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Просмотр информации о фабрике данных

Для просмотра информации о фабрике данных выполните одно из следующих действий: 


- Щелкните фабрику данных в колонке **Фабрики данных**, которая показана выше.
- Щелкните ссылку на фабрику данных в **начальной колонке**. **Начальная панель** - это колонка, которая отображается при входе на портал предварительной версии Azure. Если при создании фабрики данных выбран параметр **Добавить на начальную панель** (по умолчанию), должна отобразиться ссылка на фабрику данных на начальной панели, как показано на рисунке ниже. В этом примере ссылки на фабрики данных **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** и **LogProcessingFactory** доступны в **начальной панели**.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

В любом случае вы увидите колонку **ФАБРИКА ДАННЫХ** для выбранной фабрики данных, как показано на рисунке ниже. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Просмотр представления схемы фабрики данных
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните плитку **Схема**, чтобы увидеть представление схемы фабрики данных. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Просмотр связанных служб в фабрике данных
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните плитку **Связанные службы**, чтобы увидеть все связанные службы в виде списка. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Просмотр информации о связанной службе
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните связанную службу в списке, чтобы просмотреть информацию о ней. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Просмотр наборов данных в фабрике данных 
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните плитку **Наборы данных**, чтобы увидеть все таблицы в фабрике данных.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  Просмотр информации о наборе данных
Выберите один набор данных из списка наборов данных в колонке наборов данных, чтобы просмотреть подробную информацию об этом наборе. Обратите внимание, что таблица представляет собой прямоугольный набор данных, для которого имеется схема. Сейчас это единственный поддерживаемый тип набора данных. 

![Table Blade][image-data-factory-table]

В колонке **ТАБЛИЦА** выше вы увидите **Последние срезы** и **Проблемные срезы**. Щелкните **... (многоточие)**, чтобы просмотреть все срезы. 

![All Slices of a Table][image-data-factory-all-slices]

В колонке **Срезы данных** щелкните кнопку "Фильтр", чтобы открыть колонку фильтра, с помощью которой можно **отфильтровать** нужные срезы.

![Filter Blade][image-data-factory-filter-blade]


При переходе в колонку **Фильтр** в поле **В** автоматически устанавливается значение ближайшего времени (с округлением) для ограничения количества возвращенных записей. Значение в поле **Из** также задается автоматически. Дату в поле **Из** можно изменить, щелкнув кнопку **Календарь**. При этом дата в поле **В** изменяется автоматически при изменении даты в поле **Из**. 

Просмотреть срезы за предыдущий или следующий период можно с помощью кнопок **Назад**/**Далее**. Диапазон времени для кнопок **Назад** и **Далее** устанавливается на основе частоты и интервала срезов в соответствии с таблицей ниже.

Частота | Диапазон значений интервала | Результирующий фрагмент времени
----------| -------------------- | --------------------
Минута | 1-4 | 6 часов
Минута | 5-29 | 1 час
Минута | 30-180 | 7 дней
Минута | 180+ | 28 дней (приблизительно, календарный месяц)
Час | 1-3 | 7 дней
Час | 4-11 | 28 дней (приблизительно, календарный месяц)
Час | 12-72 | 182 дня (приблизительно, 6 месяцев)
Час | 73+ | 1 год
День | 1-6 | 1 год
День | 7-20 | 5 лет
День | 21+ | 10 лет
Неделя | 1-3 | 5 лет
Неделя | 4+ | 10 лет
Месяц | любой | 10 лет
 
Например, если задать в поле **Частота** значение **Час**, а в поле **Интервал** - значение **2**, нажатием кнопок **Назад** и **Далее** можно будет просматривать предыдущие и следующие значения с интервалом в **7 дней**. Эта логика применяется и к колонке "Фильтр" при просмотре всех срезов, последних срезов или проблемных срезов.

В колонке **Фильтр** можно фильтровать срезы по их **состояниям**. В таблице ниже приведены все состояния срезов и их описания.
 
Состояние среза | Описание
------------ | ------------
PendingExecution | Обработка данных еще не начата.
InProgress | Обработка данных выполняется.
Ready | Обработка данных завершена, и срез данных готов.
Failed | При выполнении запуска, который производит срез, произошла ошибка.
Skip | Пропуск обработки среза.
Retry | Повторная попытка запуска, который производит срез.
Timed Out | Истекло время ожидания обработки данных среза.
PendingValidation | Срез данных ожидает проверки на соответствие политикам проверки перед обработкой.
RetryValidation | Повторная попытка проверки среза.
FailedValidation | Не удалось проверить срез.
LongRetry | Срез будет в этом состоянии, если в таблице JSON указано LongRetry и при регулярных повторных попытках среза произошли ошибки.
ValidationInProgress | Выполняется проверка среза (с учетом политик, определенных в таблице JSON).



## <a name="DataFactorySlice"></a> Просмотр информации о срезе
Щелкните срез в списке срезов в колонке **ТАБЛИЦА** или в колонке **Срезы данных**, чтобы просмотреть информацию об этом срезе. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> Просмотр выполнения всех действий для среза
Для среза может быть несколько выполнений. Например, если получить срез не удалось, служба может повторить попытку через некоторое время. Кроме того, если все попытки выполнить срез были неудачными, срез можно выполнить повторно. Все выполнения действий можно увидеть в колонке **Срез данных** в списке ниже. 

## <a name="DataFactoryActivtyRunDetails"></a>  Просмотр информации о выполнения действия
Щелкните прогон действия в списке прогонов колонки **Срез данных**, чтобы просмотреть информацию о выполнении действия. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Детальный обзор операций - события за прошлую неделю
В колонке **ФАБРИКА ДАННЫХ** (или на домашней странице) для фабрики данных выберите **События за неделю** в разделе обзора **операций**, чтобы просмотреть события за прошлую неделю. Благодаря этому можно получить общее представление об операциях, выполненных фабрикой данных на прошлой неделе. Это также поможет устранить любые ошибки, связанные с перемещением или обработкой данных. 

![ Data Factory Events][image-data-factory-events]


## См. также

Статья | Описание
------ | ---------------
[Мониторинг и управление фабрикой данных Azure с помощью PowerShell][monitor-manage-using-powershell] | В этой статье описывается, как выполнять мониторинг фабрики данных Azure с помощью командлетов Azure PowerShell. 
[Включение конвейеров для работы с локальными данными][use-onpremises-datasources] | Эта статья содержит пошаговое руководство, которое показывает, как скопировать данные из локальной базы данных SQL Server в большой двоичный объект Azure.
[Использование сценариев Pig и Hive с фабрикой данных][use-pig-and-hive-with-data-factory] | В этой статье показано, как использовать действия HDInsight для выполнения сценариев Hive и Pig, чтобы обработать входные данные и получить выходные данные. 
[Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных][adf-tutorial] | В этой статье содержится подробное пошаговое руководство, которое показывает, как реализовать практически реальный сценарий с использованием фабрики данных Azure для преобразования данных из файлов журнала в подробные данные.
[Использование пользовательских действий в фабрике данных][use-custom-activities] | В этой статье приводится подробное руководство с пошаговыми инструкциями по созданию пользовательских действий и использованию их в конвейере. 
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается устранение неполадок в работе фабрики данных Azure.
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика содержит полную справку по командлетам, сценарию JSON, функциям и т. д. 
[Справочник по командлетам фабрики данных Azure][cmdlet-reference] | Этот справочник содержит подробную информацию обо всех **командлетах фабрики данных**.


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
