---
title: Подробный обзор прогнозирования исправности автомобиля и манеры вождения с помощью Azure | Документация Майкрософт
description: Используйте возможности Cortana Intelligence, чтобы получить прогнозы и актуальную информацию об исправности и манере вождения автомобиля в режиме реального времени.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 10fe87757a6da8a64e4fbd7fb624fef3e666714c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Сборник тренировочных заданий по решению для аналитики телеметрии автомобиля. Подробный обзор решения
В этом меню содержатся ссылки на разделы сборника тренировочных заданий: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

В этом документе детально рассматривается каждый этап, входящий в архитектуру решения, с инструкциями и ссылками для настройки. 

## <a name="data-sources"></a>Источники данных
Это решение предусматривает использование двух разных источников данных:

* имитированные сигналы автомобиля и набор диагностических данных;
* каталог автомобилей.

В это решение входит симулятор телематики автомобиля, как показано на снимке экрана ниже. Он выдает диагностическую информацию и сигналы, соответствующие состоянию автомобиля и манере вождения в определенный момент времени. Чтобы скачать решение Visual Studio симулятора телематики автомобиля для настройки в соответствии со своими требованиями, щелкните [эту ссылку](http://go.microsoft.com/fwlink/?LinkId=717075). Каталог автомобилей содержит эталонный набор данных, который сопоставляет идентификационные номера автомобилей (VIN) с их моделями.

![Симулятор телематики автомобиля](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Это набор данных в формате JSON, содержащий следующую схему.

| столбец | ОПИСАНИЕ | Значения |
| --- | --- | --- |
| VIN |Случайно сгенерированный идентификационный номер автомобиля |Его можно получить из главного списка 10 000 случайно сгенерированных идентификационных номеров автомобилей |
| outsideTemperature |Наружная температура, при которой движется автомобиль |Случайно сгенерированное число от 0 до 100 |
| engineTemperature |Температура двигателя автомобиля |Случайно сгенерированное число от 0 до 500 |
| Speed |Частота вращения двигателя, с которой движется автомобиль |Случайно сгенерированное число от 0 до 100 |
| fuel |Уровень топлива в автомобиле |Случайно сгенерированное число от 0 до 100 (указывает уровень топлива в процентах) |
| engineoil |Уровень моторного масла в автомобиле |Случайно сгенерированное число от 0 до 100 (указывает уровень моторного масла в процентах) |
| Давление в шинах |Давление в шинах автомобиля |Случайно сгенерированное число от 0 до 50 (указывает уровень давления в шинах в процентах) |
| odometer |Показания одометра автомобиля |Случайно сгенерированное число от 0 до 200 000 |
| accelerator_pedal_position |Положение педали газа автомобиля |Случайно сгенерированное число от 0 до 100 (указывает уровень, соответствующий положению педали газа, в процентах) |
| parking_brake_status |Указывает, припаркован ли автомобиль |Значение true или false |
| headlamp_status |Указывает, включена ли головная фара |Значение true или false |
| brake_pedal_status |Указывает, нажата ли педаль тормоза |Значение true или false |
| transmission_gear_position |Включенная передача коробки передач автомобиля |Состояния: first, second, third, fourth, fifth, sixth, seventh, eighth |
| ignition_status |Указывает, движется ли автомобиль |Значение true или false |
| windshield_wiper_status |Указывает, включен ли стеклоочиститель ветрового стекла |Значение true или false |
| ABS |Указывает, включена ли антиблокировочная система тормозов |Значение true или false |
| Timestamp |Метка времени создания точки данных |Дата |
| City |Расположение автомобиля |В этом решении представлены четыре города: Бельвью, Редмонд, Саммамиш и Сиэтл |

Эталонный набор данных модели автомобиля сопоставляет VIN-номера и модели. 

| VIN | Модель |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Седан |
| 8J0U8XCPRGW4Z3NQE |Гибридная среда |
| WORG68Z2PLTNZDBI7 |Семейный седан |
| JTHMYHQTEPP4WBMRN |Седан |
| W9FTHG27LZN1YWO0Y |Гибридная среда |
| MHTP9N792PHK08WJM |Семейный седан |
| EI4QXI2AXVQQING4I |Седан |
| 5KKR2VB4WHQH97PF8 |Гибридная среда |
| W9NSZ423XZHAONYXB |Семейный седан |
| 26WJSGHX4MA5ROHNL |Автомобиль с откидным верхом |
| GHLUB6ONKMOSI7E77 |Микроавтобус |
| 9C2RHVRVLMEJDBXLP |Малогабаритный автомобиль |
| BRNHVMZOUJ6EOCP32 |Небольшой внедорожник |
| VCYVW0WUZNBTM594J |Спортивный автомобиль |
| HNVCE6YFZSA5M82NY |Внедорожник среднего размера |
| 4R30FOR7NUOBL05GJ |Микроавтобус |
| WYNIIY42VKV6OQS1J |Большой внедорожник |
| 8Y5QKG27QET1RBK7I |Большой внедорожник |
| DF6OX2WSRA6511BVG |Двухместный легковой автомобиль |
| Z2EOZWZBXAEW3E60T |Седан |
| M4TV6IEALD5QDS3IR |Гибридная среда |
| VHRA1Y2TGTA84F00H |Семейный седан |
| R0JAUHT1L1R3BIKI0 |Седан |
| 9230C202Z60XX84AU |Гибридная среда |
| T8DNDN5UDCWL7M72H |Семейный седан |
| 4WPYRUZII5YV7YA42 |Седан |
| D1ZVY26UV2BFGHZNO |Гибридная среда |
| XUF99EW9OIQOMV7Q7 |Семейный седан |
| 8OMCL3LGI7XNCC21U |Автомобиль с откидным верхом |
| ……. | |

## <a name="ingestion"></a>Прием
Для принятия сигналов автомобиля, событий диагностики, анализа в режиме реального времени и пакетного анализа используется сочетание концентраторов событий Azure, Azure Stream Analytics и фабрики данных Azure. Все эти компоненты создаются и настраиваются как часть развертывания решения. 

### <a name="real-time-analysis"></a>Анализ в режиме реального времени
События, создаваемые симулятором телематики автомобиля, публикуются в концентраторе событий с помощью пакета SDK для концентратора событий.  

![Панель мониторинга концентратора событий](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Задание Stream Analytics принимает эти события из концентратора событий и обрабатывает данные в режиме реального времени для анализа работоспособности автомобиля.

![Обработка данных заданием Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Задание Stream Analytics:

* принимает данные из концентратора событий;
* объединяет их с эталонными данными для сопоставления VIN-номера автомобиля с соответствующей моделью; 
* сохраняет эти данные в хранилище BLOB-объектов для расширенного пакетного анализа. 

Следующий запрос Stream Analytics используется для хранения данных в хранилище BLOB-объектов. 

![Запрос задания Stream Analytics для приема данных](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Пакетный анализ
Для расширенного пакетного анализа необходимо создать дополнительное количество имитированных сигналов автомобиля и набор диагностических данных. Дополнительное количество необходимо для предоставления достаточного объема репрезентативных данных для пакетной обработки. С этой целью в рабочем процессе фабрики данных используется PrepareSampleDataPipeline для создания имитированных сигналов автомобиля и набора диагностических данных за год. Чтобы скачать решение Visual Studio настраиваемого действия .NET фабрики данных для настройки в соответствии со своими требованиями, перейдите на веб-страницу [Документация по Машинному обучению Azure (предварительная версия)](http://go.microsoft.com/fwlink/?LinkId=717077). 

Этот рабочий процесс показывает образец данных, подготовленный для пакетной обработки.

![Образец данных, подготовленный для рабочего процесса пакетной обработки](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Конвейер состоит из настраиваемого действия .NET фабрики данных.

![Действие PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

После того как конвейер успешно выполнит это действие и набор данных RawCarEventsTable будет помечен как Ready, создаются имитированные сигналы автомобиля и диагностические данные за год. Затем в вашей учетной записи хранения в контейнере connectedcar будут созданы следующие папка и файл:

![Выходные данные PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Секционирование набора данных
На этапе подготовки данных необработанные полуструктурированные сигналы автомобиля и набор диагностических данных секционируются по годам и месяцам. Секционирование обеспечивает более эффективное выполнение запросов и долговременное масштабируемое хранение, позволяя выполнять переход. Например, оно позволяет переходить от одной учетной записи хранения больших двоичных объектов к другой после заполнения первой. 

>[!NOTE] 
>Этот этап решения применяется только для пакетной обработки.

Управление входными и выходными данными:

* **Выходные данные** (с меткой PartitionedCarEventsTable) в течение длительного периода времени хранятся в виде фундаментальных данных или данных rawest в Data Lake клиента. 
* **Входные данные** для этого конвейера обычно отклоняются, так как выходные данные полностью с ними совпадают. Они лучше хранятся (секционируются) для последующего использования.

Рабочий процесс секционирования событий автомобиля.

![Рабочий процесс секционирования событий автомобиля](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Необработанные данные секционируются с помощью действия Hive Azure HDInsight в PartitionCarEventsPipeline, как показано на снимке экрана ниже. Образец данных, созданный для параметра года на этапе подготовки данных, секционируется по годам и месяцам. Секции используются для создания сигналов транспортного средства и диагностических данных для каждого месяца (всего 12 секций). 

![Действие PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Скрипт Hive PartitionConnectedCarEvents**

Скрипт Hive partitioncarevents.hql используется для секционирования. Он расположен в папке \demo\src\connectedcar\scripts скачанного ZIP-файла. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
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

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar:

![Секционированные выходные данные](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Теперь данные оптимизированы, более управляемы и готовы для дальнейшей обработки и выполнения расширенного пакетного анализа. 

## <a name="data-analysis"></a>Анализ данных
В этом разделе рассматривается использование сочетания Stream Analytics, Машинного обучения Azure, фабрики данных и HDInsight для расширенного анализа работоспособности и манер вождения автомобиля.

### <a name="machine-learning"></a>Машинное обучение
Здесь наша цель заключается в прогнозировании того, для каких автомобилей необходимо провести обслуживание или какие из них нужно отозвать на основе определенных статистических данных о работоспособности, исходя из следующих предположений:

* Техническое обслуживание автомобиля требуется при одном из следующих условий:
  
  * низкое давление в шине;
  * низкий уровень моторного масла;
  * высокая температура двигателя.

* Если выполняется хотя бы одно из следующих условий, может возникнуть проблема безопасности автомобиля, что требует отзыва:
  
  * высокая температура двигателя, но низкая наружная температура;
  * низкая температура двигателя, но высокая наружная температура.

Чтобы выявить аномалии, используются две отдельные модели на основе предыдущих условий. Одна модель для определения необходимости в обслуживании автомобиля, а вторая для определения необходимости в отзыве автомобиля. Для обнаружения аномалий обе модели используют встроенный алгоритм анализа главных компонентов. 

#### <a name="maintenance-detection-model"></a>**Модель определения необходимости в обслуживании**

Эта модель сообщает об аномалии, если один из трех показателей, а именно давление в шинах, уровень моторного масла или температура двигателя, отвечает соответствующему условию. Поэтому при создании модели необходимо учесть только эти три переменные. В эксперименте в машинном обучении модуль **Select Columns in Dataset** (Выбор столбцов в наборе данных) используется, чтобы извлечь эти три переменные. Затем используется модуль обнаружения аномалий на основе алгоритма анализа главных компонентов, чтобы создать модель обнаружения аномалий. 

Анализ главных компонентов (PCA)— это общепринятый метод машинного обучения, который можно использовать при выборе признаков, классификации и обнаружении аномалий. Этот тип анализа предусматривает преобразование набора сценариев с предположительно коррелированными переменными в набор значений, которые называются основными компонентами. Основная идея моделирования на основе анализа главных компонентов заключается в том, чтобы проецировать данные в пространстве меньшей размерности, упростив определение признаков и аномалий.

Для каждого нового фрагмента входных данных в модели обнаружения сначала рассчитывается проекция на основе собственных векторов, а затем вычисляется нормализованная ошибка восстановления. Нормализованная ошибка представляет собой показатель аномалии. Чем больше значение ошибки, тем больше аномалий в экземпляре. 

При обнаружении необходимости в обслуживании каждая запись рассматривается в качестве точки в трехмерном пространстве. Ее определяют показатели давления в шинах, уровня моторного масла и температуры двигателя. Для выявления этих аномалий используется анализ главных компонентов, чтобы проецировать исходные данные трехмерного пространства в двухмерном пространстве. Поэтому для параметра количества компонентов в анализе установлено значение 2. Этот параметр играет важную роль в обнаружении аномалий на основе анализа главных компонентов. Спроецировав данные с использованием этого анализа, определить соответствующие аномалии намного проще.

#### <a name="recall-anomaly-detection-model"></a>**Модель обнаружения аномалий для отзыва**

Эта модель предусматривает аналогичное использование модулей **Select Columns in Datase**t (Выбор столбцов в наборе данных) и PCA-based anomaly detection (Обнаружение аномалий на основе анализа главных компонентов). В частности, сначала нужно извлечь три переменные — температуру двигателя, наружную температуру и скорость, используя модуль **Select Columns in Dataset** (Выбор столбцов в наборе данных). Переменная скорости также учитывается, так как температура двигателя обычно зависит от скорости. Далее используется модуль обнаружения аномалий на основе анализа главных компонентов, чтобы проецировать данные трехмерного пространства в двухмерном пространстве. Условия для отзыва соблюдены. Если температура двигателя и наружная температура подверглись сильной отрицательной корреляции, значит автомобиль необходимо отозвать. После выполнения анализа главных компонентов алгоритм обнаружения аномалий на основе этого анализа позволяет выявлять аномалии. 

При обучении любой модели в качестве входных данных используются обычные данные для обучения модели обнаружения аномалий на основе главных компонентов. (Для обычных данных не требуется обслуживание или отзыв.) В оценивающем эксперименте используется обученная модель обнаружения аномалий, чтобы определить, нужно ли провести обслуживание или отозвать автомобиль. 

### <a name="real-time-analysis"></a>Анализ в режиме реального времени
Указанный ниже SQL-запрос Stream Analytics используется, чтобы получить средние значения всех важных параметров автомобиля. К этим параметрам относятся: скорость, уровень топлива, температура двигателя, показания одометра, давление в шинах, уровень моторного масла и т. д. Средние значения используются для обнаружения аномалий, выдачи предупреждений и определения общих условий работоспособности автомобилей в определенном регионе. Затем они сопоставляются с демографическими данными. 

![Запрос Stream Analytics для обработки в реальном времени](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Все средние значения вычисляются в 3-секундном "переворачивающемся" окне. Мы применили "переворачивающееся" окно, так как необходимо использовать непересекающиеся последовательные интервалы времени. 

Дополнительные сведения о возможностях окон в Stream Analytics см. [здесь](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Прогнозирование в режиме реального времени**

Для ввода в эксплуатацию модели машинного обучения в режиме реального времени в решение включено отдельное приложение. Приложение RealTimeDashboardApp создано и настроено как часть развертывания решения. Характеристики этого приложения:

* Прослушивает экземпляр концентратора событий, в котором Stream Analytics непрерывно публикует события.

    ![Запрос Stream Analytics на публикацию данных](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Получает события. Для каждого события, которое получает это приложение: 
   
   * данные обрабатываются с использованием конечной точки оценки "запрос и ответ" (RRS) системы машинного обучения, при этом публикация конечной точки происходит автоматически в рамках развертывания;
   * выходные данные RRS публикуются в наборе данных Power BI при помощи API push-уведомлений.

Этот шаблон также можно применять в сценариях, когда нужно интегрировать бизнес-приложение с потоком анализа в режиме реального времени. К этим сценариям относятся предупреждение, уведомление и сообщение.

Чтобы скачать решение Visual Studio RealtimeDashboardApp для настройки, щелкните [здесь](http://go.microsoft.com/fwlink/?LinkId=717078). 

#### <a name="execute-the-real-time-dashboard-application"></a>**Выполнение приложения панели мониторинга в реальном времени**
1. Извлеките RealtimeDashboardApp и сохраните его локально.

    ![Папка RealTimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Запустите приложение RealtimeDashboardApp.exe.

3. Введите допустимые учетные данные Power BI и выберите **Войти**.  

    ![Окно входа в приложение панели мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Нажмите кнопку **Принять**.

    ![Окно завершения входа в приложение панели мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Если необходимо очистить набор данных Power BI, запустите RealtimeDashboardApp с использованием параметра flushdata. 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Пакетный анализ
Цель этого задания состоит в том, чтобы продемонстрировать, как Contoso Motors использует вычислительные возможности Azure для работы с большими данными. Эти данные предоставляют подробные сведения о шаблонах вождения, манере использования и работоспособности автомобиля. Эти сведения позволяют:

* улучшить обслуживание клиентов и снизить его цену, предоставив им сведения о манерах вождения, в том числе тех, которые позволяют оптимизировать расход топлива;
* заранее изучить данные о клиентах и их манеры вождения, чтобы принимать правильные бизнес-решения и обеспечивать предоставление лучших в своем классе продуктов и услуг.

В этом решении необходимо использовать следующие метрики.

* **Агрессивная манера вождения.** Определяет характеристики модели, расположение, условия вождения и время, чтобы предоставить подробные сведения о шаблоне агрессивного вождения. Contoso Motors сможет использовать ее в маркетинговых кампаниях, чтобы предлагать новые персонализированные функции и умное страхование.
* **Манера вождения, позволяющая экономить топливо.** Определяет характеристики модели, расположение, условия вождения и время года, чтобы предоставить подробные сведения о шаблоне вождения, позволяющем экономить топливо. Contoso Motors сможет использовать это, чтобы предлагать новые функции, предоставляя сведения о том, как водить с учетом экономии и не вредить окружающей среде.
* **Модели определения необходимости в отзыве.** Определяют необходимость отзыва модели с помощью эксперимента по выявлению аномалий в рамках машинного обучения.

Рассмотрим эти метрики подробнее.

#### <a name="aggressive-driving-behavior-patterns"></a>**Шаблоны агрессивной манеры вождения**

Секционированные сигналы автомобиля и диагностические данные обрабатываются в конвейере AggresiveDrivingPatternPipeline, как показано в рабочем процессе ниже. При этом используется скрипт Hive, чтобы определить модель, расположение автомобиля, условия вождения и другие параметры, которые характерны для агрессивного вождения.

![Рабочий процесс для шаблона агрессивного вождения](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Запрос Hive для шаблона агрессивного вождения***

Скрипт Hive aggresivedriving.hql используется для анализа шаблонов агрессивной манеры вождения. Он расположен в папке \demo\src\connectedcar\scripts скачанного ZIP-файла. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

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


В скрипте учитывается включенная передача, положение педали тормоза и скорость автомобиля, которые позволяют идентифицировать неосторожное или агрессивное поведение во время вождения на основе шаблона торможения при высокой скорости. 

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar:

![Выходные данные AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Шаблоны манеры вождения для экономии топлива**

Секционированные сигналы автомобиля и диагностические данные обрабатываются в конвейере FuelEfficientDrivingPatternPipeline, как показано в рабочем процессе ниже. При этом используется скрипт Hive, чтобы определить модель, расположение автомобиля, условия вождения и другие параметры, которые характерны для шаблонов вождения, позволяющих экономить топливо.

![Шаблоны вождения для экономии топлива](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Запрос Hive для шаблона вождения, позволяющего экономить топливо***

Скрипт Hive fuelefficientdriving.hql используется для анализа шаблонов манеры вождения, позволяющих экономить топливо. Он расположен в папке \demo\src\connectedcar\scripts скачанного ZIP-файла. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

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


В этом скрипте учитывается включенная передача, положение педали тормоза, педали газа и скорость автомобиля, что дает возможность определить манеру вождения, позволяющую экономить топливо, на основе шаблонов ускорения, торможения и скорости. 

После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar:

![Выходные данные FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Прогнозы для модели отзыва**

Эксперимент по машинному обучению подготавливается и публикуется в качестве веб-службы в рамках развертывания решения. В этом рабочем процессе используется конечная точка пакетной оценки. Она регистрируется в качестве службы, связанной с фабрикой данных. Для ввода в эксплуатацию используется пакетная оценка в фабрике данных.

![Конечная точка машинного обучения](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Зарегистрированная связанная служба используется в DetectAnomalyPipeline для оценки данных с использованием модели обнаружения аномалий. 

![Пакетная оценка в фабрике данных в рамках машинного обучения](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

В контейнере выполняется несколько шагов по подготовке данных, чтобы их можно было использовать в веб-службе пакетной оценки. 

![DetectAnomalyPipeline для прогнозирования отзывов](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Запрос Hive для обнаружения аномалий***

По завершении оценки действие HDInsight выполняет обработку и агрегацию данных, которые модель относит к категории аномалий. Модель использует оценку вероятности 0,60 или выше.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

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


После успешного выполнения конвейера отобразятся следующие секции, созданные в учетной записи хранения в контейнере connectedcar:

![Выходные данные DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Опубликовать

### <a name="real-time-analysis"></a>Анализ в режиме реального времени
Один из запросов в задании Stream Analytics предусматривает публикацию событий в выходном экземпляре концентратора событий. 

![Публикация данных задания Stream Analytics в выходном экземпляре концентратора событий](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Следующий запрос Stream Analytics используется для публикации выходного экземпляра концентратора событий:

![Запрос Stream Analytics на публикацию в выходном экземпляре концентратора событий](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Этот поток событий использует приложение RealTimeDashboardApp, которое входит в состав решения. Это приложение использует веб-службу машинного обучения "запрос и ответ" для оценки в режиме реального времени. Оно публикует результаты для использования в наборе данных Power BI. 

### <a name="batch-analysis"></a>Пакетный анализ
Результаты пакетной обработки и обработки в режиме реального времени публикуются в таблицах базы данных SQL Azure. Сервер, база данных и таблицы SQL создаются автоматически как часть скрипта настройки. 

Результаты пакетной обработки копируются в рабочий процесс киоска данных.

![Копирование результатов пакетной обработки в рабочий процесс киоска данных](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Задание Stream Analytics публикуется в киоске данных.

![Публикация данных задания Stream Analytics в киоске данных](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Настройка киоска данных выполняется в задании Stream Analytics.

![Настройка киоска данных в задании Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Использование
Power BI предоставляет для этого решения расширенную панель мониторинга, предназначенную для визуализации данных в режиме реального времени и прогнозной аналитики. 

Окончательная панель мониторинга выглядит следующим образом:

![Информационная панель Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Сводка
Этот документ содержит подробные сведения о решении для аналитики телеметрии автомобилей. Шаблон лямбда-архитектуры используется для анализа в режиме реального времени и пакетного анализа с прогнозами и действиями. Этот шаблон подходит для самых разнообразных сценариев использования, в которых требуется использование «горячего» (в режиме реального времени) и «холодного» (пакетного) анализа. 

### <a name="references"></a>Ссылки

* [Решение Visual Studio "Симулятор телематики автомобиля"](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/)
* [Фабрика данных Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Приступая к отправке событий в концентраторы событий Azure на платформе .NET Standard](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Перемещение данных с помощью действия копирования](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Использование настраиваемых действий в конвейере фабрики данных Azure](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Документация по Машинному обучению Azure (предварительная версия)](http://go.microsoft.com/fwlink/?LinkId=717077) 
