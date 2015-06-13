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
В этой статье описываются различные сценарии использования предварительной версии портала Azure для мониторинга и управления фабрика данных Azure.

## <a name="AllDataFactories"></a> Просмотр всех данных фабрик в подписку Azure

- Подпишитесь на [предварительную версию портала Azure][azure-preview-portal].
- Щелкните **Обзор** концентратора слева и щелкните **данных фабрики**.  

	![Обзор концентратор -> фабрики данных][image-data-factory-browse-datafactories]

	Если вы не видите **данных фабрики**, щелкните **все** и нажмите кнопку **factorries данные** в **Обзор** выноски.

	![Обзор концентратор -> все][image-data-factory-browse-everything]

- Вы увидите все данные фабрики в **данных фабрики** выноски.

	![Выноска фабрики данных][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Просмотр сведений о фабрика данных

Для просмотра информации о фабрике данных выполните одно из следующих действий:


- Щелкните фабрика данных в **данных фабрики** выноску показано выше.
- Щелкните ссылку для фабрики данных на **начальной**. **Начальной** является выноске см, если выполнить вход в на портале Azure предварительной версии. Если вы выбрали **Добавить на начальную панель** при создании фабрики данных (параметр по умолчанию), вы увидите фабрика данных ссылку на начальной панели, как показано на следующем рисунке. В этом примере **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** и **LogProcessingFactory** фабрики каналов данных доступны на **начальной**.


![Фабрика данных на начальной панели][image-data-factory-datafactory-from-startboard]

В любом случае вы увидите **ФАБРИКА данных** выноски для фабрики выбранные данные, как показано на следующем рисунке.

 ![Данные фабрики домашней страницы][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Представление схемы фабрики данных
В **ФАБРИКА данных** щелкните выноску фабрики данных **Схема** плитки для просмотра схемы данных фабрики.

![Представление диаграммы фабрика данных][image-data-factory-diagram-view]
 
### Откройте конвейера в представлении диаграмм
Можно просмотреть все действия в конвейере, щелкнув правой кнопкой мыши конвейера в представлении диаграммы, а затем выбрав **Откройте конвейера**. Вы увидите действий в конвейере и наборы данных ввода-вывода для действий. ![Откройте конвейера](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Щелкните **фабрика данных** в строке навигации в верхнем левом углу, чтобы вернуться в представление диаграммы. Представление диаграммы отображает все каналы. В этом примере вы создали только один конвейер.

## <a name="DataFactoryLinkedServices"></a> Просмотреть связанные службы в фабрике данных
В **ФАБРИКА данных** щелкните выноску фабрики данных **связанные службы** плитки, чтобы увидеть все связанные службы в списке.

![Выноска связанные службы][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Просмотр сведений о связанном службы
В **СВЯЗАННЫЕ службы** стоечный модуль, щелкните связанные службы из списка, чтобы просмотреть сведения о нем.

![Выноска связанные службы][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Просмотр наборов в фабрике данных 
В **ФАБРИКА данных** щелкните выноску фабрики данных **наборы данных** плитки, чтобы увидеть все таблицы в фабрике данных.

![Выноска наборов данных][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Просмотр сведений о наборе данных
Выберите один набор данных из списка наборов данных в колонке наборов данных, чтобы просмотреть подробную информацию об этом наборе. Обратите внимание, что таблица представляет собой прямоугольный набор данных, для которого имеется схема. Сейчас это единственный поддерживаемый тип набора данных.

![Выноска таблицы][image-data-factory-table]

В **таблицы** выноску выше, и **последние обновленные фрагменты** и **недавно произошел сбой фрагменты** списки сортируются по **время ПОСЛЕДНЕГО обновления**. Время обновления фрагмента изменяется в следующих ситуациях.

-  Обновить состояние срез вручную, например, с помощью **набора AzureDataFactorySliceStatus** (или), щелкнув **ЗАПУСКА** на **СРЕЗ** выноски для диаграммы.
-  Срез изменяет состояние из-за выполнения (например выполнения работы, запуска завершается, а не удалось, запуска завершено и выполнена успешно, и т. д).
 
	
Чтобы просмотреть срезы данных, отсортированных по времени начала и окончания фрагмента, вместо этого, щелкните **срезы данных (по времени срез)** плитки.
 
![Срезы данных по времени среза][DataSlicesBySliceTime]

Щелкните заголовок списки или **... (многоточие)** Чтобы просмотреть список больших фрагментов.

![Все фрагменты таблицы][image-data-factory-all-slices]

На **срезы данных** стоечный модуль, щелкните **фильтра** кнопку, чтобы увидеть **фильтра** выноска, позволяющая **фильтра** фрагменты для просмотра конкретного срезы, которые требуется просмотреть. Вы увидите выноске аналогично приведенному ниже, при нажатии кнопки **фильтра** на **срезы данных** выноску с фрагментами **сортируются по время обновления**.

![Стоечный модуль фильтра][image-data-factory-filter-blade]

 **Фильтра** выноску позволяет фильтровать на основе **последнего обновления времени** и **срез состояние**. Следующая таблица описывает все состояния срез и их описание.
 
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
RetryValidation | Повторная проверка фрагмента.
FailedValidation | Не удалось проверить срез.
LongRetry | Срез будет в этом состоянии, если в таблице JSON указано LongRetry, и при регулярных повторных попытках среза произошли ошибки.
ValidationInProgress | Выполняется проверка среза (с учетом политик, определенных в таблице JSON).

При нажатии кнопки **фильтра** на на **срезы данных** выноску с фрагментами **сортируются по времени срез**, вы увидите другого типа **фильтра** выноски.

![Фильтр Выноска 2][image-data-factory-filter-blade-2]


При запуске **фильтра** выноске **для** автоматически присваивается время последней (с округлением) позволяет ограничить число возвращаемых записей.  **Из** поля, а также автоматически устанавливается. Можно изменить **из** даты, щелкнув **календаря** кнопки.  **Для** Дата автоматически изменяется при изменении **из** даты.

Можно щелкнуть **Назад**/ ** Далее ** кнопки для просмотра срезы в предыдущий период и следующий период. Диапазон времени для **Назад** и **Далее** кнопки является набором, основанным на срез частоту и интервал, как показано в следующей таблице.

Частота | Диапазон значений интервала | Результирующий фрагмент времени
----------| -------------------- | --------------------
Минуты | 1-4 | 6 часов
Минуты | 5-29 | 1 день
Минуты | 30-180 | 7 дней
Минуты | 180 + | 28 дней (приблизительно. Календарный месяц)
Час | 1-3 | 7 дней
Час | 4-11 | 28 дней (приблизительно. Календарный месяц)
Час | 12 72 | 182 дня (приблизительные. 6 месяцев)
Час | 73 + | 1 год
День | 1-6 | 1 год
День | 7-20 | 5 лет
День | 21 + | 10 лет
Неделя | 1-3 | 5 лет
Неделя | 4 + | 10 лет
Месяц | любой | 10 лет
 
Например, если определить **Частота** как **час** и **интервал** из **2**, щелкнув **Далее**/ ** назад ** кнопки перемещения диапазона времени **7 дней** в любом направлении. Эта логика применяется и к колонке "Фильтр" при просмотре всех срезов, последних срезов или проблемных срезов.




## <a name="DataFactorySlice"></a> Просмотр сведений о фрагмента
Щелкните срез в списке срезы либо на **таблицы** выноски или **срезы данных** выноски для просмотра сведений о этого фрагмента.

![Срез данных][image-data-factory-dataslice]

Если фрагмент не находится в **готов** состояние, можно увидеть вышестоящего срезы, которые еще не готовы и блокируют текущий фрагмент выполнение в **вышестоящего фрагменты, которые не готовы** списка.

### <a name="DataFactoryActivtyRuns"></a> Представление, которое выполняет все действия фрагмента
Для среза может быть несколько выполнений. Например, если получить срез не удалось, служба может повторить попытку через некоторое время. Кроме того, если все попытки выполнить срез были неудачными, срез можно выполнить повторно. Можно просмотреть все действия выполняется на ** срез данных ** стоечный модуль в списке в нижней.

## <a name="DataFactoryActivtyRunDetails"></a> Просмотр сведений о выполнения действия
Щелкните действие запуска из списка запускается **срез данных** выноски для просмотра подробных сведений о действии выполнения.

![Сведения о выполнении действия][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Операции увеличения - события за прошлую неделю
В **ФАБРИКА данных** стоечный модуль (или Домашняя страница) для фабрики данных щелкните **события за прошлую неделю** в **операции** увеличения, чтобы просмотреть события за прошлую неделю. Благодаря этому можно получить общее представление об операциях, выполненных фабрикой данных на прошлой неделе. Это также поможет устранить любые ошибки, связанные с перемещением или обработкой данных.

![События фабрика данных][image-data-factory-events]


## См. также

Статья | Описание
------ | ---------------
[Монитор и управлять фабрика данных Azure, с помощью PowerShell][monitor-manage-using-powershell] | В этой статье описаны способы отслеживания фабрику данных Azure с помощью командлетов Azure PowerShell. 


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

<!---HONumber=GIT-SubDir--> 