<properties 
	pageTitle="Оповещения Stream Analytics | Microsoft Azure" 
	description="Общая информация об оповещениях Stream Analytics" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Настройка оповещений

## Страница «Монитор»

Правило можно настроить таким образом, чтобы в случае, если метрика достигнет указанного состояния, появлялось оповещение.

Например: «Если число выходных событий за последние 15 минут < 100, отправлять уведомления по идентификатору электронной почты: xyz@company.com”».

Правила на основе метрик можно настраивать через портал либо [программным путем](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a), используя данные в журналах операций.

## Настройка оповещений через портал Azure

Настроить оповещения можно одним из двух способов.

1.	Вкладка «Монитор» задания Stream Analytics.  
2.	Журнал операций в службах управления.  

## Настройка оповещений с помощью вкладки «Монитор» задания на портале

1.	Выберите метрику на вкладке «Монитор», нажмите кнопку «Добавить правило» в нижней части панели мониторинга и настройте правила.  

    ![Панель мониторинга](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Определение имени и описания оповещения

    ![Создание правила](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Введите пороговые значения, интервал оповещений и действия при получении оповещений.

    ![Определение условий](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Настройка оповещений с помощью журналов операций

1.	Откройте вкладку **Оповещения** в службах управления на [портале Azure](https://manage.windowsazure.com).  
2.	Нажмите **Добавить правило**.  

    ![Критерии](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Определите имя и описание оповещения. Выберите в поле «Тип службы» значение Stream Analytics, а в поле «Имя службы» — имя задания.

    ![Определение оповещения](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->