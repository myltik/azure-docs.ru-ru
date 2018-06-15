---
title: Использование инструментов Azure Stream Analytics для Visual Studio
description: В этой статье описывается, как приступить к работе с инструментами Azure Stream Analytics для Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: bcb4302c3a237ec75ff670b322c9d068d8700de9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738519"
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Использование инструментов Azure Stream Analytics для Visual Studio
Выпущена общедоступная версия инструментов Azure Stream Analytics для Visual Studio. Эти инструменты расширяют возможности пользователя Stream Analytics для устранения неполадок, а также позволяют создавать сложные запросы и даже записывать их на локальный компьютер. Кроме того, можно экспортировать задание Stream Analytics в проект Visual Studio.

## <a name="introduction"></a>Введение
В этом руководстве вы узнаете, как использовать средства Stream Analytics для Visual Studio для создания, разработки, локального тестирования, отладки заданий Stream Analytics и управления ими. С помощью пакета Nuget для непрерывной интеграции и непрерывного развертывания после создания задания можно настроить процесс непрерывной интеграции и развертывания в Azure. Дополнительные сведения см. в статье [Использование средств Stream Analytics Visual Studio для настройки процессов непрерывной интеграции и непрерывного развертывания](stream-analytics-tools-for-visual-studio-cicd.md) статьи.

После работы с этим учебником вы сможете выполнить следующие задачи:

* Изучить средства Stream Analytics для Visual Studio.
* Настроить и развернуть задание Stream Analytics.
* Протестировать это задание локально с помощью локальных примеров данных.
* Использовать мониторинг для устранения неполадок.
* Экспортировать существующие задания в проекты.

