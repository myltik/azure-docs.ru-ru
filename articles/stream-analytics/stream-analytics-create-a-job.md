<properties 
	pageTitle="Создание задания | Microsoft Azure" 
	description="Сегмент схемы обучения по созданию задания."
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
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Создание задания

Важнейшим ресурсом в службе Azure Stream Analytics является задание Stream Analytics. Оно состоит из одного или нескольких источников входных данных, запроса, определяющего преобразование данных, и одного или нескольких назначений выходных данных для записи результатов.

Чтобы начать работать со службой Stream Analytics, создайте новое задание Stream Analytics. Обратите внимание на то, что пока задание не будет запущено, на стоимости услуг оно не отразится.

1.  Выполните вход в подключенный [портал Microsoft Azure](http://manage.windowsazure.com).
2.  **Нажмите «Создать»** и последовательно выберите пункты **Службы данных**, **Stream Analytics** и **Быстрое создание**.

    ![Мастер быстрого создания](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

3.  Укажите требуемую конфигурацию для задания Stream Analytics.
	- В поле **Имя задания** введите имя для идентификации задания Stream Analytics. Как только значение поля **Имя задания** будет проверено, рядом с ним отобразится зеленая галочка. Значение в поле **Имя задания** может включать только буквы, цифры и символ «-» и должно содержать от 3 до 63 символов.
	- В поле **Регион** укажите, в каком географическом регионе должно выполняться задание.
	- В поле **Учетная запись хранения регионального мониторинга** выберите или создайте учетную запись хранения. Она будет использоваться для хранения данных мониторинга для всех заданий Stream Analytics, выполняемых в этом регионе.

4.  Настроив параметры нового задания Stream Analytics, нажмите **Создать задание Stream Analytics**. Задание Stream Analytics будет создано в течение нескольких минут. Для контроля состояния можно отслеживать ход выполнения в концентраторе уведомлений.

    ![Концентратор уведомлений](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

5.  Новое задание будет отображаться с состоянием **Создано**. Обратите внимание, что кнопка **Запуск** неактивна. Перед запуском задания необходимо настроить для него источник данных, запрос и выходные данные.

    ![Состояние задания](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->