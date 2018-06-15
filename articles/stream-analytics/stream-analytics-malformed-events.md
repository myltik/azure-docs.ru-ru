---
title: Устранение неполадок в случае неправильного формата входных событий в Azure Stream Analytics | Документация Майкрософт
description: Как определить, какое событие во входных данных вызывает проблему в задании Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766383"
---
# <a name="troubleshoot-for-malformed-input-events"></a>Устранение неполадок в случае неправильного формата входных событий

Если входной поток задания Stream Analytics содержит сообщения неправильного формата, возникают проблемы сериализации. К сообщениям неправильного формата относятся случаи неверной сериализации, например отсутствие скобок в объекте JSON или неверный формат метки времени. Когда задание Stream Analytics получает неправильно сформированное сообщение, это сообщение отклоняется, а пользователь получает предупреждение. На плитке **Входные данные** задания Stream Analytics отображается символ предупреждения:

![Плитка "Входные данные"](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок

1. Перейдите к плитке входных данных и щелкните ее, чтобы просмотреть предупреждения.
2. На плитке сведений о входных данных отображается набор предупреждений с подробными сведениями о проблеме. Ниже приведен пример предупреждающего сообщения, в котором отображаются раздел, смещение и порядковые номера с данными JSON неправильного формата. 

   ![Предупреждающее сообщение со смещением](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Чтобы получить данные JSON, которые имеют неверный формат, запустите следующий фрагмент кода. Этот блок кода считывает идентификатор раздела, смещение и выводит данные. Полный пример можно найти в [репозитории примеров на GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). После считывания данных можно проанализировать и исправить формат сериализации.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Дополнительная информация

* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
