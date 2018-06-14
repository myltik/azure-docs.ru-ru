---
title: Рекомендации по обработке ошибок службы "Функции Azure" | Документация Майкрософт
description: Содержит общие рекомендации по обработке ошибок, возникающих в ходе выполнения функций, а также ссылки на разделы об ошибках, связанных с привязкой.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: cfowler
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29133701"
---
# <a name="azure-functions-error-handling"></a>Обработка ошибок службы "Функции Azure"

Этот раздел содержит общие рекомендации по обработке ошибок, возникающих в ходе выполнения функций. Он также содержит ссылки на разделы, в которых описываются ошибки привязки, которые могут возникнуть. 

## <a name="handing-errors-in-functions"></a>Обработка ошибок в функциях
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Коды ошибок привязки

При интеграции со службами Azure могут возникать ошибки, поступающие из интерфейсов API базовых служб. Ссылки на документацию по кодам ошибок для этих служб можно найти в подразделах **Исключения и коды возврата** следующих справочных разделов, посвященных триггерам и привязкам.

+ [База данных Azure Cosmos](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Хранилище BLOB-объектов](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Концентраторы событий](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранилище очередей](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Хранилище таблиц](functions-bindings-storage-table.md#exceptions-and-return-codes)
