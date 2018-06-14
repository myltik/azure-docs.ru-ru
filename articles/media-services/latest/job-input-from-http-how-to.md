---
title: Создание входных данных задания Служб мультимедиа Azure из URL-адреса HTTP (HTTPS) | Документация Майкрософт
description: В этой статье показано, как создать входные данные задания из URL-адреса HTTP (HTTPS).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159933"
---
# <a name="create-a-job-input-from-an-https-url"></a>Создание входных данных задания из URL-адреса HTTP (HTTPS)

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это можно сделать, указав URL-адрес HTTP (HTTPS) как входные данные задания (как показано в этом примере). Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Пример кода .NET

В следующем коде показано, как создать задание с помощью входных данных URL-адреса HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Дополнительная информация

[Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md).
