<properties 
	pageTitle="Задания по масштабированию в Stream Analytics | Azure" 
	description="Узнайте, как масштабировать задания анализа потоков" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/10/2015" 
	ms.author="jgao"/>

# Задания по масштабированию в Azure Stream Analytics 

Узнайте, как вычислить *единицы потоковой передачи* для задания потоковой аналитики и масштабирования заданий потоковой аналитики с помощью настройки входных разделов, настройки определения запроса и определения единиц потоковой передачи.

Определение задания Azure Stream Analytics включает входы, запросы и выходы. Входами являются точки, откуда задания считывают данные из потока, выходами являются точки, куда направляются результаты задания, а запросом является способ преобразования входного потока.  Задание требует по крайней мере один источник потока входных данных. Входной источник потока данных может быть концентратором событий служебной шины Azure или хранилищем больших двоичных объектов Azure. Дополнительные сведения см. в разделах [Введение в Azure Stream Analytics][stream.analytics.introduction], [Начало работы с Azure Stream Analytics][stream.analytics.get.started] и [Руководство разработчика Azure Stream Analytics][stream.analytics.developer.guide]. 

Ресурс, доступный для обработки заданий Stream Analytics, измеряется единицей потоковой передачи. Для пропускной способности каждой единицы потоковой передачи можно указать не более 1 МБ в секунду. Каждое задание требует не менее одной единицы потоковой передачи, используемой по умолчанию для всех заданий. Можно задать до 12 единиц потоковой передачи для задания Stream Analytics с помощью портала управления Azure. Каждая подписка Azure может иметь до 12 единиц потоковой передачи для всех заданий в определенном регионе. Для увеличения единицы потоковой передачи для вашей подписки до 100 единиц свяжитесь со [службой поддержки Майкрософт][microsoft.support].

Число единиц потоковой передачи для использования в одном задании зависит от конфигурации раздела на входных данных и запроса, определенного для задания. В статье показано, как вычислить и настроить запрос для увеличения пропускной способности.

