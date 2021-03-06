---
title: включение файла
description: включение файла
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
В следующей таблице перечислены квоты и ограничения для [концентраторов событий Azure](https://azure.microsoft.com/services/event-hubs/). См. сведения о [ценах на концентраторы событий](https://azure.microsoft.com/pricing/details/event-hubs/).

| Ограничение | Область | Заметки | Значение |
| --- | --- | --- | --- | --- |
| Число концентраторов событий на пространство имен |Пространство имен |Последующие запросы на создание концентратора событий будут отклонены. |10 |
| Число разделов на концентратор событий |Сущность |- |32 |
| Число групп потребителей на концентратор событий |Сущность |- |20 |
| Число подключений AMQP на пространство имен |Пространство имен |Запросы на дополнительные соединения отклоняются, а вызывающий код получает исключение. |5 000 |
| Максимальный размер события концентраторов событий|Сущность |- |256 KB |
| Максимальный размер имени концентратора событий |Сущность |- |50 символов |
| Число получателей вне эпохи на группу потребителей |Сущность |- |5 |
| Максимальный срок хранения данных о событиях |Сущность |- |1–7 дн. |
| Максимальное число единиц пропускной способности |Пространство имен |Превышение ограничения для единиц пропускной способности приводит к регулированию данных и созданию исключения **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Вы можете запросить большее число единиц пропускной способности для уровня "Стандартный", отправив запрос в [службу поддержки](/azure/azure-supportability/how-to-create-azure-support-request). [Дополнительные единицы пропускной способности](../articles/event-hubs/event-hubs-auto-inflate.md) доступны в блоках по 20 единиц на основе подтвержденной покупки. |20 |
| Число правил авторизации на каждое пространство имен |Пространство имен|Последующие запросы на создание правила авторизации будут отклонены.|12 |
