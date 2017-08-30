---
title: "Использование инструментов Azure Stream Analytics для Visual Studio | Документация Майкрософт"
description: "Руководство по началу работы с инструментами Azure Stream Analytics для Visual Studio."
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: samacha
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017

---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Использование инструментов Azure Stream Analytics для Visual Studio
## <a name="introduction"></a>Введение
В этом руководстве вы узнаете, как использовать инструменты Azure Stream Analytics для Visual Studio для создания, разработки, локального тестирования, отладки заданий Stream Analytics и управления ими. 

После работы с этим учебником вы сможете выполнить следующие задачи:
* Изучить инструменты Stream Analytics для Visual Studio.
* Настроить и развернуть задание Stream Analytics.
* Протестировать это задание локально с помощью локальных примеров данных.
* Использовать мониторинг для устранения неполадок.
* Экспортировать существующие задания в проекты.

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным руководством вам потребуется:
* Выполните действия до раздела "Создание задания Stream Analytics" руководства [Создание решения IoT с помощью Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Используйте Visual Studio 2015, Visual Studio 2013 с обновлением 4 или Visual Studio 2012. Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. Visual Studio 2017 не поддерживается. 
* Используйте Azure SDK для .NET (версии 2.7.1 или выше). Можно установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* Установите [инструменты Stream Analytics для Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Создание проекта Stream Analytics
1. В Visual Studio щелкните меню **Файл** и выберите **Новый проект**. 

2. Из списка шаблонов слева выберите **Stream Analytics** и щелкните **Azure Stream Analytics Application** (Приложение Azure Stream Analytics).

3. Введите **имя проекта**, **расположение** и **имя решения**, как и для других проектов.

    ![Окно нового проекта](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    Будет создан проект **Toll** в **обозревателе решений**.

    ![Проект Toll, созданный в обозревателе решений](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Выбор соответствующей подписки
1. В Visual Studio щелкните меню **Вид** и выберите **Обозреватель серверов**.

2. Войдите в систему, используя учетную запись Azure. 

## <a name="define-the-input-sources"></a>Определение источников входных данных
1.  В **обозревателе решений** разверните узел **Входные данные** и переименуйте **Input.json** на **EntryStream.json**. Дважды щелкните **EntryStream.json**.
2.  Теперь **псевдонимом входных данных** должен быть **EntryStream**. Псевдоним входных данных используется в скрипте запроса. 
3.  В поле **Тип источника** выберите **Поток данных**.
4.  В поле **Источник** выберите **Концентратор событий**.
5.  В поле **Пространство имен служебной шины** выберите вариант **TollData**.
6.  В поле **Имя концентратора событий** выберите **запись**.
7.  В поле **Имя политики концентратора событий** выберите **RootManageSharedAccessKey** (значение по умолчанию).
8.  В поле **Формат сериализации событий** выберите**Json**. 
9.  В поле **Кодировка** выберите **UTF-8**. Ваши параметры должны выглядеть примерно так:

    ![Окно ввода](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. Чтобы завершить работу мастера, нажмите кнопку **Сохранить**. Теперь можно добавить другой источник входных данных, чтобы создать выходной поток. Щелкните правой кнопкой мыши **узел входных данных** и выберите **Создать элемент**.

    ![Новый элемент](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. В окне выберите **Azure Stream Analytics Input** (Входные данные Azure Stream Analytics) и измените **имя** на **ExitStream.json**. Щелкните **Добавить**.

    ![Окно "Добавить новый элемент"](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. Дважды щелкните **ExitStream.json** в проекте и сделайте то же, что и при заполнении сведений для потока записи. Обязательно введите **exit** в качестве **имени концентратора событий**, как показано на снимке экрана ниже.

    ![Окно ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    Теперь определены два входных потока.

    ![Входные потоки входа и выхода](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    Добавьте входные справочные данные для файла большого двоичного объекта с данными регистрации автомобиля.

13. Щелкните правой кнопкой мыши **узел входных данных** в проекте, а затем сделайте то же, что и при настройке входных потоковых данных. Для параметра **Псевдоним входных данных** введите **Регистрация**, а для параметра **Тип источника** выберите **Справочные данные**.

    ![Окно регистрации](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. В поле **Учетная запись хранения** выберите вариант **tolldata**. В поле **Контейнер** выберите **tolldata**, а в поле **Шаблон пути** введите **registration.json**. Имя файла должно быть введено строчными буквами (в нем учитывается регистр).
15. Чтобы завершить работу мастера, нажмите кнопку **Сохранить**.

Все входные данные определены.

## <a name="define-the-output"></a>Определение выходных данных
1.  В **обозревателе решений** разверните узел **Входные данные** и дважды щелкните **Output.json**.

2.  В поле **Псевдоним выходных данных** введите **output**. 
3.  В поле **Приемник** выберите **База данных SQL**.
4.  В поле **База данных** выберите **TollDataDB**.
5.  В поле **Имя пользователя** введите **tolladmin**. 
6.  В поле **Пароль** введите **123toll!**.
7.  В поле **Таблица** введите **TollDataRefJoin**.
8.  Щелкните **Сохранить**.

    ![Окно вывода](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Создание запроса Stream Analytics
Это руководство отвечает на ряд бизнес-вопросов, связанных с данными о сборе платы. В нем также создаются запросы Stream Analytics, с помощью которых можно получить ответы.
Прежде чем приступить к первому заданию Stream Analytics, давайте рассмотрим простые сценарии и синтаксис запроса.

### <a name="introduction-to-the-stream-analytics-query-language"></a>Общие сведения о языке запросов Stream Analytics
Допустим, вам нужно подсчитать количество автомобилей, въезжающих в пункт сбора платы. Так как это непрерывный поток событий, необходимо определить период времени. Измените вопрос на следующий: "Сколько автомобилей въезжает в пункт сбора платы каждые три минуты?". Этот способ подсчета часто называют переворачивающимся.

Взгляните на запрос Stream Analytics, который отвечает на этот вопрос.

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Stream Analytics использует язык запросов, такой же как SQL, и добавляет несколько расширений для указания аспектов запроса, связанных со временем.

Дополнительные сведения об используемых в запросах конструкциях [Управление временем](https://msdn.microsoft.com/library/azure/mt582045.aspx) и [Оконные расширения](https://msdn.microsoft.com/library/azure/dn835019.aspx) см. на сайте MSDN.

Теперь, когда вы написали первый запрос Stream Analytics, нужно его протестировать. Используйте файлы с образцами данных, расположенные в папке TollApp по следующему пути:

..\TollApp\TollApp\Data

Эта папка содержит следующие файлы:
*   Entry.json
*   Exit.json
*   Registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>Подсчет количества автомобилей, въезжающих в пункт сбора платы
В проекте дважды щелкните **Script.asaql**, чтобы открыть скрипт в **редакторе запросов**. Скопируйте и вставьте скрипт из предыдущего раздела в редактор. Редактор запросов поддерживает технологию Intellisense, выделение синтаксиса цветом и маркер ошибок.

![Редактор запросов](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Локальное тестирование запросов Stream Analytics

1. Чтобы скомпилировать запрос определения ошибок синтаксиса, щелкните проект правой кнопкой мыши и выберите **Сборка**. 

2. Чтобы сверить этот запрос с примером данных, можно использовать локальные примеры данных. Щелкните правой кнопкой мыши входные данные и выберите **Add local input** (Добавить локальные входные данные).

    ![Добавление локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. Во всплывающем окне выберите примеры данных из локальной папки. Щелкните **Сохранить**.

    ![Окно добавления локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    Файл **local_EntryStream.json** будет автоматически добавлен в папку входных данных.

    ![Файл, добавленный в папку входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. В **редакторе запросов** щелкните **Run Locally** (Запустить локально). Или можно нажать клавишу F5.

    ![Локальный запуск](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Выходные данные локального запуска](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Нажмите любую клавишу, чтобы просмотреть выходные данные в окне **Результаты локального запуска ASA** в Visual Studio. 

    ![Окно "Результаты локального запуска ASA"](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. Щелкните **Открыть папку результата** для проверки выходных файлов в формате CSV и JSON.

    ![Выходные данные "Открыть папку результата"](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>Выборка входных данных
Можно также применить выборку входных данных из источников входных данных в локальный файл. 
1. Щелкните правой кнопкой мыши файл конфигурации ввода и выберите **Образцы данных**. 

   ![Образец данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    Сейчас можно выбрать только концентратор событий или центр Интернета вещей. Другие источники входных данных не поддерживаются.

2. Во всплывающем окне введите локальный путь для сохранения примера данных. Щелкните **Выборка**.

    ![Окно "Образцы данных"](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    Ход выполнения можно просмотреть в окне **вывода**. 

    ![Окно вывода](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Отправка запроса Stream Analytics в Azure
1. В **редакторе запросов** щелкните **Отправить в Azure** в редакторе сценариев.

    ![Отправка в Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Выберите **Create a New Azure Stream Analytics Job** (Создать задание Azure Stream Analytics). Введите **имя задания** и выберите правильную **подписку**. Нажмите кнопку **Submit**(Отправить).

    ![Окно отправки задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>Запустите задание
Ваше задание было создано, и представление заданий открылось автоматически. 
1. Нажмите кнопку с **зеленой стрелкой**, чтобы запустить задание.

    ![Запустите задание](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Выберите значение по умолчанию и нажмите кнопку **Запустить**.
 
    ![Окно запуска задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    **Состояние** задания изменится на **Выполняется** и отобразятся сведения о **входных** и **выходных событиях**.

    ![Состояние выполнения в сводных данных задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Проверка результатов в Visual Studio
1. Откройте **обозреватель серверов** в Visual Studio и щелкните правой кнопкой мыши таблицу **TollDataRefJoin**.
2. Выберите **Показать таблицу данных** для просмотра выходных данных задания.
   
    ![Выбор "Показать таблицу данных" в обозревателе серверов](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>Просмотр метрик заданий
Определенную базовую статистику задания можно найти с помощью **метрик задания**. 

![Окно метрик задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Отображение задания в обозревателе серверов
В **обозревателе серверов** щелкните **Задания Stream Analytics** и щелкните **Обновить**. В **заданиях Stream Analytics** отобразится задание.

![Задания Stream Analytics в списке обозревателя серверов](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Открытие представления задания
Чтобы открыть представление задания, разверните узел задания и дважды щелкните узел **Представление заданий**.

![Узел представления задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Экспорт существующего задания в проект
Существуют два способа экспортировать существующее задание в проект.

В **обозревателе серверов** щелкните правой кнопкой мыши узел задания в узле **заданий Stream Analytics**, а затем выберите **Export to New Stream Analytics Project** (Экспортировать в новый проект Stream Analytics).

![Экспорт в новый проект Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

Проект создается в **обозревателе решений**.

![Проект, созданный в обозревателе решений](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
Также можно использовать представление задания и щелкнуть **Создать проект**.

![Создание проекта](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения
 
- Не поддерживаются выходные данные Power BI и Azure Date Lake Store.
- Редактор не поддерживает добавление или изменение определяемых пользователем функций JavaScript.

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

