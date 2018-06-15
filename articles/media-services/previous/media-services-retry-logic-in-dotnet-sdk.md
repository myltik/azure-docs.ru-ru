---
title: Логика повторного выполнения операций в пакете SDK служб мультимедиа для .NET | Документация Майкрософт
description: В этом разделе содержится обзор логики повторных попыток в пакете SDK служб мультимедиа для .NET.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 34125712c59938b3a74e7cdc150f3f16b694b92f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785943"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Логика повторного выполнения операций в пакете SDK служб мультимедиа для .NET
При работе со службами Microsoft Azure могут возникать временные сбои. В большинстве случаев после нескольких повторных попыток операция завершается успешно. Пакет SDK служб мультимедиа для .NET реализует логику повторного выполнения операций для обработки временных сбоев, связанных с исключениями и ошибками. Причиной этих ошибок и исключений могут быть веб-запросы, выполнение запросов, сохранение изменений и операции с хранилищем.  По умолчанию пакет SDK служб мультимедиа для .NET пытается выполнить операцию четыре раза, а затем возвращает исключение в приложение. Затем это исключение должно быть правильно обработано кодом приложения.  

 Вот краткое описание политик веб-запросов, операций с хранилищем, выполнения запросов и сохранения изменений:  

* Политика операций с хранилищем используется для операций с хранилищем BLOB-объектов (отправка и скачивание файлов ресурсов).  
* Политика веб-запросов используется для универсальных веб-запросов (например, для получения маркера аутентификации и разрешения конечной точки пользовательского кластера).  
* Политика выполнения запросов используется для отправки запросов к сущностям из REST (например, mediaContext.Assets.Where(…)).  
* Политика сохранения изменений используется для выполнения любых операций, изменяющих данные внутри службы (например, создание или обновление сущности, вызов функции службы для выполнения какого-то действия).  
  
  В этом разделе перечислены типы исключений и коды ошибок, которые обрабатываются логикой повторного выполнения операций в пакете SDK служб мультимедиа для .NET.  

## <a name="exception-types"></a>Типы исключений
В таблице ниже описаны исключения, которые пакет SDK служб мультимедиа для .NET обрабатывает или не обрабатывает для некоторых операций, которые могут вызывать временные сбои.  

| Исключение | Веб-запрос | Операции с хранилищем | Выполнение запросов | Сохранение изменений |
| --- | --- | --- | --- | --- |
| WebException<br/>Дополнительные сведения см. в разделе [Коды состояний WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |Yes |Yes |Yes |Yes |
| DataServiceClientException<br/> Дополнительные сведения см. в разделе [Коды состояний ошибок HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Нет  |Yes |Yes |Yes |
| DataServiceQueryException<br/> Дополнительные сведения см. в разделе [Коды состояний ошибок HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Нет  |Yes |Yes |Yes |
| DataServiceRequestException<br/> Дополнительные сведения см. в разделе [Коды состояний ошибок HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Нет  |Yes |Yes |Yes |
| DataServiceTransportException |Нет  |Нет  |Yes |Yes |
| TimeoutException |Yes |Yes |Yes |Нет  |
| SocketException |Yes |Yes |Yes |Yes |
| StorageException |Нет  |Yes |Нет  |Нет  |
| IOException |Нет  |Yes |Нет  |Нет  |

### <a name="WebExceptionStatus"></a> Коды состояний WebException
В таблице ниже показано, для каких кодов ошибок WebException реализована логика повторного выполнения операций. Перечисление [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) определяет коды состояний.  

| Status | Веб-запрос | Операции с хранилищем | Выполнение запросов | Сохранение изменений |
| --- | --- | --- | --- | --- |
| ConnectFailure |Yes |Yes |Yes |Yes |
| NameResolutionFailure |Yes |Yes |Yes |Yes |
| ProxyNameResolutionFailure |Yes |Yes |Yes |Yes |
| SendFailure |Yes |Yes |Yes |Yes |
| PipelineFailure |Yes |Yes |Yes |Нет  |
| ConnectionClosed |Yes |Yes |Yes |Нет  |
| KeepAliveFailure |Yes |Yes |Yes |Нет  |
| UnknownError |Yes |Yes |Yes |Нет  |
| ReceiveFailure |Yes |Yes |Yes |Нет  |
| RequestCanceled |Yes |Yes |Yes |Нет  |
| Время ожидания |Yes |Yes |Yes |Нет  |
| ProtocolError <br/>Повторное выполнение операции при состоянии ProtocolError определяется обработкой кодов состояния HTTP. Дополнительные сведения см. в разделе [Коды состояний ошибок HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Yes |Yes |Yes |Yes |

### <a name="HTTPStatusCode"></a> Коды состояний ошибок HTTP
Когда операции выполнения запросов и сохранения изменений выдают исключение DataServiceClientException, DataServiceQueryException или DataServiceQueryException, код состояния ошибки HTTP возвращается в свойстве StatusCode.  В таблице ниже показано, для каких кодов ошибок реализована логика повторного выполнения операций.  

| Status | Веб-запрос | Операции с хранилищем | Выполнение запросов | Сохранение изменений |
| --- | --- | --- | --- | --- |
| 401 |Нет  |Yes |Нет  |Нет  |
| 403 |Нет  |Yes<br/>Обработка повторных попыток с увеличением времени ожидания. |Нет  |Нет  |
| 408 |Yes |Yes |Yes |Yes |
| 429 |Yes |Yes |Yes |Yes |
| 500 |Yes |Yes |Yes |Нет  |
| 502 |Yes |Yes |Yes |Нет  |
| 503 |Yes |Yes |Yes |Yes |
| 504 |Yes |Yes |Yes |Нет  |

Фактическую реализацию логики повторного выполнения операций в пакете SDK служб мультимедиа для .NET см. [здесь](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Дополнительная информация
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

