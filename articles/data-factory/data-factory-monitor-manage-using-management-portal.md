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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Мониторинг фабрики данных Azure с помощью портала предварительной версии Azure
В этой статье описываются различные сценарии использования портала предварительной версии Azure для мониторинга фабрики данных Azure и управления ею.

## <a name="AllDataFactories"></a> Просмотр всех фабрик данных в подписке Azure

- Выполните вход на [портал предварительной версии Azure][azure-preview-portal].
- Щелкните раздел **ОБЗОР** слева и выберите **Фабрики данных**.  

	![Концентратор "ОБЗОР" -> Фабрики данных][image-data-factory-browse-datafactories]

	Если пункт **Фабрики данных** не отображается, щелкните **Показать все**, а затем выберите **Фабрики данных** в колонке **Обзор**.

	![Концентратор "ОБЗОР" -> Все][image-data-factory-browse-everything]

- В колонке **Фабрики данных** должны отобразиться все фабрики данных.

	![Колонка фабрик данных][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Просмотр сведений о фабрике данных

Для просмотра информации о фабрике данных выполните одно из следующих действий:


- Щелкните фабрику данных в колонке **Фабрики данных**, которая показана выше.
- Щелкните ссылку на фабрику данных на **начальной панели**. **Начальная панель** — это колонка, которая отображается при входе на портал предварительной версии Azure. Если при создании фабрики данных выбран параметр **Добавить на начальную панель** (по умолчанию), должна отобразиться ссылка на фабрику данных на начальной панели, как показано на рисунке ниже. В этом примере ссылки на фабрики данных **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** и **LogProcessingFactory** доступны на **начальной панели**.


![Фабрика данных на начальной панели][image-data-factory-datafactory-from-startboard]

В любом случае отобразится колонка **ФАБРИКА ДАННЫХ** для выбранной фабрики данных, как показано на рисунке ниже.

 ![Домашняя страница фабрики данных][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Просмотр представления схемы фабрики данных
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните плитку **Схема**, чтобы увидеть представление схемы фабрики данных.

![Представление схемы фабрики данных][image-data-factory-diagram-view]
 
### Открытие конвейера в представлении схемы
Вы можете просмотреть все действия в конвейере, щелкнув правой кнопкой конвейер в представлении схемы и нажав пункт **Открыть конвейер**. Будут отображены действия в конвейере вместе с входными и выходными наборами данных для этих действий. ![Открыть конвейер](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Щелкните **Фабрика данных** в строке навигации в верхнем левом углу, чтобы перейти к представлению схемы. В представлении схемы отображаются все конвейеры. В этом примере создан только один конвейер.

## <a name="DataFactoryLinkedServices"></a> Просмотр связанных служб в фабрике данных
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните плитку **Связанные службы**, чтобы отобразить все связанные службы в виде списка.

![Колонка "Связанные службы"][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Просмотр информации о связанной службе
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните связанную службу в списке, чтобы просмотреть информацию о ней.

![Колонка "Связанная служба"][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Просмотр наборов данных в фабрике данных 
В колонке **ФАБРИКА ДАННЫХ** для фабрики данных щелкните плитку **Наборы данных**, чтобы увидеть все таблицы в фабрике данных.

![Колонка "Наборы данных"][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Просмотр информации о наборе данных
Выберите один набор данных из списка наборов данных в колонке наборов данных, чтобы просмотреть подробную информацию об этом наборе. Обратите внимание, что таблица представляет собой прямоугольный набор данных, для которого имеется схема. Сейчас это единственный поддерживаемый тип набора данных.

![Колонка "Таблица"][image-data-factory-table]

В представленной выше колонке **ТАБЛИЦА** оба списка, **Недавно обновленные срезы** и **Срезы, в которых недавно произошел сбой**, сортируются по **ВРЕМЕНИ ПОСЛЕДНЕГО ОБНОВЛЕНИЯ**. Время обновления среза изменяется в следующих ситуациях.

-  Вы обновляете состояние среза вручную, например используя командлет **Set-AzureDataFactorySliceStatus** или щелкнув **ВЫПОЛНИТЬ** в колонке **СРЕЗ** для этого среза.
-  Срез изменяет состояние в ходе выполнения (например, выполнение началось, выполнение завершилось со сбоем, выполнение завершилось успешно и т. п.).
 
	
Чтобы вместо этого просмотреть срезы данных, отсортированные по времени начала и окончания среза, щелкните плитку **Срезы данных (по времени среза)**.
 
![Срезы данных по времени среза][DataSlicesBySliceTime]

Щелкните заголовок списков или **... (многоточие)**, чтобы увидеть расширенный список срезов.

![Все срезы таблицы][image-data-factory-all-slices]

В колонке **Срезы данных** щелкните **Фильтр**, чтобы открыть колонку **Фильтр**, с помощью которой можно **отфильтровать** нужные срезы для изучения. Отобразится колонка, похожая на ту, которая показана ниже, при выборе **Фильтр** в колонке **Срезы данных** со срезами, **отсортированными по времени обновления**.

![Колонка "Фильтр"][image-data-factory-filter-blade]

Колонка **Фильтр** позволяет фильтровать по **последнему времени обновления** и **состоянию среза**. В следующей таблице приводятся все состояния среза и их описание.
 
Состояние среза | Описание
------------ | ------------
PendingExecution | Обработка данных еще не началась.
InProgress | Обработка данных выполняется.
Ready | Обработка данных завершена, и срез данных готов.
Failed | При выполнении запуска, который производит срез, произошла ошибка.
Skip | Пропуск обработки среза.
Retry | Повторная попытка запуска, который производит срез.
Timed Out | Истекло время ожидания обработки данных среза.
PendingValidation | Срез данных ожидает проверки на соответствие политикам проверки перед обработкой.
RetryValidation | Повторная попытка проверки среза.
FailedValidation | Не удалось проверить срез.
LongRetry | Срез будет в этом состоянии, если в таблице JSON указано LongRetry, и при регулярных повторных попытках среза произошли ошибки.
ValidationInProgress | Выполняется проверка среза (с учетом политик, определенных в таблице JSON).

При нажатии кнопки **Фильтр** в колонке **Срезы данных**, в которой срезы **сортируются по времени среза**, отобразится другой тип колонки **Фильтр**.

![Колонка 2 "Фильтр"][image-data-factory-filter-blade-2]


При переходе в колонку **Фильтр** в поле **В** автоматически устанавливается значение ближайшего времени (с округлением) для ограничения количества возвращенных записей. Значение в поле **От** также задается автоматически. Дату в поле **От** можно изменить, щелкнув кнопку **Календарь**. При этом дата в поле **В** изменяется автоматически при изменении даты в поле **От**.

Просмотреть срезы за предыдущий или следующий период можно с помощью кнопок **Назад**/**Далее**. Диапазон времени для кнопок **Назад** и **Далее** устанавливается на основе частоты и интервала срезов в соответствии с таблицей ниже.

Частота | Диапазон значений интервала | Результирующий фрагмент времени
----------| -------------------- | --------------------
Минуты | 1–4 | 6 часов
Минуты | 5–29 | 1 день
Минуты | 30–180 | 7 дней
Минуты | 180+ | 28 дней (приблизительно календарный месяц)
Час | 1–3 | 7 дней
Час | 4–11 | 28 дней (приблизительно календарный месяц)
Час | 12–72 | 182 дня (приблизительно 6 месяцев)
Час | 73+ | 1 год
День | 1–6 | 1 год
День | 7–20 | 5 лет
День | 21+ | 10 лет
Неделя | 1–3 | 5 лет
Неделя | 4+ | 10 лет
Месяц | любой | 10 лет
 
Например, если задать в поле **Частота** значение **Час**, а в поле **Интервал** значение **2**, нажатием кнопок **Назад**/**Далее** можно будет просматривать предыдущие и следующие значения с интервалом в **7 дней**. Эта логика применяется и к колонке "Фильтр" при просмотре всех срезов, последних срезов или проблемных срезов.




## <a name="DataFactorySlice"></a> Просмотр информации о срезе
Щелкните срез в списке срезов в колонке **ТАБЛИЦА** или в колонке **Срезы данных**, чтобы просмотреть информацию об этом срезе.

![Срез данных][image-data-factory-dataslice]

Если срез не находится в состоянии **Готов**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**.

### <a name="DataFactoryActivtyRuns"></a> Просмотр выполнения всех действий для среза
Для среза может быть несколько выполнений. Например, если получить срез не удалось, служба может повторить попытку через некоторое время. Кроме того, если все попытки выполнить срез были неудачными, срез можно выполнить повторно. Все выполнения действий можно увидеть в колонке **Срез данных** в списке ниже.

## <a name="DataFactoryActivtyRunDetails"></a> Просмотр информации о выполнении действия
Щелкните прогон действия в списке прогонов колонки **Срез данных**, чтобы просмотреть информацию о выполнении действия.

![Сведения о выполнении действия][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Детальный обзор операций — события за прошлую неделю
В колонке **ФАБРИКА ДАННЫХ** (или на домашней странице) для фабрики данных выберите **События за неделю** в разделе обзора **операций**, чтобы просмотреть события за прошлую неделю. Благодаря этому можно получить общее представление об операциях, выполненных фабрикой данных на прошлой неделе. Это также поможет устранить любые ошибки, связанные с перемещением или обработкой данных.

![События фабрики данных][image-data-factory-events]


## См. также

Статья | Описание
------ | ---------------
[Мониторинг фабрики данных Azure и управление ею с помощью PowerShell][monitor-manage-using-powershell] | В этой статье рассматривается мониторинг фабрики данных Azure при помощи командлетов Azure PowerShell. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


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
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=August15_HO6-->