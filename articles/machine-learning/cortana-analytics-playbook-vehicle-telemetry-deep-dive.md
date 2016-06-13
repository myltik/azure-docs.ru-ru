<properties 
	pageTitle="Сборник тренировочных заданий по решению для аналитики телеметрии автомобиля. Подробный обзор решения | Microsoft Azure" 
	description="Используйте возможности Cortana Intelligence, чтобы получить прогнозы и актуальную информацию об исправности и манере вождения автомобиля в режиме реального времени." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="bradsev" />


# Сборник тренировочных заданий по решению для аналитики телеметрии автомобиля. Подробный обзор решения

В этом **меню** содержатся ссылки на разделы сборника тренировочных заданий:

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

В этом разделе подробно рассматривается каждый из этапов, входящих в архитектуру решения, с инструкциями и ссылками для настройки.

## Источники данных

Это решение предусматривает использование двух разных источников данных:

- **имитированные сигналы автомобиля и набор диагностических данных**; 
- **каталог автомобилей.**

В это решение входит симулятор телематики автомобиля. Оно выдает диагностическую информацию и сигналы, соответствующие состоянию автомобиля и манере вождения в определенный момент времени. Щелкните [здесь](http://go.microsoft.com/fwlink/?LinkId=717075), чтобы скачать **решение Visual Studio симулятора телематики автомобиля** для настройки в соответствии со своими требованиями. Каталог автомобилей содержит справочный набор данных для сопоставления идентификационного номера (VIN) с моделью автомобиля.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Рис. 2. Симулятор телематики автомобиля*

Это набор данных в формате JSON, который содержит следующую схему.

столбец | Описание | Значения   
 ------- | ----------- | ---------  
VIN | Случайно сгенерированный идентификационный номер автомобиля | Его можно получить из главного списка 10 000 случайно сгенерированных идентификационных номеров автомобилей
outsideTemperature | Наружная температура, при которой движется автомобиль | Случайно сгенерированное число от 0 до 100
engineTemperature | Температура двигателя автомобиля | Случайно сгенерированное число от 0 до 500
Speed | Частота вращения двигателя, с которой движется автомобиль | Случайно сгенерированное число от 0 до 100
fuel | Уровень топлива в автомобиле | Случайно сгенерированное число от 0 до 100 (указывает уровень топлива в процентах)
engineoil | Уровень моторного масла в автомобиле | Случайно сгенерированное число от 0 до 100 (указывает уровень моторного масла в процентах)
tirepressure | Давление в шинах автомобиля | Случайно сгенерированное число от 0 до 50 (указывает уровень давления в шинах в процентах)
odometer | Показания одометра автомобиля | Случайно сгенерированное число от 0 до 200 000
accelerator\_pedal\_position | Положение педали газа автомобиля | Случайно сгенерированное число от 0 до 100 (указывает уровень, соответствующий положению педали газа, в процентах)
parking\_brake\_status | Указывает, припаркован ли автомобиль | Значение true или false
headlamp\_status | Указывает, включена ли головная фара | Значение true или false
brake\_pedal\_status | Указывает, нажата ли педаль тормоза | Значение true или false
transmission\_gear\_position | Включенная передача коробки передач автомобиля | Состояния: first, second, third, fourth, fifth, sixth, seventh, eighth
ignition\_status | Указывает, движется ли автомобиль | Значение true или false
windshield\_wiper\_status | Указывает, включен ли стеклоочиститель ветрового стекла | Значение true или false
ABS | Указывает, включена ли антиблокировочная система тормозов | Значение true или false
Timestamp | Метка времени создания точки данных | Дата
City | Расположение автомобиля | В этом решении используется 4 города: Bellevue, Redmond, Sammamish, Seattle


Справочный набор данных модели автомобиля позволяет сопоставить VIN с моделью.

VIN | Модель |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Седан |
8J0U8XCPRGW4Z3NQE | Гибридная среда |
WORG68Z2PLTNZDBI7 | Семейный седан |
JTHMYHQTEPP4WBMRN | Седан |
W9FTHG27LZN1YWO0Y | Гибридная среда |
MHTP9N792PHK08WJM | Семейный седан |
EI4QXI2AXVQQING4I | Седан |
5KKR2VB4WHQH97PF8 | Гибридная среда |
W9NSZ423XZHAONYXB | Семейный седан |
26WJSGHX4MA5ROHNL | Автомобиль с откидным верхом |
GHLUB6ONKMOSI7E77 | Автомобиль с кузовом «универсал» |
9C2RHVRVLMEJDBXLP | Малогабаритный автомобиль |
BRNHVMZOUJ6EOCP32 | Небольшой внедорожник |
VCYVW0WUZNBTM594J | Спортивный автомобиль |
HNVCE6YFZSA5M82NY | Внедорожник среднего размера |
4R30FOR7NUOBL05GJ | Автомобиль с кузовом «универсал» |
WYNIIY42VKV6OQS1J | Большой внедорожник |
8Y5QKG27QET1RBK7I | Большой внедорожник |
DF6OX2WSRA6511BVG | Двухместный легковой автомобиль |
Z2EOZWZBXAEW3E60T | Седан |
M4TV6IEALD5QDS3IR | Гибридная среда |
VHRA1Y2TGTA84F00H | Семейный седан |
R0JAUHT1L1R3BIKI0 | Седан |
9230C202Z60XX84AU | Гибридная среда |
T8DNDN5UDCWL7M72H | Семейный седан |
4WPYRUZII5YV7YA42 | Седан |
D1ZVY26UV2BFGHZNO | Гибридная среда |
XUF99EW9OIQOMV7Q7 | Семейный седан
8OMCL3LGI7XNCC21U | Автомобиль с откидным верхом |
……. | |


### Создание имитированных данных
1.	Щелкните стрелку в правом верхнем углу узла «Симулятор телематики автомобиля», чтобы загрузить пакет для симуляции данных. Сохраните файлы на локальном компьютере и извлеките их. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *Рис. 3. Проект внедрения решения для аналитики телеметрии автомобиля*

2.	На локальном компьютере перейдите в папку с извлеченным пакетом симулятора телематики автомобиля. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*Рис. 4. Папка симулятора телематики автомобиля*

3.	Запустите приложение **CarEventGenerator.exe**.

### Ссылки

[Решение Visual Studio "Симулятор телематики автомобиля"](http://go.microsoft.com/fwlink/?LinkId=717075)

[Концентратор событий Azure](https://azure.microsoft.com/services/event-hubs/)

[Фабрика данных Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## Прием
Для принятия сигналов автомобиля, событий диагностики, анализа в режиме реального времени и пакетного анализа используется сочетание концентраторов событий Azure, Stream Analytics и фабрики данных. Все эти компоненты создаются и настраиваются как часть развертывания решения.

### Анализ в режиме реального времени
События, создаваемые симулятором телематики автомобиля, публикуются в концентраторе событий с помощью пакета SDK для концентратора событий. Задание Stream Analytics принимает эти события из концентратора событий и обрабатывает данные в режиме реального времени для анализа работоспособности автомобиля.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*Рис. 5. Панель мониторинга концентратора событий*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*Рис. 6. Обработка данных заданием Stream Analytics*

Задание Stream Analytics принимает данные из концентратора событий, объединяет их со справочными данными для сопоставления VIN автомобиля с соответствующей моделью и сохраняет эти данные в хранилище BLOB-объектов для расширенного пакетного анализа. Указанный ниже запрос Stream Analytics используется для хранения данных в хранилище BLOB-объектов Azure.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*Рис. 7. Запрос задания Stream Analytics для приема данных*

### Пакетный анализ
Для расширенного пакетного анализа необходимо создать дополнительное количество имитированных сигналов автомобиля и набор диагностических данных. Это необходимо для предоставления достаточного объема репрезентативных данных для пакетной обработки. С этой целью в рабочем процессе фабрики данных Azure используется конвейер PrepareSampleDataPipeline для создания имитированных сигналов автомобиля и набора диагностических данных за год. Щелкните [здесь](http://go.microsoft.com/fwlink/?LinkId=717077), чтобы скачать решение Visual Studio настраиваемого действия DotNet фабрики данных для настройки в соответствии со своими требованиями.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*Рис. 8. Подготовка образца данных для рабочего процесса пакетной обработки*

Конвейер состоит из настраиваемого действия .Net фабрики данных Azure, указанного ниже:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*Рис. 9. PrepareSampleDataPipeline*

После того как конвейер успешно выполнит это действие и набор данных RawCarEventsTable будет помечен как Ready, создаются имитированные сигналы автомобиля и диагностические данные за год. Затем в вашей учетной записи хранения в контейнере connectedcar будут созданы следующие папка и файл.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*Рис. 10. Вывод PrepareSampleDataPipeline*

### Ссылки

[Пакет SDK концентратора событий Azure для приема потока](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Возможности перемещения данных фабрики данных Azure](../data-factory/data-factory-data-movement-activities.md) [Действие DotNet фабрики данных Azure](../data-factory/data-factory-use-custom-activities.md)

[Решение Visual Studio "Действие DotNet фабрики данных Azure" для подготовки примера данных](http://go.microsoft.com/fwlink/?LinkId=717077)


## Подготовка.
>[AZURE.ALERT] Этот этап решения применяется только для пакетной обработки.

На этапе подготовки данных необработанные полуструктурированные сигналы автомобиля и набор диагностических данных секционируются по годам и месяцам для эффективного выполнения запросов и долговременного масштабируемого хранения (*другими словами* это позволяет переходить от одной учетной записи хранения больших двоичных объектов к другой после заполнения первой). Выходные данные (с меткой *PartitionedCarEventsTable*) в течение длительного периода времени хранятся в виде фундаментальных данных или данных rawest в озере данных заказчика. Входные данные для этого конвейера обычно отклоняются, так как выходные данные полностью с ними совпадают — они лучше хранятся (секционируются) для последующего использования.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Рис. 11. Рабочий процесс секционирования событий автомобиля*

Необработанные данные секционируются с помощью действия Hive HDInsight в PartitionCarEventsPipeline. Имитированные данные за год, созданные на первом этапе, секционируются по годам и месяцам для создания секций сигналов автомобиля и диагностических данных, соответствующих каждому месяцу года (всего 12 секций).

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Рис. 12. PartitionCarEventsPipeline*

Указанный ниже сценарий Hive с именем partitioncarevents.hql используется для секционирования и расположен в папке \\demo\\src\\connectedcar\\scripts скачанного ZIP-файла.

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Рис. 13. Сценарий Hive PartitionConnectedCarEvents*

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Рис. 14. Секционированные выходные данные*

Теперь данные оптимизированы, более управляемы и готовы для дальнейшей обработки и выполнения расширенного пакетного анализа.

## Анализ данных

В этом разделе рассматривается использование сочетания Azure Stream Analytics, Машинного обучения Azure, фабрики данных Azure и Azure HDInsight для расширенного анализа работоспособности и манер вождения автомобиля. Этот раздел состоит из 3 подразделов.

1.	**Машинное обучение**. В этом подразделе содержатся сведения об эксперименте обнаружения аномалий, проведенном в этом решении для прогнозирования необходимости технического обслуживания и отзывов из-за проблем безопасности для автомобилей.
2.	**Анализ в режиме реального времени**. В этом подразделе содержатся сведения об аналитике в режиме реального времени с использованием языка запросов Stream Analytics и выполнения эксперимента машинного обучения в режиме реального времени с помощью пользовательского приложения.
3.	**Пакетный анализ**. В этом подразделе содержатся сведения о преобразовании и обработке данных пакета с помощью Azure HDInsight и Машинного обучения Azure, используемых в фабрике данных Azure.

### Машинное обучение

Здесь наша цель заключается в прогнозировании того, для каких автомобилей необходимо провести обслуживание или какие из них нужно отозвать на основе определенных статистических данных о работоспособности. Предположим следующее.

- **Техническое обслуживание** автомобиля требуется при одном из условий:
	- низкое давление в шине;
	- низкий уровень моторного масла;
	- высокая температура двигателя.

- Может возникнуть **проблема безопасности** автомобиля, что требует **отзыва** при одном из условий:
	- высокая температура двигателя, но низкая наружная температура;
	- низкая температура двигателя, но высокая наружная температура.

Чтобы выявить аномалии, мы создали две отдельные модели на основе условий выше: для определения необходимости в обслуживании и необходимости в отзыве автомобиля. Для обнаружения аномалий обе модели используют встроенный алгоритм анализа главных компонентов.

**Модель определения необходимости в обслуживании.** Эта модель сообщает об аномалии, если один из трех показателей, а именно давление в шинах, уровень моторного масла или температура двигателя, отвечает соответствующему условию. Поэтому при создании модели необходимо учесть только эти три переменные. В эксперименте Машинного обучения Azure сначала мы используем модуль **Столбцы проекта**, чтобы извлечь эти три переменные. Затем мы используем модуль обнаружения аномалий на основе алгоритма анализа главных компонентов, чтобы создать модель обнаружения аномалий.

Анализ главных компонентов — это общепринятый метод машинного обучения, который можно использовать при выборе признаков, классификации и обнаружении аномалий. Этот тип анализа предусматривает преобразование набора сценариев с предположительно коррелированными переменными в набор значений, которые называются основными компонентами. Основная идея моделирования на основе анализа главных компонентов заключается в том, чтобы проецировать данные в пространстве меньшей размерности, упростив определение признаков и аномалий.
 
При обнаружении аномалий для каждого нового входного фрагмента данных сначала рассчитывается проекция на основе собственных векторов, а затем вычисляется нормализованная ошибка восстановления. Нормализованная ошибка представляет собой показатель аномалии. Чем больше значение ошибки, тем больше аномалий в экземпляре.

При обнаружении необходимости в обслуживании каждую запись можно рассматривать в качестве точки в трехмерном пространстве. Ее определяют показатели давления в шинах, уровня моторного масла и температуры двигателя. Чтобы выявить эти аномалии, можно проецировать исходные данные трехмерного пространства в двухмерном пространстве, используя анализ главных компонентов. Поэтому мы установим значение «2» для параметра количества компонентов в анализе. Этот параметр играет важную роль в обнаружении аномалий на основе анализа главных компонентов. Спроецировав данные с использованием этого анализа, определить соответствующие аномалии намного проще.

**Модель обнаружения аномалий для отзыва.** Эта модель предусматривает аналогичное использование модулей «Столбцы проекта» и обнаружения аномалий на основе анализа главных компонентов. В этой модели сначала нужно извлечь три переменные — температуру двигателя, наружную температуру и скорость, используя модуль **Столбцы проекта**. Мы также учитываем переменную скорости, так как температура двигателя обычно зависит от скорости. Далее мы используем модуль обнаружения аномалий на основе анализа главных компонентов, чтобы проецировать данные трехмерного пространства в двухмерном пространстве. Если температура двигателя и наружная температура подверглись сильной отрицательной корреляции, значит автомобиль отвечает условиям для отзыва и его необходимо отозвать. Алгоритм обнаружения аномалий на основе анализа главных компонентов позволяет выявлять аномалии после выполнения соответствующего анализа.

Обратите внимание, что при обучении любой модели в качестве входных данных необходимо использовать обычные данные, полученные в условиях, когда не требуется обслуживание или отзыв, как и при обучении модели обнаружения аномалий на основе анализа главных компонентов. В оценивающем эксперименте мы используем обученную модель обнаружения аномалий, чтобы определить, нужно ли провести обслуживание или отозвать автомобиль.


### Анализ в режиме реального времени

Следующий SQL-запрос Stream Analytics используется, чтобы получить средние значения таких важных параметров автомобиля, как скорость, уровень топлива, температура двигателя, показания одометра, давление в шинах, уровень моторного масла, для обнаружения аномалий, выдачи предупреждений и определения общих условий работоспособности автомобилей в определенном регионе, а также сопоставить их с демографическими данными.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Рис. 15. Запрос Stream Analytics для обработки в режиме реального времени

Все средние значения вычисляются в 3-секундном окне TumblingWindow. Мы применили TubmlingWindow, так как необходимо использовать непересекающиеся последовательные интервалы времени.

Дополнительные сведения обо всех возможностях окон в Azure Stream Analytics см. [здесь](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Прогнозирование в режиме реального времени**

Для ввода в эксплуатацию модели машинного обучения в режиме реального времени в решение включено отдельное приложение. Оно называется RealTimeDashboardApp. Это приложение создано и настроено как часть развертывания решения. RealTimeDashboardApp выполняет такие задачи.

1.	Прослушивает экземпляр концентратора событий, где Stream Analytics постоянно публикует события. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Рис. 16. Запрос Stream Analytics для публикации данных в выходном экземпляре концентратора событий* 

2.	Для каждого события, которое получает это приложение:

	- обрабатывает данные с использованием конечной точки оценки «запрос-ответ» (RRS) системы машинного обучения, при этом публикация конечной точки происходит автоматически в рамках развертывания;
	- выходные данные RRS публикуются в наборе данных Power BI при помощи API push-уведомлений.

Этот шаблон также можно применять в сценариях, когда нужно интегрировать бизнес-приложение с потоком анализа в режиме реального времени для предупреждений, уведомлений, сообщений и т. д.

Щелкните [здесь](http://go.microsoft.com/fwlink/?LinkId=717078), чтобы скачать решение Visual Studio RealtimeDashboardApp для настройки.

**Чтобы запустить приложение панели мониторинга в реальном времени:**

1.	Щелкните узел PowerBI в представлении схемы и на панели свойств перейдите по ссылке "Скачать приложение панели мониторинга в реальном времени". ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *Рис. 17. Указания по настройке панели мониторинга Power BI*
2.	Извлеките его и сохраните локально. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Рис. 18. Папка RealtimeDashboardApp*
3.	Запустите приложение RealtimeDashboardApp.exe.
4.	Укажите действительные учетные данные Power BI, войдите и нажмите кнопку «Принять».![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Рис. 19. RealtimeDashboardApp: вход в Power BI*

>[AZURE.NOTE] Примечание. Если вы хотите очистить набор данных Power BI, запустите RealtimeDashboardApp с использованием параметра flushdata:

	RealtimeDashboardApp.exe -flushdata

### Пакетный анализ

Цель этого задания состоит в том, чтобы продемонстрировать, как Contoso Motors использует вычислительные возможности Azure для работы с большими данными и получения исчерпывающих сведений о шаблоне вождения, манере использования и работоспособности автомобиля с целью:

- улучшить обслуживание клиентов и снизить его цену, предоставив им сведения о манерах вождения, в том числе тех, которые позволяют оптимизировать расход топлива;
- заранее изучить данные о клиентах и их манеры вождения, чтобы принимать правильные бизнес-решения и обеспечивать предоставление лучших в своем классе продуктов и услуг.

В этом решении необходимо получить такие метрики.

1.	**Агрессивная манера вождения** — определяет характеристики модели, расположение, условия вождения и время, чтобы предоставить подробные сведения о шаблоне агрессивного вождения. Contoso Motors сможет использовать ее в маркетинговых кампаниях, чтобы предлагать новые персонализированные функции и умное страхование.
2.	**Манера вождения для экономии топлива** — определяет характеристики модели, расположение, условия вождения и время, чтобы предоставить подробные данные о шаблонах вождения, позволяющих экономить топливо. Contoso Motors сможет использовать ее, чтобы предлагать новые функции, предоставляя сведения о том, как водить с учетом экономии и не вредить окружающей среде. 
3.	**Модели определения необходимости в отзыве** — определяет необходимость отзыва модели с помощью эксперимента по выявлению аномалий в рамках машинного обучения.

Рассмотрим эти метрики подробнее.


**Шаблон агрессивного вождения**

Секционированные сигналы автомобиля и диагностические данные обрабатываются в конвейере AggresiveDrivingPatternPipeline. При этом используется сценарий Hive, чтобы определить модель, расположение автомобиля, условия вождения и другие параметры, которые характерны для агрессивного вождения.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *Рис. 20. Рабочий процесс для шаблона агрессивного вождения*

Сценарий Hive aggresivedriving.hql, который используется для анализа шаблона агрессивного вождения, находится в скачанном ZIP-файле в папке \\demo\\src\\connectedcar\\scripts.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Рис. 21. Запрос Hive для шаблона агрессивного вождения*

В нем учитывается включенная передача, положение педали тормоза и скорость автомобиля, которые позволяют идентифицировать неосторожное или агрессивное поведение во время вождения на основе шаблона торможения при высокой скорости.

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*Рис. 22. Выходные данные AggressiveDrivingPatternPipeline*


**Шаблон вождения для экономии топлива**

Секционированные сигналы автомобиля и диагностические данные обрабатываются в конвейере FuelEfficientDrivingPatternPipeline. При этом используется сценарий Hive, чтобы определить модель, расположение автомобиля, условия вождения и другие параметры, которые характерны для вождения, позволяющего оптимально расходовать топливо.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*Рис. 23. Рабочий процесс для шаблона вождения для экономии топлива*

Сценарий Hive fuelefficientdriving.hql, который используется для анализа шаблона агрессивного вождения, находится в скачанном ZIP-файле в папке \\demo\\src\\connectedcar\\scripts.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Рис. 24. Запрос Hive для шаблона вождения, позволяющего экономить топливо*

В нем учитывается включенная передача, положение педали тормоза, педали газа и скорость автомобиля, которые позволяют определить манеру вождения, позволяющую экономить топливо, на основе шаблонов ускорения, торможения и скорости.

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*Рис. 25. Выходные данные FuelEfficientDrivingPatternPipeline*


**Прогнозы для отзыва**

Эксперимент по машинному обучению подготавливается и публикуется в качестве веб-службы в рамках развертывания решения. В этом рабочем процессе используется конечная точка пакетной оценки, регистрируемая в качестве службы, связанной с фабрикой данных. Для ввода в эксплуатацию используется пакетная оценка в фабрике данных.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*Рис. 26. Конечная точка машинного обучения, зарегистрированная в качестве связанной службы в фабрике данных*

Зарегистрированная связанная служба используется в DetectAnomalyPipeline для оценки данных с использованием модели обнаружения аномалий.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*Рис. 27. Пакетная оценка в фабрике данных в рамках Машинного обучения Azure*

В контейнере выполняется несколько шагов по подготовке данных, чтобы их можно было использовать в веб-службе пакетной оценки.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*Рис. 28. DetectAnomalyPipeline для прогнозирования необходимости в отзыве автомобилей*

По завершении оценки выполняется действие HDInsight по обработке и агрегации данных, которые с помощью модели отнесены к категории аномалий с вероятностью 0,6 или выше.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'

*Рис. 29. Запрос Hive на агрегацию отзывов*

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*Рис. 30. Выходные данные DetectAnomalyPipeline*


## Опубликовать

### Анализ в режиме реального времени

Один из запросов в задании Stream Analytics предусматривает публикацию событий в выходном экземпляре концентратора событий.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Рис. 31. Публикация данных задания Stream Analytics в выходном экземпляре концентратора событий*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Рис. 32. Запрос Stream Analytics на публикацию в выходном экземпляре концентратора событий*

Этот поток событий использует приложение RealTimeDashboardApp, которое входит в состав решения. Это приложение использует веб-службу машинного обучения типа «запрос-ответ» для оценки в режиме реального времени и публикует результаты в наборе данных Power BI.

### Пакетный анализ

Результаты пакетной обработки и обработки в режиме реального времени публикуются в таблицах Базы данных SQL Azure. Сервер, База данных и таблицы SQL Azure создаются автоматически при настройке.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Рис. 33. Копирование результатов пакетной обработки в рабочий процесс киоска данных*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Рис. 34. Публикация данных задания Stream Analytics в киоске данных*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Рис. 35. Настройка киоска данных в задании Stream Analytics*


## Использование

Power BI предоставляет для этого решения расширенную панель мониторинга, предназначенную для визуализации данных в режиме реального времени и прогнозной аналитики.

Чтобы получить подробные инструкции по настройке отчетов Power BI и панели мониторинга, щелкните здесь. Окончательная панель мониторинга выглядит так:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Рис. 36. Панель мониторинга Power BI*

## Сводка

Этот документ содержит подробные сведения о решении для аналитики телеметрии автомобилей. Здесь показан шаблон лямбда-архитектуры для анализа в режиме реального времени и пакетного анализа с прогнозами и действиями. Этот шаблон подходит для самых разнообразных сценариев использования, в которых требуется использование «горячего» (в режиме реального времени) и «холодного» (пакетного) анализа.

<!---HONumber=AcomDC_0601_2016-->