---
title: "Настройка выходов данных для заданий Stream Analytics | Документация Майкрософт"
description: "Настройка выходных данных для заданий Stream Analytics | Сегмент схемы обучения"
keywords: "вывод данных, перемещение данных"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aebad3dc5b6fda757713b53edfe9a6d474b7f4cb


---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Настройка выходов данных для заданий Stream Analytics
Задания службы Azure Stream Analytics можно подключать к одному или нескольким выходам данных, которые определяют подключение к существующему приемнику данных. По мере того как задание Stream Analytics обрабатывает и преобразует входящие данные, поток выходных данных записывается в выходные данные задания.

Выходные данные Stream Analytics могут служить источником данных для панелей мониторинга в режиме реального времени и предупреждений, запускать рабочие процессы переноса данных или просто архивировать данные для последующей пакетной обработки. Служба Stream Analytics обеспечивает первоклассную интеграцию с различными службами Azure, которые подробно описаны здесь.

Чтобы добавить выход данных в задание службы Stream Analytics, выполните следующие действия.

1. На классическом портале Azure щелкните **Выходные данные**, после чего щелкните **Добавить выходные данные** в вашем задании Stream Analytics.
   
    ![Добавление выходных данных](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
    На портале Azure нажмите плитку **Выходные данные** в своем задании Stream Analytics.
   
    ![Добавление выходных данных на портале Azure](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)
2. Укажите тип выходных данных:
   
    ![Выбор типа перемещения данных](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
    ![Выбор типа перемещения данных на портале Azure](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)
3. В поле **Псевдоним выходных данных** введите понятное имя для этого выхода данных. Впоследствии это имя можно будет использовать в запросе вашего задания для ссылки на выходные данные.  
   
    Укажите остальные свойства подключения, необходимые для подключения к выходу данных.  Эти поля зависят от типа выходных данных и подробно описаны здесь.  
   
    ![Добавление свойств вывода данных](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  
4. В зависимости от типа выходных данных необходимо указать способ сериализации или форматирования данных. В этой статье описаны конкретные параметры сериализации для каждого выходного типа.
   
    Укажите остальные свойства подключения, необходимые для подключения к источнику данных. Эти поля зависят от типа входных данных и источника и подробно описаны [здесь](stream-analytics-create-a-job.md).  
   
    ![Добавление вывода данных в концентратор событий](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  
   
    ![Вывод данных в концентратор событий на портале Azure](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [!Note]
> Любой элемент вывода, добавляемый в задание, должен существовать до момента запуска задания и запуска потока событий. Например, при выводе данных в хранилище BLOB-объектов задание не создаст учетную запись хранения автоматически. Пользователь должен создать эту запись перед тем, как запустить задание ASA.
> 
> 

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


