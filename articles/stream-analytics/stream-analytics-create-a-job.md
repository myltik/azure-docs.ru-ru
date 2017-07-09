---
title: "Создание задания обработки аналитики данных для Stream Analytics | Документация Майкрософт"
description: "Создание задания обработки аналитики данных для Stream Analytics | Сегмент схемы обучения."
keywords: "обработка аналитики данных"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 2e989ab0cb95a1caef44b2f235b162f2c12a79cc
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017


---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Создание задания обработки аналитики данных для Stream Analytics
Важнейшим ресурсом в службе Azure Stream Analytics является задание Stream Analytics.  Оно состоит из одного или нескольких источников входных данных, запроса, определяющего преобразование данных, и одного или нескольких назначений выходных данных для записи результатов. Вместе они позволяют пользователю обрабатывать аналитику данных для сценариев потоковой передачи данных.

Чтобы начать работу со службой Stream Analytics, создайте новое задание Stream Analytics.  Обратите внимание на то, что пока задание не будет запущено, на стоимости услуг оно не отразится.

1. Войдите на [классический портал Azure](http://manage.windowsazure.com) или [портал Azure](https://portal.azure.com/).
2. На портале щелкните **Создать**, щелкните **Службы данных** или **Аналитика данных** (в зависимости от портала), а затем щелкните **Azure Stream Analytics** или **Stream Analytics** и выберите **Быстрое создание**.
   
   ![Мастер заданий обработки аналитики данных](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Создание задания обработки аналитики данных](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Укажите требуемую конфигурацию для задания Stream Analytics.
   
   * В поле **Имя задания** введите имя для идентификации задания Stream Analytics. Как только значение поля **Имя задания** будет проверено, рядом с ним отобразится зеленая галочка. Значение в поле **Имя задания** может включать только буквы, цифры и символ "-" и должно содержать от 3 до 63 символов.
   * Используйте поле **Регион** или **Расположение** на портале Azure, чтобы указать географическое расположение для запуска задания.
   * Если используется портал Azure, выберите или создайте учетную запись хранения для использования в качестве **учетной записи хранения регионального мониторинга**. Она будет использоваться для хранения данных мониторинга для всех заданий Stream Analytics, выполняемых в этом регионе.
   * Если вы используете портал Azure, укажите новую или существующую **группу ресурсов** для хранения связанных ресурсов для приложения.
4. Настроив параметры нового задания Stream Analytics, нажмите **Создать задание Stream Analytics**. Задание Stream Analytics будет создано в течение нескольких минут. Для контроля состояния можно отслеживать ход выполнения в концентраторе уведомлений.
   
   ![Концентратор уведомлений для задания обработки аналитики данных](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Создание задания обработки аналитики данных на портале Azure](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Новое задание будет отображаться с состоянием **Создано**. Обратите внимание, что кнопка **Запуск** неактивна. Перед запуском задания необходимо настроить для него источник данных, запрос и выходные данные.
   
   ![Состояние задания обработки аналитики данных](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Состояние задания обработки аналитики данных на портале Azure](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


