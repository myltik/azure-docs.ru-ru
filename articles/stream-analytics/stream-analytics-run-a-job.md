<properties 
	pageTitle="Выполнение задания | Microsoft Azure" 
	description="Сегмент схемы обучения по выполнению задания."
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# Выполните задание

После указания входных данных, запроса и выходных данных вы можете запустить задание Stream Analytics.

Для запуска задания:

1.	На панели мониторинга заданий портала Azure нажмите **Запустить** в нижней части страницы.

    ![Кнопка "Начало"](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    На портале предварительной версии Azure нажмите **Запустить** в верхней части страниц заданий.

    ![Кнопка запуска на портале предварительной версии Azure](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	Укажите значение параметра **Начало передачи выходных данных**, чтобы определить время, когда задание начнет выдавать выходные данные. Для заданий, которые не были запущены ранее, значение по умолчанию — **Время начала задания**, то есть задание начнет обработку данных немедленно. Вы также можете указать **Настраиваемое** время в прошлом (для использования данных за прошедший период) или в будущем (для задержки обработки до будущего времени). В случаях, когда задание было запущено и остановлено ранее, становится доступен параметр **Время последней остановки**, который позволяет возобновить задание с момента последнего вывода и избежать потери данных.

    ![Время начала](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Время начала на портале предварительной версии Azure](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	Подтвердите выбор. Состояние задания изменится на *Запуск*, а после запуска задания — на *Выполняется*. Ход выполнения операции **Запуск** можно отслеживать в **центре уведомлений**:

    ![Ход выполнения](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Ход выполнения на портале предварительной версии Azure](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->