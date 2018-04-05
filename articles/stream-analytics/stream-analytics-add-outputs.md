---
title: Настройка выходов данных для заданий Stream Analytics | Документация Майкрософт
description: Настройка выходных данных для заданий Stream Analytics | Сегмент схемы обучения
keywords: вывод данных, перемещение данных
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/26/2017
ms.author: jeanb
ms.openlocfilehash: 9e1a8640421c4232cf4abc09794ca584995906f9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Настройка выходов данных для заданий Stream Analytics

Задания службы Azure Stream Analytics можно подключать к одному или нескольким выходам данных, которые определяют подключение к существующему приемнику данных. По мере того как задание Stream Analytics обрабатывает и преобразует входящие данные, поток выходных данных записывается в выходные данные задания.

Выходные данные Stream Analytics могут служить источником данных для панелей мониторинга в режиме реального времени и предупреждений, запускать рабочие процессы переноса данных или просто архивировать данные для последующей пакетной обработки. Служба Stream Analytics обеспечивает первоклассную интеграцию с различными службами Azure, которые подробно описаны здесь.

Чтобы добавить выход данных в задание службы Stream Analytics, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) откройте задание и щелкните **Выходные данные**, а затем нажмите кнопку **Добавить** в появившейся колонке выходных данных.
   
    ![Добавление выходных данных](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. В поле **Псевдоним выходных данных** введите понятное имя для этого выхода данных. Впоследствии это имя можно будет использовать в запросе вашего задания для ссылки на выходные данные.  
   
    Укажите остальные свойства подключения, необходимые для подключения к выходу данных.  Эти поля зависят от типа выходных данных и подробно описаны здесь.  
   
    ![Выбор типа перемещения данных](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. В зависимости от типа выходных данных необходимо указать способ сериализации или форматирования данных. В этой статье описаны конкретные параметры сериализации для каждого выходного типа.
   
    Укажите остальные свойства подключения, необходимые для подключения к источнику данных. Эти поля зависят от типа входных данных и источника и подробно описаны в статье [Создание задания обработки аналитики данных для Stream Analytics](stream-analytics-create-a-job.md).  

> [!Note]
>
> Любой элемент вывода, добавляемый в задание, должен существовать до момента запуска задания и запуска потока событий. Например, при выводе данных в хранилище BLOB-объектов задание не создаст учетную запись хранения автоматически. Пользователь должен создать эту запись перед тем, как запустить задание ASA.
> 
 

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

