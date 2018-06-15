---
title: Использование панели мониторинга Power BI для прогнозирования исправности автомобиля и манеры вождения с помощью Azure | Документация Майкрософт
description: Используйте возможности Cortana Intelligence, чтобы получить прогнозы и актуальную информацию об исправности и манере вождения автомобиля в режиме реального времени.
services: machine-learning
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 8544c400fdb7d4f00a4946aba321fec3568d024a
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248458"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Инструкции по настройке панели мониторинга Power BI шаблона решения для аналитики телеметрии автомобилей
Из этого меню можно переходить к другим разделам обзора. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Решение для аналитики телеметрии автомобилей демонстрирует, как поставщики и производители автомобилей, а также страховые компании могут использовать возможности Cortana Intelligence. Они могут получать прогнозы и актуальную информацию об исправности автомобиля и манере вождения в реальном времени, чтобы повышать качество обслуживания клиентов, оптимизировать исследования, разработку и маркетинговые кампании. В этих пошаговых инструкциях показано, как настроить отчеты и панель мониторинга Power BI после развертывания решения в подписке. 

## <a name="prerequisites"></a>предварительным требованиям
* Разверните решение [аналитики телеметрии автомобиля](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90). 
* [Установите Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Получите [подписку Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас нет подписки Azure, для начала получите бесплатную подписку Azure.
* Откройте учетную запись Power BI.

## <a name="cortana-intelligence-suite-components"></a>Компоненты Cortana Intelligence Suite
В вашей подписке будут развернуты следующие службы Cortana Intelligence из шаблона решения для аналитики телеметрии автомобилей:

* **Концентраторы событий Azure**. Принимают миллионы событий телеметрии автомобиля в Azure.
* **Azure Stream Analytics**. Предоставляет подробные данные о работоспособности автомобиля в режиме реального времени и сохраняет их в долговременное хранилище для более детальной пакетной аналитики.
* **Машинное обучение Azure**. Обнаруживает аномалии в реальном времени и использует пакетную обработку для предоставления прогнозной аналитики.
* **Azure HDInsight**. Преобразует данные в требуемом масштабе.
* **Фабрика данных Azure**. Предназначена для координации, планирования и мониторинга конвейера пакетной обработки, а также управления ресурсами.

**Power BI** предоставляет для этого решения расширенную панель мониторинга для визуализации данных и прогнозной аналитики. 

Это решение предусматривает использование двух разных источников данных:

* имитированные сигналы автомобиля и наборы диагностических данных;
* каталог автомобилей.

В это решение входит симулятор телематики автомобиля. Он выдает диагностическую информацию и сигналы, соответствующие состоянию автомобиля и манере вождения в определенный момент времени. 

Каталог автомобилей — это эталонный набор данных, который сопоставляет VIN-номера и модели.

## <a name="power-bi-dashboard-preparation"></a>Подготовка панели мониторинга Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Настройка панели мониторинга Power BI в реальном времени

#### <a name="start-the-real-time-dashboard-application"></a>Запуск приложения панели мониторинга в реальном времени
После завершения развертывания следуйте инструкциям по ручному управлению.

1. Скачайте файл RealtimeDashboardApp.zip с приложением панели мониторинга в режиме реального времени и распакуйте его.

2.  В распакованной папке откройте файл конфигурации приложения RealtimeDashboardApp.exe.config. Замените appSettings для подключений служб концентраторов событий, хранилища BLOB-объектов Azure и Машинного обучения Azure значениями из инструкций по ручному управлению. Сохраните изменения.

3. Запустите приложение RealtimeDashboardApp.exe. В окне входа введите допустимые учетные данные Power BI. 

   ![Окно входа Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Нажмите кнопку **Принять**. Приложение начинает выполняться.

   ![Разрешения для панели мониторинга Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Войдите на веб-сайт Power BI и создайте панель мониторинга, работающую в режиме реального времени.

Теперь все готово для настройки панели мониторинга Power BI.  

### <a name="configure-power-bi-reports"></a>Настройка отчетов Power BI
Подготовка отчетов и панели мониторинга в реальном времени занимает около 30–45 минут. 

1. Перейдите на веб-страницу [Power BI](http://powerbi.com) и выполните вход.

    ![Страница входа Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. В Power BI создается набор данных. Выберите набор данных **ConnectedCarsRealtime**.

    ![Набор данных ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Чтобы сохранить пустой отчет, нажмите CTRL+S.

    ![Пустой отчет](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Введите имя отчета **Аналитика телеметрии автомобилей в реальном времени: отчеты**.

    ![Имя отчета](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Отчеты в реальном времени
В этом решении есть три отчета в реальном времени:

* Исправные автомобили
* автомобили, которым требуется обслуживание;
* статистика работоспособности автомобилей.

Вы можете настроить все три отчета или остановиться после любого этапа. Затем можно перейти к следующему разделу о том, как настраивать пакетные отчеты. Мы рекомендуем сразу создать все три отчета для визуализации всей аналитической информации, предоставляемой этим решением в реальном времени.  

### <a name="vehicles-in-operation-report"></a>Отчет об исправных автомобилях
1. Дважды щелкните элемент **Страница 1** и переименуйте его на **Исправные автомобили**.

    ![Исправные автомобили](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. На вкладке **Поля** выберите **vin**. На вкладке **Визуализации** выберите визуализацию **Карточка**.  

    Визуализация **Карточка** создается, как показано на следующем рисунке:

    ![Выбор vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Выберите пустую область, чтобы добавить новую визуализацию.  

4. На вкладке **Поля** выберите **город** и **vin**. На вкладке **Визуализации** выберите визуализацию **Карта**. Перетащите **vin** в область **Значения**. Перетащите **Город** в область **Условные обозначения**. 

    ![Визуализация в виде карточки](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. На вкладке **Визуализации** выберите раздел **Формат**. Выберите **Заголовок** и измените **текст** на **Исправные автомобили по городам**.

    ![Исправные автомобили по городам](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Окончательная визуализация выглядит следующим образом:

    ![Окончательная визуализация](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Выберите пустую область, чтобы добавить новую визуализацию.  

7. На вкладке **Поля** выберите **город** и **vin**. На вкладке **Визуализации** выберите визуализацию **Гистограмма с группировкой**. Перетащите **город** в область **Ось**. Перетащите **vin** в область **Значение**.

8. Отсортируйте гистограмму по параметру **Count of vin** (Количество VIN).

    ![Количество VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Измените заголовок гистограммы в поле **Заголовок** на **Исправные автомобили по городам**. 

10. Выберите раздел **Формат**, а затем **Цвета данных**. Измените **Показать все** на **Вкл.**

    ![Цвета данных](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Измените цвет отдельного города, выбрав символ цвета.

    ![Изменение цвета](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Выберите пустую область, чтобы добавить новую визуализацию.  

13. На вкладке **Визуализации** выберите визуализацию **Гистограмма с группировкой**. На вкладке **Поля** перетащите **город** в область **Ось**. Перетащите **Модель** в область **Условные обозначения**. Перетащите **vin** в область **Значение**.

    ![Гистограмма с группировкой](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Диаграмма выглядит следующим образом:

    ![Отрисовка](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Переупорядочите все визуализации, чтобы страница выглядела следующим образом:

    ![Панель мониторинга с визуализациями](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Отчет "Исправные автомобили" настроен. Вы можете создать следующий отчет в реальном времени или перейти к настройке панели мониторинга. 

### <a name="vehicles-requiring-maintenance-report"></a>Отчет об автомобилях, которым требуется обслуживание

1. Выберите ![Добавить](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png), чтобы добавить новый отчет. Переименуйте его в **Автомобили, которым требуется обслуживание**.

    ![автомобили, которым требуется обслуживание;](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. На вкладке **Поля** выберите **vin**. На вкладке **Визуализации** выберите визуализацию **Карточка**.

    ![Визуализация карточки Vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Набор данных содержит поле с именем **MaintenanceLabel**. Это поле может иметь значение 0 или 1. Значение задается моделью машинного обучения, которая предоставляется как часть решения. Она интегрирована с механизмом обработки в реальном времени. Значение 1 означает, что автомобилю требуется обслуживание. 

3. Чтобы добавить **фильтр уровня страницы** для отображения данных автомобилей, которые требуют обслуживания: 

   a. Перетащите поле **MaintenanceLabel** в область **Фильтры уровня страницы**.
  
      ![Фильтры на уровне страницы](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    Б. В нижней части **Page Level Filters MaintenanceLabel** (Фильтры уровня страницы MaintenanceLabel) выберите **Простая фильтрация**.

      ![Простая фильтрация](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Задайте значение фильтра **1**.

      ![Значение фильтра](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Выберите пустую область, чтобы добавить новую визуализацию.  

5. На вкладке **Визуализации** выберите визуализацию **Гистограмма с группировкой**. 

    ![Карточка VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Гистограмма с группировкой](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. На вкладке **Поля** перетащите **Модель** в область **Ось**. Перетащите **vin** в область **Значение**. Отсортируйте визуализацию по параметру **Count of vin** (Количество VIN). Измените заголовок гистограммы в поле **Заголовок** на **Автомобили, которым требуется обслуживание, по моделям**. 

7. На вкладке **Визуализации** в разделе **Поля** ![Изображение раздела "Поля"](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) перетащите поле **vin** в область **Насыщенность цвета**.

    ![Насыщенность цвета](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. В разделе **Формат** измените **Цвета данных** в визуализации: 

    a. Измените **минимальное** значение цвета на **F2C812**.

    Б. Измените **максимальное** значение цвета на **FF6300**.

    ![Новые цвета данных](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Новые цвета визуализации выглядят следующим образом:

    ![Новые цвета визуализации](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Выберите пустую область, чтобы добавить новую визуализацию.  

10. На вкладке **Визуализации** выберите **Гистограмма с группировкой**. Перетащите **vin** в область **Значение**. Перетащите **город** в область **Ось**. Отсортируйте гистограмму по параметру **Count of vin** (Количество VIN). Измените заголовок гистограммы в поле **Заголовок** на **Автомобили, которым требуется обслуживание, по городам**.

    ![Автомобили, которым требуется обслуживание, по городам](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Выберите пустую область, чтобы добавить новую визуализацию.  

12. На вкладке **Визуализации** выберите визуализацию **Многострочная карточка**. Перетащите **Модель** и **vin** в область **Поля**.

    ![Многострочная карточка](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Переупорядочите все визуализации, чтобы итоговый отчет выглядел следующим образом: 

    ![Измененный итоговый отчет](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Отчет "Автомобили, которым требуется обслуживание" в реальном времени настроен. Вы можете создать следующий отчет в реальном времени или перейти к настройке панели мониторинга. 

### <a name="vehicle-health-statistics-report"></a>Отчет по статистике работоспособности автомобилей

1. Выберите ![Добавить](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png), чтобы добавить новый отчет. Переименуйте его в **Статистика работоспособности автомобилей**. 

2. На вкладке **Визуализации** выберите визуализацию **Датчик**. Перетащите параметр **Скорость** в области **Значение**, **Минимальное значение** и **Максимальное значение**.

   ![Статистика работоспособности автомобилей](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. В области **Значение** для параметра **Скорость** измените агрегирование по умолчанию на **Среднее**.

4. В области **Минимальное значение** для параметра **Скорость** измените агрегирование по умолчанию на **Минимум**.

5. В области **Максимальное значение** для параметра **Скорость** измените агрегирование по умолчанию на **Максимум**.

   ![Значения скорости](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. В качестве **заголовка датчика** укажите **Средняя скорость**.

   ![Датчик](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Выберите пустую область, чтобы добавить новую визуализацию.  

    Аналогичным образом добавьте следующие **датчики**: **Среднее количество моторного масла**, **Среднее количество топлива** и **Средняя температура двигателя**.  

8. В поле каждого датчика измените агрегирование по умолчанию, выполнив шаги, приведенные выше для датчика **Средняя скорость**.

    ![Дополнительные датчики](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Выберите пустую область, чтобы добавить новую визуализацию.

10. На вкладке **Визуализации** выберите визуализацию **Линейная гистограмма и гистограмма с группировкой**. Перетащите **город** в область **Общая ось**. Перетащите **tirepressure**, **engineoil** и **speed** в область **Значения столбцов**. Измените тип агрегирования на **Среднее**. 

11. Перетащите **engineTemperature** в область **Значения строк**. Измените тип агрегирования на **Среднее**. 

    ![Значения столбца и строки](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Измените **заголовок** диаграммы в соответствующем поле на **Среднее значение скорости, давления в шинах, температуры моторного масла и двигателя**.  

    ![Заголовок строки и гистограммы с группировкой](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Выберите пустую область, чтобы добавить новую визуализацию.

14. На вкладке **Визуализации** выберите визуализацию **Диаграмма дерева**. Перетащите **Модель** в область **Группа**. Перетащите **MaintenanceProbability** в область **Значения**.

15. Измените **заголовок** диаграммы в соответствующем поле на **Модели автомобилей, которым требуется обслуживание**.

    ![Заголовок диаграммы дерева](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Выберите пустую область, чтобы добавить новую визуализацию.

17. На вкладке **Визуализации** выберите визуализацию **Нормированная линейчатая диаграмма**. Перетащите **город** в область **Ось**. Перетащите **MaintenanceProbability** и **RecallProbability** в область **Значение**.

    ![Области оси и значения](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. В разделе **Формат** выберите **Цвета данных**. Задайте цвету **MaintenanceProbability** значение **F2C80F**.

19. Измените **заголовок** диаграммы в соответствующем поле на **Вероятность обслуживания и отзыва автомобилей по городам**.

    ![Нормированная линейчатая диаграмма](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Выберите пустую область, чтобы добавить новую визуализацию.

21. На вкладке **Визуализации** выберите визуализацию **Диаграмма с областями**. Перетащите **Модель** в область **Ось**. Перетащите **engineOil**, **tirepressure**, **speed** и **MaintenanceProbability** в область **Значения**. Измените тип агрегирования на **Среднее**. 

    ![Тип агрегирования](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Измените **заголовок** диаграммы на **Среднее значение количества моторного масла, давления в шинах, скорости и вероятности обслуживания по моделям**.

    ![Заголовок диаграммы с областями](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Выберите пустую область, чтобы добавить новую визуализацию.

24. На вкладке **Визуализации** выберите визуализацию **Точечная диаграмма**. Перетащите **Модель** в области **Сведения** и **Условные обозначения**. Перетащите поле **топливо** в область **Ось X**. Измените тип агрегирования на **Среднее**. Перетащите поле **engineTemperature** в область **Ось Y**. Измените тип агрегирования на **Среднее**. Перетащите **vin** в область **Размер**.

    ![Области сведений, условных обозначений, оси и размера](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Измените **заголовок** диаграммы на **Среднее значение количества топлива, температуры двигателя и количества VIN по модели**.

    ![Заголовок точечной диаграммы](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Итоговый отчет выглядит следующим образом:

    ![Итоговый отчет](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Закрепление визуализаций из отчетов на панели мониторинга в реальном времени
1. Создайте пустую панель мониторинга, выбрав символ "плюс" рядом с параметром **Панели мониторинга**. Введите имя **Панель мониторинга аналитики телеметрии автомобилей**.

    ![Символ "плюс" на панели мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Закрепите визуализации из предыдущих отчетов на панели мониторинга. 

    ![Символ "закрепить" на панели мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Когда все три отчета закреплены на панели мониторинга, результат будет похож на снимок экрана ниже. Если вы не создавали все отчеты, панель мониторинга может выглядеть по-другому. 

    ![Панель мониторинга с отчетами](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Вы создали панель мониторинга в реальном времени. По мере выполнения файлов CarEventGenerator.exe и RealtimeDashboardApp.exe на панели мониторинга будут отображаться соответствующие обновления в реальном времени. Для выполнения следующих действий может потребоваться 10–15 минут.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Настройка панели мониторинга пакетной обработки Power BI
> [!NOTE]
> Чтобы завершить выполнение и обработать данные за год, конвейеру пакетной обработки потребуется около двух часов (после завершения развертывания). Дождитесь завершения обработки, прежде чем выполнить следующие действия.
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Скачивание файла конструктора Power BI

1. Предварительно настроенный файл конструктора Power BI входит в состав инструкций по ручному управлению развертыванием. Найдите пункт "2. Настройка панели мониторинга пакетной обработки Power BI".

2. Скачайте шаблон Power BI для панели мониторинга пакетной обработки с именем **ConnectedCarsPbiReport.pbix**.

3. Сохраните его локально.

### <a name="configure-power-bi-reports"></a>Настройка отчетов Power BI

1. Откройте файл конструктора **ConnectedCarsPbiReport.pbix** с помощью Power BI Desktop. Если его у вас еще нет, установите Power BI Desktop с веб-сайта [Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).

2. Выберите **Изменить запросы**.

    ![Изменение запросов](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Дважды щелкните **Источник**.

    ![Источник](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Обновите строку подключения, указав сервер Azure SQL, подготовленный как часть развертывания. Найдите в инструкциях по ручному управлению в разделе о базе данных SQL Azure следующие элементы:

    * Сервер: somethingsrv.database.windows.net
    * База данных: connectedcar
    * Имя пользователя: username
    * Пароль. Паролем к серверу SQL можно управлять на портале Azure.

5. Для параметра **База данных** оставьте значение **connectedcar**.

    ![База данных](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Нажмите кнопку **ОК**.

7. Вкладка **Windows credential** (Учетные данные Windows) выбрана по умолчанию. Измените ее на **Учетные данные базы данных**, выбрав вкладку **База данных** справа.

8. Введите **имя пользователя** и **пароль** в соответствующих полях для доступа к базе данных SQL Azure (выбрано на этапе развертывания).

    ![Учетные данные базы данных](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Нажмите кнопку **Подключиться**.

10. Повторите предыдущие шаги для каждого из трех оставшихся запросов на правой панели. Затем обновите сведения о подключении к источнику данных.

11. Выберите **Close and Load** (Закрыть и загрузить). Наборы данных файла Power BI Desktop подключены к таблицам базы данных SQL.

12. Выберите **Закрыть**, чтобы закрыть файл Power BI Desktop.

    ![закройте](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Выберите **Сохранить**, чтобы сохранить изменения. 

Теперь вы настроили все отчеты для пакетной обработки в решении. 

## <a name="upload-to-powerbicom"></a>Отправка на powerbi.com
1. Перейдите на [веб-портал Power BI](http://powerbi.com) и войдите.

2. Выберите **Получение данных**.

3. Отправьте файл Power BI Desktop. Выберите **Получение данных** > **Files Get** (Полученные файлы)  > **Локальный файл**.

4. Перейдите к файлу **ConnectedCarsPbiReport.pbix**.

5. После передачи файла вернитесь к своей рабочей области Power BI. Набор данных, отчет и пустая панель мониторинга будут созданы автоматически.  

6. Закрепите диаграммы на новой панели мониторинга **Панель мониторинга аналитики телеметрии автомобилей** в Power BI. Выберите пустую панель мониторинга, созданную ранее, а затем перейдите в раздел **Отчеты**. Выберите только что отправленный отчет.  

    ![Новая панель мониторинга Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Отчет содержит шесть страниц:

    Страница 1. Плотность автомобиля.  
    Страница 2. Работоспособность автомобилей в реальном времени.  
    Страница 3. Агрессивно управляемые автомобили.   
    Страница 4. Отозванные автомобили.  
    Страница 5. Автомобили с экономным расходом топлива.  
    Страница 6. Логотип компании Contoso Motors.  

    ![Отчет Power BI с шестью страницами](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. **На странице 3** закрепите следующее содержимое:  

    a. **Count of vin** (Количество VIN).  

   ![Страница 3. Количество VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    Б. **Агрессивно управляемые автомобили по моделям — каскадная диаграмма** 

   ![Страница 3, диаграмма 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. **На странице 5** закрепите следующее содержимое: 

    a. **Count of vin** (Количество VIN).

   ![Страница 5, диаграмма 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    Б. **Автомобили с экономным расходом топлива по моделям: гистограмма с группировкой.**

   ![Страница 5, диаграмма 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. **На странице 4** закрепите следующее содержимое:  

    a. **Count of vin** (Количество VIN). 

   ![Страница 4, диаграмма 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    Б. **Отозванные автомобили по городам, моделям: диаграмма дерева.**

   ![Страница 4, диаграмма 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. **На странице 6** закрепите следующее содержимое:  

    * **Логотип компании Contoso Motors.**

    ![Логотип компании Contoso Motors.](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Упорядочение панели мониторинга  

1. Перейдите на панель мониторинга.

2. Наведите указатель на каждую диаграмму. Переименуйте каждую диаграмму на основе имени, представленного в следующем примере панели управления:

   ![Организация панели мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Расположите диаграммы, как показано на следующем примере:

    ![Измененная панель мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. После создания всех отчетов, упомянутых в этом документе, окончательная панель мониторинга будет выглядеть следующим образом: 

   ![Итоговая панель мониторинга](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Вы создали отчеты и панель мониторинга для сбора данных о состоянии автомобилей и манере вождения (в том числе прогнозные данные и данные пакетной обработки) в режиме реального времени.  
