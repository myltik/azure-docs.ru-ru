---
title: "Основные сведения о службе Stream Analytics и журналах операций | Документация Майкрософт"
description: "Порядок работы с журналами операций Stream Analytics"
keywords: "журналы служб"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e694eb8f4d658ff9410ae7be2bd4b69500ecbf20


---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Основные сведения о службе Stream Analytics и журналах операций
Все службы Azure предоставляют пользователям сообщения операционного журнала для записи данных, связанных с операциями управления. В службе Azure Stream Analytics эти данные можно использовать в целях отладки, например для просмотра состояния задания, хода его выполнения и сообщений об ошибках, а также для отслеживания хода выполнения задания с течением времени, от запуска до обработки и получения выходных данных.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Поиск журналов операций на портале управления Azure
Доступ к журналам операций можно получить одним из двух способов.  

* Панель мониторинга задания Stream Analytics  
* Службы управления на классическом портале Azure  

## <a name="dashboard-of-the-stream-analytics-job"></a>Панель мониторинга задания Stream Analytics
Ссылка на соответствующие журналы задания Stream Analytics отображается на вкладке панели мониторинга задания. При переходе по этой ссылке фильтры настраиваются на отображение последних журналов соответствующего задания.

  ![Выбор журналов служб управления](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Службы управления
Чтобы перейти к журналам операций Stream Analytics и других служб на классическом портале Azure, выполните следующие действия:

1. На **классическом портале Azure** щелкните [Службы управления](https://manage.windowsazure.com).
2. Выберите значение **Stream Analytics** в поле **Тип** и имя задания в поле **Имя службы**.  
   
   ![Выбор Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Поиск журналов аудита на портале Azure
Чтобы найти на портале Azure журналы операций для задания Stream Analytics, щелкните **Обзор** и выберите **Журналы аудита**.

  ![Выбор Stream Analytics на портале Azure](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Откроется колонка с событиями за последние 7 дней для всех ресурсов в подписке.  Вы можете применить фильтр, чтобы просмотреть события определенного типа или за указанный временной интервал, выбрав команду **Фильтр** .

  ![Выбор Stream Analytics на портале Azure](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Получение подробных сведений журнала
Для просмотра журналов задания можно использовать фильтры "Диапазон времени" и "Состояние".

На портале управления Azure нажмите кнопку **Сведения** в нижней части окна, чтобы увидеть дополнительные сведения о выбранном событии. 

  ![Выбор сведений](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

На портале Azure щелкните раздел журнала, чтобы просмотреть включенные в него события.

  ![Выбор подробных сведений на портале Azure](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

Здесь вы можете открыть колонку **Сведения** , щелкнув событие.

  ![Выбор подробных сведений на портале Azure](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Отладка невыполненного задания
На портале управления Azure нажмите значок поиска и введите "failed". Это позволит получить все журналы с ошибками. 

  ![Отладка задания с ошибкой](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

На портале Azure журналы можно отфильтровать по уровню сообщений и просмотреть только **критические** события.

  ![Отладка на портале Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Выберите одну из ошибок, нажмите кнопку **Сведения** и ознакомьтесь с дополнительными сведениями об ошибке.  Некоторые сообщения об ошибках содержат также информацию о том, как устранить проблему. 

  ![Сведения об операции](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Если вам нужно связаться со [службой поддержки](https://azure.microsoft.com/support/options/) или передать информацию специалистам через [форум MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), обратите внимание на раздел "Сведения об операции", особенно на **Идентификатор корреляции**. 

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