##Содержание
+ [Рассчитайте максимальное количество единиц потоковой передачи для задания](#calculate)
+ [Настройка раздела задания Stream Analytics](#configure)
+ [Мониторинг производительности задания Stream Analytics](#monitor)
+ [Дальнейшие действия](#nextstep)


##<a name="calculate"></a>Расчет максимального количества единиц потоковой передачи для задания
Общее число единиц потоковой передачи, которое можно использовать заданием Stream Analytics, зависит от числа шагов в запросе, определенных для задания, и количества разделов для каждого шага.

### Действия по выполнению запроса
Запрос может иметь один или несколько шагов. Каждый шаг является вложенным запросом, определенным с помощью ключевого слова WITH. Запрос, находящийся за пределами ключевого слова WITH, также учитывается в качестве шага. Например, оператор SELECT в следующем запросе:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

Предыдущий запрос имеет 2 шага. 

> [WACOM.NOTE] Этот образец запроса будет описан далее в этой статье.

### Разделы шага

Разделение шага требует наличия следующих условий.

- Источник входных данных должен быть разделен. См. [Руководство для разработчиков Azure Stream Analytics][stream.analytics.developer.guide] и [Руководство для разработчиков концентраторов событий Azure][azure.event.hubs.developer.guide].
- Инструкция SELECT запроса должна читаться из разделенного источника входных данных. 
- Запрос внутри действия должен включать ключевое слово Partition By 

Если запрос разделен, входные данные событий будут обработаны и объединены в отдельные группы секции, а выходные данные событий будут сгенерированы для каждой из групп. Если желательно иметь объединенный запрос, необходимо создать второй неразделенный шаг для объединения.

Предварительный выпуск Azure Stream Analytics не поддерживает разделение по названиям столбцов. Разделение можно выполнять только по полю PartitionId, это встроенное поле в запросе. Поле ParitionId указывает, из какой секции источника потока данных является событие.  Дополнительные сведения см. в разделе [Ограничения и известные проблемы Azure Stream Analytics][stream.analytics.limitations].

### Рассчитайте максимальное количество единиц потоковой передачи для задания

Все несекционированные вместе шаги можно масштабировать до 6 единиц потоковой передачи для задания Stream Analytics. Для добавления дополнительных единиц потоковой передачи данных шаг должен быть секционирован. Каждая секция может иметь 6 единиц потоковой передачи.

<table border="1">
<tr><th>Запрос задания</th><th>Максимальное количество единиц потоковой передачи для задания</th></td>

<tr><td>
<ul>
<li>Запрос содержит один шаг.</li>
<li>Шаг не секционирован.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Поток входных данных секционирован по 3.</li>
<li>Запрос содержит один шаг.</li>
<li>Шаг является секционированным.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>Запрос состоит из двух шагов.</li>
<li>Ни один из шагов не секционирован.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>Поток входных данных секционирован по 3.</li>
<li>Запрос состоит из двух шагов.</li>
<li>Инструкция SELECT считывает из секционированных входных данных.</li>
</ul>
</td>
<td>24 (18 для секционированного шага + 6 для несекционированного шага)</td></tr>
</table>

###Пример шкалы
Следующий запрос вычисляет количество машин, проходящих через пропускной пункт, с тремя пунктами для оплаты и пропускной способности 3 минуты для каждого пункта. Этот запрос может увеличиваться до 6 единиц потоковой передачи.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Чтобы использовать дополнительные единицы потоковой передачи для запроса, данные входного потока данных и запроса должны быть секционированы. При наличии раздела потока данных, равного 3, следующий измененный запрос может быть увеличен до 18 единиц потоковой передачи.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Если запрос разделен, входные данные событий будут обработаны и объединены в отдельные группы секции, а выходные данные событий будут сформированы для каждой из групп. Секционирование может вызвать некоторые непредвиденные результаты, если поле группы не является ключом раздела во входном потоке данных. Например, поле TollBoothId в предыдущем примере запроса не является ключом раздела Input1. Данные из пункта 1 могут быть распределены в нескольких секциях. Каждая из секций Input1 будет обрабатываться отдельно с помощью Stream Analytics, и будет создаваться несколько записей для автомобиля, проходящего через один и тот же пункт. В случае если нельзя изменить ключ раздела ввода, эту проблему можно устранить, добавив дополнительные несекционированные действия. Например: 

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Этот запрос можно увеличить до 24 единиц потоковой передачи. 

>[WACOM.NOTE] При объединении двух потоков убедитесь в том, что потоки разделены с помощью ключа раздела для объединяемого столбца и количество разделов в обоих потоках одинаковое.


##<a name="configure"></a>Настройка раздела задания Stream Analytics

**Чтобы настроить единицы потоковой передачи для задания**

1. Выполните вход на [портал управления][azure.management.portal].
2. Нажмите **Stream Analytics** слева.
3. Выберите задание Stream Analytics, которое необходимо расширить.
4. В верхней части страницы щелкните **МАСШТАБ**.

![Azure Stream Analytics configure job scale][img.Stream.Analytics.Configure.Scale]


##<a name="monitor"></a>Мониторинг производительности задания

На портале управления можно отслеживать пропускную способность задания событий в секунду:

![Azure Stream Analytics monitor jobs][img.Stream.Analytics.Monitor.job]
 
Рассчитайте ожидаемую пропускную способность рабочей нагрузки событий в секунду. В случае если пропускная способность меньше, чем ожидалось, выполните настройку входного раздела, настройте запрос и добавьте дополнительные единицы потоковой передачи для задания.

##<a name="nextstep"></a> Дальнейшие действия
В этой статье вы узнали, как рассчитать единицы потоковой передачи и масштабировать задания Stream Analytics. Чтобы узнать больше о Stream Analytics, см.:

- [Введение в Azure Stream Analytics][stream.analytics.introduction]
- [Начало использования Azure Stream Analytics][stream.analytics.get.started]
- [Руководство по Azure Stream Analytics для разработчиков][stream.analytics.developer.guide]
- [Ограничения Azure Stream Analytics и известные проблемы][stream.analytics.limitations]
- [Справочник по языку запросов для Azure Stream Analytics][stream.analytics.query.language.reference]
- [Справочник по REST API для управления Azure Stream Analytics ][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301



<!--HONumber=46--> 
