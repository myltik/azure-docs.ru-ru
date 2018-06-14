---
title: Общие сведения о хранилище очередей Azure | Документация Майкрософт
description: Общие сведения о хранилище очередей Azure
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 792e8c7efb2a627fbc1abde1389015949d5931e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059589"
---
# <a name="introduction-to-queues"></a>Общие сведения об очередях

Хранилище очередей Azure — это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения.

## <a name="common-uses"></a>Распространенные варианты использования

Наиболее частые способы использования хранилища очередей включают:

* создание списка невыполненных работ для асинхронной обработки;
* передачу сообщений из веб-роли Azure в рабочую роль Azure.

## <a name="queue-service-concepts"></a>Основные понятия службы очередей

Служба очереди содержит следующие компоненты:

![Понятия очереди](./media/storage-queues-introduction/queue1.png)

* **Формат URL-адреса**. К очереди можно обратиться, используя следующий формат URL-адреса:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Следующий URL-адрес позволяет обратиться к очереди на схеме:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Учетная запись хранения.** Доступ к службе хранилища Azure всегда осуществляется с помощью учетной записи хранения. Сведения об емкости учетной записи хранения см. в статье [Целевые показатели масштабируемости и производительности службы хранилища Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Очередь**. Очередь содержит набор сообщений. Все сообщения должны находиться в очереди. Обратите внимание: имя очереди должно содержать только строчные символы. Дополнительные сведения см. в статье о [присвоении имен очередям и метаданным](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Сообщение.** Сообщение в любом формате размером до 64 КБ. Сообщение может оставаться в очереди не более 7 дней.

## <a name="next-steps"></a>Дополнительная информация

* [создать учетную запись хранения;](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Начало работы с очередями с использованием .NET](storage-dotnet-how-to-use-queues.md)
