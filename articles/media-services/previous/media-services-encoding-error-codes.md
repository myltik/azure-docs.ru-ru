---
title: Коды ошибок кодирования служб мультимедиа Azure | Документация Майкрософт
description: В этом разделе перечислены коды ошибок, которые могут быть возвращены при возникновении ошибок во время выполнения задачи кодирования.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 7a1733175f796a0d8c0c0d4247b2db2dd47e4674
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782673"
---
# <a name="encoding-error-codes"></a>Коды ошибок кодирования

В следующей таблице перечислены коды ошибок, которые могут быть возвращены при возникновении ошибок во время выполнения задачи кодирования.  Чтобы получить сведения об ошибке в коде .NET, используйте класс [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Чтобы получить сведения об ошибке в коде REST, используйте REST API [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

| ErrorDetail.Code | Возможные причины ошибки |
| --- | --- |
| Unknown |Неизвестная ошибка при выполнении задачи |
| ErrorDownloadingInputAssetMalformedContent |Категория ошибок, охватывающая ошибки загрузки входного ресурса, такие как некорректное имя файлов, файлы нулевой длины, неправильное форматирование и т. д. |
| ErrorDownloadingInputAssetServiceFailure |Категория ошибок, охватывающая проблемы на стороне службы, например проблемы с сетью или хранилищем во время загрузки. |
| ErrorParsingConfiguration |Категория ошибок, в которых задача <see cref="MediaTask.PrivateData"/> (конфигурация) является недопустимой, например: конфигурация не является допустимой конфигурацией системы или содержит недопустимый XML. |
| ErrorExecutingTaskMalformedContent |Категория ошибок, возникающих во время выполнения задачи, которые могут быть вызваны проблемами во входных файлах мультимедиа. |
| ErrorExecutingTaskUnsupportedFormat |Категория ошибок, в которой обработчик мультимедиа не может обработать представленные файлы — формат мультимедиа не поддерживается или не соответствует конфигурации. Например, при попытке получить только звук из ресурса, который содержит видео. |
| ErrorProcessingTask |Категория других ошибок обработчика мультимедиа, возникших во время обработки задачи, которые не связаны с содержимым. |
| ErrorUploadingOutputAsset |Категория ошибок при отправке выходного ресурса |
| ErrorCancelingTask |Категория ошибок, охватывающая ошибки, возникающие при попытке отменить задачу |
| TransientError |Категория ошибок, охватывающая временные проблемы (например, временные сетевые проблемы со службой хранилища Azure) |

Для получения помощи от команды разработчиков для **служб мультимедиа** создайте [запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Связанные статьи
* [Выполняйте расширенные задачи кодирования путем настройки предустановок стандартного кодировщика служб мультимедиа](media-services-custom-mes-presets-with-dotnet.md)
* [Квоты и ограничения](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
