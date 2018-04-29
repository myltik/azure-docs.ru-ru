---
title: Добавление входных данных в задания Azure Stream Analytics
description: Узнайте, как подключить источник данных к заданию Stream Analytics в качестве источника потоковых входных данных из концентраторов событий или источника потоковых ссылочных данных из хранилища BLOB-объектов.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 13c3c948fbe24d5536b32967c8394060ee898377
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Подключение потоковой передачи входных или ссылочных данных к заданию Stream Analytics
Узнайте, как подключить источник данных к заданию Stream Analytics в качестве источника потоковых входных данных из концентраторов событий или источника потоковых ссылочных данных из хранилища BLOB-объектов.

Задания службы Azure Stream Analytics можно подключать к одному или нескольким источникам данных, каждый из которых определяет подключение к существующему источнику данных. Данные, отправляемые в этот источник данных, принимаются заданием Stream Analytics как потоковые данные и обрабатываются в режиме реального времен. Служба Stream Analytics обеспечивает первоклассную интеграцию с [концентраторами событий Azure](https://azure.microsoft.com/services/event-hubs/) и [хранилищем BLOB-объектов](../storage/blobs/storage-dotnet-how-to-use-blobs.md) независимо от того, входят ли они в подписку.

Эта статья представляет собой шаг [схемы обучения Stream Analytics](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Входные данные: потоковые данные и ссылочные данные
В службе Stream Analytics используют два отдельных типа входных данных: потоки данных и эталонные данные.

* **Потоки данных**: задания Stream Analytics должны включать как минимум один поток входных данных, потребляемый и изменяемый заданием. В качестве источников входных потоковых данных могут выступать хранилище больших двоичных объектов Azure и концентраторы событий Azure. Концентраторы событий Azure используются для сбора потоков событий с подключенных устройств, служб и приложений. Для приема массовых данных  в качестве потока можно использовать хранилище больших двоичных объектов Azure.  
* **Эталонные данные**: служба Stream Analytics поддерживает второй тип вспомогательных входных данных, которые называются эталонными.  В отличие от передаваемых данных, эти данные статичны или замедляют изменения.  Обычно они используются для выполнения поиска и корреляции с потоками данных для создания более плотных наборов данных.  Хранилище больших двоичных объектов Azure — единственный поддерживаемый источник входных эталонных данных.  

Чтобы добавить в задание службы Stream Analytics входные данные, выполните следующие действия.

1. На портале Azure щелкните **Входные данные**, а затем выберите **Добавление входных данных** в задании Stream Analytics.
   
    ![Портал Azure — добавление входящих данных.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    На портале Azure щелкните элемент **Входные данные** в вашем задании Stream Analytics.  
   
    ![Портал Azure — добавление входных данных.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Укажите тип входных данных: либо **Поток данных**, либо **Ссылочные данные**.
   
    ![Добавление правильных входных, потоковых или ссылочных данных](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Добавление правильных входных, потоковых или ссылочных данных](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. При создании ввода потока данных, укажите тип источника для эталонных данных.  Так как в настоящее время поддерживается только хранилище больших двоичных объектов, при создании эталонных данных этот шаг можно пропустить.
   
    ![Добавление источника потока данных](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Добавление источника потока данных](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. В поле «Псевдоним входных данных» введите понятное имя для этого источника входных данных.  Впоследствии это имя будет использоваться в запросе вашего задания для ссылки на входные данные.
   
    Укажите остальные свойства подключения, необходимые для подключения к источнику данных. 
   
    ![Добавление входных данных концентратора событий](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Укажите параметры сериализации для входных данных:
   
   * Чтобы запросы работали как следует, укажите **Формат сериализации событий** для входящих данных.  Поддерживаемые форматы сериализации: JSON, CSV и Avro. Убедитесь, что формат JSON совпадает со спецификацией и не содержит ведущий "0" в десятичных числах.
   * Проверьте **кодировку** данных.  В настоящее время единственным поддерживаемым форматом кодировки является UTF-8.
     
     ![Параметры сериализации для входных данных](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Параметры сериализации для входных данных](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Когда источник входных данных будет создан, служба Stream Analytics проверит возможность подключения к этому источнику.  Состояние операции проверки подключения можно проверить в концентраторе уведомлений.
   
    ![Проверка подключения входных потоковых данных](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Проверка подключения входных потоковых данных](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Получение справки по потоковой передаче входных данных
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