>[!IMPORTANT]
> Корпорация Майкрософт рекомендует обновить Средства Azure Data Lake для Visual Studio до версии 2.3.3000.4 и выше. Предыдущие версии являются устаревшими и недоступными для скачивания. 
>
>**Что необходимо сделать?**
>
>1. Проверьте, не используете ли вы Azure Stream Analytics для Visual Studio более ранней версии, чем 2.3.3000.5. 
>   
>   ![Проверка версии средств](./media/stream-analytics-tools-for-vs/about-data-lake.png)
> 
>2. Если у вас более ранняя версия, чем 2.3.3000.5, обновите Средства Azure Data Lake для Visual Studio, посетив сайт центра загрузки: 
>    - [для Visual Studio 2017;](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [для Visual Studio 2013 и 2015.](https://www.microsoft.com/en-us/download/details.aspx?id=54630)

## <a name="prerequisites"></a>предварительным требованиям
Для работы с данным руководством вам потребуется:

* Выполните действия до раздела "Создание задания Stream Analytics" руководства [Создание решения IoT с помощью Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Установите Visual Studio 2017, Visual Studio 2015 или Visual Studio 2013 с обновлением 4. Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. 
* Следуйте [инструкциям по установке](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install), чтобы установить средства Stream Analytics для Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Создание проекта Stream Analytics
В Visual Studio выберите **Файл** > **Создать проект**. Из списка шаблонов слева выберите **Stream Analytics** и щелкните **Azure Stream Analytics Application** (Приложение Azure Stream Analytics).
В нижней части страницы введите **имя** проекта, **расположение** и **имя решения**, как и для других проектов.

![Создание проекта](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Проект **Toll** создается в **обозревателе решений**.

![Проект Toll в обозревателе решений](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Выбор соответствующей подписки
1. Откройте **обозреватель сервера** в Visual Studio, воспользовавшись меню **Вид**.

2. Войдите в свою учетную запись Azure. 

## <a name="define-input-sources"></a>Определение источников входных данных
1. В **обозревателе решений** разверните узел **Входные данные** и переименуйте **Input.json** на **EntryStream.json**. Дважды щелкните **EntryStream.json**.

2. В поле **Входной псевдоним** введите **EntryStream**. Обратите внимание на то, что входной псевдоним используется в сценарии запроса.

3. Для параметра **Тип источника** выберите значение **Поток данных**.

4. Для параметра **Источник** выберите значение **Концентратор событий**.

5. Для параметра **Пространство имен служебной шины** выберите значение **TollData**.

6. Для параметра **Имя концентратора событий** выберите значение **запись**.

7. Для параметра **Имя политики концентратора событий** выберите значение **RootManageSharedAccessKey** (значение по умолчанию).

8. Для параметра **Формат сериализации событий** выберите значение **JSON**, а в для параметра **Кодировка** — значение **UTF8**.
   
   Параметры имеют следующий вид.
   
   ![Входные параметры](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Чтобы завершить работу мастера, нажмите кнопку **Сохранить** в нижней части страницы. Теперь можно добавить другой источник входных данных, чтобы создать выходной поток. Щелкните правой кнопкой мыши **узел входных данных** и выберите **Создать элемент**.
   
   ![Новый элемент](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Во всплывающем окне выберите **Stream Analytics Input** (Входные данные Stream Analytics) и измените **имя** на **ExitStream.json**. Выберите **Добавить**.
   
    ![Добавление нового элемента](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Дважды щелкните **ExitStream.json** в проекте и сделайте то же, что и при заполнении полей для потока записи. Обязательно введите **exit** в поле **Имя концентратора событий**, как показано на снимке экрана ниже.
   
    ![Параметры ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Теперь определены два входных потока.
   
   ![Два входных потока](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Добавьте входные эталонные данные для файла большого двоичного объекта с данными регистрации автомобилей.
   
12. Щелкните правой кнопкой мыши узел **Inputs** в проекте, а затем сделайте то же, что и при настройке входных потоков. Для параметра **Тип источника** выберите **Справочные данные**, а для параметра **Входной псевдоним** введите **Регистрация**.
   
    ![Параметры регистрации](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Выберите **учетную запись хранения**, содержащую параметр с **TollData**. Контейнер должен иметь имя **TollData**, а в поле **Шаблон пути** введите **registration.json**. Имя файла должно быть введено строчными буквами (в нем учитывается регистр).

14. Нажмите кнопку **Сохранить**, чтобы завершить работу мастера.

Все входные данные определены.

## <a name="define-output"></a>Определение выходных данных
1. В **обозревателе решений** разверните узел **Входные данные** и дважды щелкните **Output.json**.

2. В поле **Выходной псевдоним** введите **output**. В поле **Приемник** выберите **База данных SQL**.

3. В поле **База данных** введите **TollDataDB**.

4. В поле **Имя пользователя** введите **tolladmin**. В поле **Пароль** введите **123toll!**. В поле **Таблица** введите **TollDataRefJoin**.

5. Щелкните **Сохранить**.

   ![Выходные параметры](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Запрос Stream Analytics
Это руководство отвечает на ряд бизнес-вопросов, связанных с данными о сборе платы. Мы составили запросы, которые можно использовать в Stream Analytics, чтобы получить соответствующие ответы. Прежде чем приступить к первому заданию Stream Analytics, давайте рассмотрим простые сценарии и синтаксис запроса.

### <a name="introduction-to-stream-analytics-query-language"></a>Общие сведения о языке запросов Stream Analytics
Допустим, вам нужно подсчитать количество автомобилей, въезжающих в пункт сбора платы. Так как это непрерывный поток событий, необходимо определить период времени. Давайте изменим вопрос на следующий: "Сколько автомобилей въезжает в пункт сбора платы каждые три минуты?" Эту меру часто называют переворачивающейся.

Рассмотрим запрос Stream Analytics, который отвечает на этот вопрос.

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Как видите, Stream Analytics использует язык запросов, который похож на SQL. Он добавляет несколько расширений для указания аспектов запросов, связанных со временем.

Дополнительные сведения об используемых в запросах конструкциях [Управление временем](https://msdn.microsoft.com/library/azure/mt582045.aspx) и [Оконные расширения](https://msdn.microsoft.com/library/azure/dn835019.aspx) см. на сайте MSDN.

Теперь, когда вы написали первый запрос Stream Analytics, проверьте его с помощью примеров файлов данных, расположенных в папке TollApp по указанному ниже пути.

**..\TollApp\TollApp\Data**

Эта папка содержит следующие файлы:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Вопрос. Количество автомобилей, въезжающих в пункт сбора платы
В проекте дважды щелкните **Script.asaql**, чтобы открыть этот сценарий в редакторе. Вставьте сценарий из предыдущего раздела в редактор. Редактор запросов поддерживает технологию IntelliSense, выделение синтаксиса цветом и маркер ошибок.

![Редактор запросов](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Локальное тестирование запросов Stream Analytics
Можно предварительно скомпилировать запрос, чтобы увидеть все синтаксические ошибки.

1. Чтобы проверить этот запрос на примерах данных, можно использовать локальные примеры данных, щелкнув правой кнопкой мыши входные данные и выбрав **Добавить локальный ввод**.
   
   ![Добавление локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. Во всплывающем окне выберите примеры данных из локальной папки. Щелкните **Сохранить**.
   
   ![Добавление локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Файл **local_EntryStream.json** будет автоматически добавлен в папку входных данных.
   
   ![Список файлов в локальной папке входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. В редакторе запросов щелкните **Запустить локально**. Или можно нажать клавишу F5.
   
   ![Локальный запуск](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Путь к выходным данным можно найти в выходных данных консоли. Нажмите любую клавишу, чтобы открыть папку результатов.
   
   ![Локальный запуск](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Проверьте результаты в локальной папке.
   
   ![Результат в локальной папке](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Пример ввода
Можно также применить выборку входных данных из источников входных данных в локальный файл. Щелкните правой кнопкой мыши файл конфигурации входных данных и выберите **Образцы данных**. 

![Образец данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Обратите внимание на то, что сейчас можно выбрать только концентраторы событий или Центры Интернета вещей. Другие источники входных данных не поддерживаются. Во всплывающем диалоговом окне введите локальный путь для сохранения примера данных. Выберите **Пример**.

![Конфигурация примера данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Ход выполнения можно просмотреть в окне **вывода**. 

![Выходные данные для примера данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Отправка запроса Stream Analytics в Azure
1. В **редакторе запросов** щелкните **Отправить в Azure** в редакторе сценариев.

   ![Отправка в Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Выберите **Create a New Azure Stream Analytics Job** (Создать задание Azure Stream Analytics). В поле **Имя задания** введите **TollApp**. Из раскрывающегося списка выберите соответствующую **подписку**. Нажмите кнопку **Submit** (Отправить).

   ![Отправка задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Запустите задание
Задание создано, и представление заданий откроется автоматически. 
1. Нажмите кнопку с зеленой стрелкой, чтобы запустить задание.

   ![Кнопка запуска задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Выберите значение по умолчанию и нажмите кнопку **Запустить**.
 
   ![Запуск задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Вы увидите, что состояние задания изменилось на **Выполняется** и происходят события ввода и вывода.

   ![Сводка задания и метрики](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Проверка результатов в Visual Studio
1. Откройте обозреватель серверов Visual Studio и щелкните правой кнопкой мыши таблицу **TollDataRefJoin** .

2. Выберите **Показать таблицу данных** для просмотра выходных данных задания.
   
   ![Просмотр данных таблицы](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Просмотр метрик задания
Определенную базовую статистику задания можно найти на странице **Метрики задания**. 

![Метрики задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Отображение задания в обозревателе серверов
В **обозревателе сервера** щелкните **Задания Stream Analytics** и щелкните **Обновить**. В разделе **Задания Stream Analytics** отобразится ваше задание.

![Список заданий](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Открытие представления задания
Разверните узел задания и дважды щелкните узел **Представление заданий**, чтобы открыть представление заданий.

![Представление задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Экспорт существующего задания в проект
Существуют два способа экспортировать существующее задание в проект.
* Щелкните правой кнопкой мыши узел задания в узле **Задания Stream Analytics** в **обозревателе сервера**. Выберите **Экспортировать в новый проект Stream Analytics**.
   
   ![Экспорт в новый проект Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Созданный проект появится в **обозревателе решений**.
   
    ![Задание в обозревателе решений](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* В представлении заданий выберите **Создать проект**.
   
   ![Создание проекта](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения
 
* Локальное тестирование не работает, если запрос содержит геопространственные функции.
* Редактор не поддерживает добавление или изменение определяемой пользователем функции JavaScript.
* Локальное тестирование не поддерживает сохранение выходных данных в формате JSON. 
* Не поддерживаются выходные данные Power BI и ADLS.



## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Azure Stream Analytics).
* [Azure Stream Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) (Справочник по API-интерфейсу REST для управления Stream Analytics).


