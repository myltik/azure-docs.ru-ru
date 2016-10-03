<properties 
	pageTitle="Настройка оповещений для запросов в Stream Analytics | Microsoft Azure" 
	description="Общая информация об оповещениях Stream Analytics" 
	keywords="настройка оповещений"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>


# Настройка оповещений для заданий Azure Stream Analytics

## Введение: страница мониторинга

Оповещения можно настроить на отображение в случае, если метрика достигнет указанного состояния.

Например: "Если число выходных событий за последние 15 минут < 100, отправлять уведомления по идентификатору электронной почты: xyz@company.com".

Правила на основе метрик можно настраивать через портал либо [программным путем](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a), используя данные в журналах операций.

## Настройка оповещений через классический портал Azure

Есть два способа настройки оповещений на портале управления Azure:

1.	Вкладка **Монитор** задания Stream Analytics.
2.	Журнал операций в службах управления.

## Настройка оповещений с помощью вкладки "Монитор" задания на портале

1.	Выберите метрику на вкладке монитора, нажмите кнопку **Добавить правило** в нижней части панели мониторинга и настройте правила.

    ![Панель мониторинга](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Определение имени и описания оповещения

    ![Создание правила](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Введите пороговые значения, интервал оповещений и действия при получении оповещений.

    ![Определение условий](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Настройка оповещений с помощью журналов операций

1.	Откройте вкладку **Оповещения** в службах управления на [классическом портале Azure](https://manage.windowsazure.com).
2.	Нажмите **Добавить правило**.

    ![Критерии](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Определите имя и описание оповещения. Выберите в поле «Тип службы» значение Stream Analytics, а в поле «Имя службы» — имя задания.

    ![Определение оповещения](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Настройка оповещений на портале Azure ##

На портале Azure перейдите к нужному заданию Stream Analytics и откройте раздел **Мониторинг**. В появившейся колонке **Метрика** выберите команду **Добавить оповещение**.

  ![Настройка портала Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

Правилу оповещения можно дать имя и выбрать описание, которое будет отображаться в уведомлении по электронной почте.

Если вы выбрали раздел "Метрики", нужно задать условия и пороговое значение для метрики.

  ![Выбор метрики на портале Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Дополнительные сведения о настройке оповещений на портале Azure см. в статье [Получение уведомлений](../azure-portal/insights-receive-alert-notifications.md).

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->