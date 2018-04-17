---
title: Создание задания обработки аналитики данных для Stream Analytics | Документация Майкрософт
description: Создание задания обработки аналитики данных для Stream Analytics | Сегмент схемы обучения.
keywords: обработка аналитики данных
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 03f21c076624ee758d313c4f58e29c616226de57
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Создание задания обработки аналитики данных для Stream Analytics
Важнейшим ресурсом в службе Azure Stream Analytics является задание Stream Analytics.  Оно состоит из одного или нескольких источников входных данных, запроса, определяющего преобразование данных, и одного или нескольких назначений выходных данных для записи результатов. Вместе они позволяют пользователю обрабатывать аналитику данных для сценариев потоковой передачи данных.

Чтобы начать работу со службой Stream Analytics, создайте новое задание Stream Analytics.  Обратите внимание, пока задание не будет запущено, на стоимости услуг оно не отразится.

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Выберите **Создать ресурс** > **Data + Analytics** (Данные и аналитика) > **Stream Analytics job** (Задание Stream Analytics).
3. Нажмите кнопку **Создать**.
   
3. Укажите требуемую конфигурацию для задания Stream Analytics.
   
   * В поле **Имя задания** введите имя для идентификации задания Stream Analytics. Как только значение поля **Имя задания** будет проверено, рядом с ним отобразится зеленая галочка. Значение в поле **Имя задания** может включать только буквы, цифры и символ "-" и должно содержать от 3 до 63 символов.
   * В поле **Расположение** укажите географическое расположение, в котором будет выполняться задание.
   * Укажите новую или существующую **группу ресурсов** для хранения связанных ресурсов для приложения.
4. Нажмите кнопку **Создать**.
Задание Stream Analytics будет создано в течение нескольких минут. Для контроля состояния можно отслеживать ход выполнения в концентраторе уведомлений.
    
   ![Создание задания обработки аналитики данных на портале Azure](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Новое задание отобразится с состоянием **Создано**. Обратите внимание, что кнопка **Запуск** неактивна. Перед запуском задания настройте для него источник данных, запрос и выходные данные.

   
   ![Состояние задания обработки аналитики данных на портале Azure](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

