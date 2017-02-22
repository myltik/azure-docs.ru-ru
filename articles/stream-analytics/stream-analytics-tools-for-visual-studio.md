---
title: "Использование инструментов Azure Stream Analytics для Visual Studio | Документация Майкрософт"
description: "Руководство по началу работы с инструментами Azure Stream Analytics для Visual Studio."
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: 
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 81a5678051b026a16bdae2f2eab7ead2c17f9563
ms.openlocfilehash: 4874c52b24d9c69fa1d1648035102aaef276f944


---
# <a name="use-azure-stream-analytics-tool-for-visual-studio"></a>Использование инструментов Azure Stream Analytics для Visual Studio
Выпущена общедоступная версия инструментов Azure Stream Analytics для Visual Studio. Эти инструменты расширяют возможности пользователя Stream Analytics для устранения неполадок, а также позволяют создавать сложные запросы и даже записывать их на локальный компьютер. Кроме того, они дают возможность экспортировать задание Stream Analytics в проект Visual Studio.

## <a name="introduction"></a>Введение
В этом руководстве вы знаете, как использовать инструменты Azure Stream Analytics для Visual Studio для создания, разработки, локального тестирования, отладки заданий Azure Stream Analytics и управления ими. 

После работы с этим учебником вы сможете выполнить следующие задачи:
* Изучить инструменты Azure Stream Analytics для Visual Studio.
* Настроить и развернуть задание Stream Analytics.
* Протестировать это задание локально с помощью локальных примеров данных.
* Использовать мониторинг для устранения неполадок.
* Экспортировать существующие задания в проекты.

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным руководством вам потребуется:
* Выполните действия до раздела **Создание задания Stream Analytics** руководства [Создание решения IoT с помощью Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Visual Studio 2015, Visual Studio 2013 с обновлением 4 или Visual Studio 2012. Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. В настоящее время Visual Studio 2017 не поддерживается. 
* Пакет SDK Microsoft Azure для .NET (версии 2.7.1 или выше).  Вы можете установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* Установленный экземпляр [инструментов Azure Stream Analytics для Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Создание проекта Stream Analytics
В Visual Studio щелкните меню **Файл** и выберите **Новый проект**. Из списка шаблонов слева выберите **Stream Analytics** и щелкните **Azure Stream Analytics Application** (Приложение Azure Stream Analytics).
Введите имя проекта, расположение и имя решения в нижней части окна, как и для других проектов.

![Создание проекта Azure Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Вы увидите созданный проект **Toll** в **обозревателе решений**.

![Создание проекта Azure Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Выбор соответствующей подписки
1. Откройте **обозреватель сервера** в Visual Studio, воспользовавшись меню **Вид**.
2. Войдите в свою учетную запись Azure. 

## <a name="define-input-sources"></a>Определение источников входных данных
1.  В **обозревателе решений** разверните узел **Входные данные** и переименуйте **Input.json** в **EntryStream.json**. Дважды щелкните **EntryStream.json**.
2.  Теперь **псевдонимом входных данных** должен быть **EntryStream**. Обратите внимание, что этот псевдоним входных данных будет использоваться в сценарии запроса. 
3.  В качестве типа источника данных выберите **Поток данных**.
4.  Источник данных — **концентратор событий**.
5.  Из раскрывающегося списка "Service bus namescape" (Пространство имен служебной шины) выберите **TollData**.
6.  В качестве имени концентратора событий следует указать **entry**.
7.  Имя политики концентратора событий — **RootManageSharedAccessKey** (значение по умолчанию).
8.  В поле **Формат сериализации событий** выберите **JSON**, а в поле **Кодировка** — **UTF8**.
   
   Настройки будут выглядеть следующим образом:
   
   ![Определение источников входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.  Чтобы завершить работу мастера, нажмите кнопку **Сохранить** в нижней части страницы. Теперь можно добавить другой источник входных данных, чтобы создать выходной поток. Щелкните правой кнопкой мыши узел "Входные данные" и выберите **Создать элемент**.
   
   ![Определение источников входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Во всплывшем окне выберите **Azure Stream Analytics Input** (Входные данные Azure Stream Analytics) и измените имя на **ExitStream.json**. Щелкните **Добавить**.
   
   ![Определение источников входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Дважды щелкните **ExitStream.json** в проекте и выполните те же действия, что и при заполнении сведений для потока записи. Обязательно введите значение имени концентратора событий, как показано на снимке экрана ниже.
   
   ![Определение источников входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Теперь определены два входных потока.
   
   ![Определение источников входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Далее добавим входные справочные данные для файла большого двоичного объекта с данными регистрации автомобиля.
   
12. Щелкните правой кнопкой мыши узел **Входные данные** в проекте и выполните те же действия для входных данных потока, но вместо "Поток данных" выберите **Справочные данные**, а вместо "Входной псевдоним" — **Регистрация**.
   
   ![Определение источников входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Выберите учетную запись хранения, имя которой содержит **tolldata**. Контейнер должен иметь имя **tolldata**, а в поле **Шаблон пути** введите **registration.json**. Имя файла должно быть введено строчными буквами (в нем учитывается регистр).
14. Нажмите кнопку **Сохранить**, чтобы завершить работу мастера.

Все входные данные определены.

## <a name="define-output"></a>Определение выходных данных
1.  В **обозревателе решений** разверните узел **Входные данные** и дважды щелкните **Output.json**.
2.  Для параметра "Выходной псевдоним" задайте значение **output**, а в качестве приемника выберите Базу данных SQL.
2.  Введите имя базы данных — **TollDataDB**.
3.  Введите **tolladmin** в поле **Имя пользователя**, **123toll!** в поле **Пароль** и **TollDataRefJoin** в поле **Таблица**.
4.  Щелкните **Сохранить**.

![Определение выходных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Запрос Azure Stream Analytics
В этом руководстве мы попытаемся ответить на несколько бизнес-вопросов, связанных с данными платы, и создадим запросы Stream Analytics, используемые в Azure Stream Analytics для нахождения нужного ответа.
Прежде чем приступить к первому заданию Stream Analytics, давайте рассмотрим простые сценарии и синтаксис запроса.

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Общие сведения о языке запросов Azure Stream Analytics
Допустим, вам нужно подсчитать количество автомобилей, въезжающих в пункт сбора платы. Так как это непрерывный поток событий, необходимо определить период времени. Давайте изменим вопрос на следующий: "Сколько автомобилей въезжает в пункт сбора платы каждые три минуты?". Этот период часто называют переворачивающимся.

Давайте взглянем на запрос Azure Stream Analytics, который отвечает на этот вопрос.

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Как видите, Azure Stream Analytics использует язык запросов, такой же как SQL, и добавляет несколько расширений для указания аспектов запроса, связанных со временем.

Дополнительные сведения об используемых в запросах конструкциях [Управление временем](https://msdn.microsoft.com/library/azure/mt582045.aspx) и [Оконные расширения](https://msdn.microsoft.com/library/azure/dn835019.aspx) см. на сайте MSDN.

Теперь, когда вы написали первый запрос Azure Stream Analytics, настало время его тестирования с помощью файлов-примеров, расположенных в папке TollApp по указанному ниже пути.

**..\TollApp\TollApp\Data**

Эта папка содержит следующие файлы: • Entry.json • Exit.json • Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Вопрос. Количество автомобилей, въезжающих в пункт сбора платы
В проекте дважды щелкните Script.asaql, чтобы открыть сценарий в редакторе, и вставьте в окно редактора сценарий из предыдущего раздела. Редактор запросов поддерживает технологию Intellisense, выделение синтаксиса цветом и маркер ошибок.

![Изменение запроса](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>Локальное тестирование запросов Azure Stream Analytics

1. Можно предварительно скомпилировать запрос, чтобы увидеть все синтаксические ошибки. [Подлежит уточнению]
2. Чтобы проверить этот запроса на примерах данных, можно использовать локальные примеры данных, щелкнув правой кнопкой мыши входные данные и выбрав в контекстном меню **Add local input** (Добавить локальные входные данные).
   
   ![Добавление локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   Во всплывающем окне выберите примеры данных из локальной папки. Щелкните **Сохранить**.
   
   ![Добавление локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Файл **local_EntryStream.json** будет автоматически добавлен в папку входных данных.
   
   ![Добавление локальных входных данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. В редакторе запросов щелкните "Run Locally" (Запустить локально). Или можно нажать клавишу F5.
   
   ![Локальный запуск](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Можно найти путь вывода в выходных данных консоли и нажать любую клавишу, чтобы открыть папку результатов.
   
   ![Локальный запуск](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Проверьте результат в локальной папке.
   
   ![Локальный запуск](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Пример ввода
Можно также применить выборку входных данных из источников входных данных в локальный файл. Щелкните правой кнопкой мыши файл конфигурации ввода и выберите **Образцы данных**. 

![Пример данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Обратите внимание, что в данный момент доступна только выборка данных концентратора событий или Центра Интернета вещей. Другие источники входных данных не поддерживаются.  Во всплывающем диалоговом окне укажите локальный путь для сохранения примеров данных. Щелкните **Выборка**.

![Пример данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Ход выполнения можно просмотреть в окне вывода. 

![Пример данных](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>Отправка запроса Azure Stream Analytics в Azure
В **редакторе запросов** щелкните **Submit To Azure in script editor** (Отправить в Azure в редакторе сценариев).

![Отправка задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Выберите "Create a New Azure Stream Analytics Job" (Создать задание Azure Stream Analytics). Введите имя задания, как показано ниже. Выберите соответствующую подписку. Нажмите кнопку Отправить.

![Отправка задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-job"></a>Запуск задания
Ваше задание было создано, и представление заданий открылось автоматически. Нажмите кнопку с **зеленой стрелкой**, чтобы запустить задание.

![Запуск задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
Выберите значение по умолчанию и нажмите кнопку **Запустить**.
 
![Запуск задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

Вы увидите, что состояние задания изменилось на **Выполняется** и происходят события ввода и вывода.

![Запуск задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Проверка результатов в Visual Studio
1. Откройте обозреватель серверов Visual Studio и щелкните правой кнопкой мыши таблицу **TollDataRefJoin** .
2. Выберите **Показать таблицу данных** для просмотра выходных данных задания.
   
   ![Выбор "Показать таблицу данных" в обозревателе серверов](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Просмотр метрик задания
Определенную базовую статистику задания можно найти с помощью **метрик задания**. 

![Метрики задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-job-in-server-explorer"></a>Отображение задания в обозревателе сервера
Щелкните **Задания Stream Analytics** в **обозревателе сервера** и щелкните **Обновить**. Ваше задание должно отобразиться в списке **Задания Stream Analytics**.

![Список заданий](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>Открытие представления задания
Разверните узел задания и дважды щелкните узел **Представление задания**, чтобы открыть представление задания.

![Представление задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Экспорт существующего задания в проект
Существуют два способа экспортировать существующее задание в проект.
1. Щелкните правой кнопкой мыши узел задания в узле **Задания Stream Analytics** в **обозревателе сервера**. В контекстном меню выберите **Export to New Stream Analytics Project** (Экспортировать в новый проект Stream Analytics).
   
   ![Экспорт задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Вы увидите созданный проект **обозревателе решений**.
   
   ![Экспорт задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. В представлении задания щелкните **Generate Project** (Создать проект).
   
   ![Экспорт задания](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения
 
1. Локальное тестирование не работает, если запрос содержит геопространственные функции. 
2. Редактор не поддерживает добавление или изменение определяемой пользователем функции JavaScript.
3. Локальное тестирование не поддерживает сохранение выходных данных в формате JSON. 
4. Не поддерживаются выходные данные Power BI и ADLS.



## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)





<!--HONumber=Feb17_HO1-->


