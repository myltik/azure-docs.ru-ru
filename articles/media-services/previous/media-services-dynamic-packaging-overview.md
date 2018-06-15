---
title: Обзор динамической упаковки служб мультимедиа Azure | Документация Майкрософт
description: В этой статье представлены общие сведения о технологии динамической упаковки.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 8f05015da1f66331413086c0e27c25cd5da75f5c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783033"
---
# <a name="dynamic-packaging"></a>Динамическая упаковка
## <a name="overview"></a>Обзор
Службы мультимедиа Microsoft Azure можно использовать для передачи содержимого исходных файлов, потоков мультимедиа и защищенного содержимого в различных форматах на вход клиентов, реализованных на базе различных технологий (например, iOS, XBOX, Silverlight или Windows 8). Эти клиенты работают по разным протоколам: например, для iOS используется формат потоковой трансляции HTTP (HLS) V4, а для технологий Silverlight и Xbox необходимо использовать формат Smooth Streaming. Если у вас есть набор файлов MP4 (ISO Base Media 14496-12) с адаптивной скоростью (мультискоростных) или аналогичных файлов Smooth Streaming, которые вам нужно передать клиентам, принимающим форматы MPEG DASH, HLS или Smooth Streaming, вы можете воспользоваться функциями динамической упаковки служб мультимедиа.

При работе с этими функциями достаточно создать ресурс, содержащий набор мультискоростных MP4-файлов или файлов Smooth Streaming. Затем с учетом формата, указанного в манифесте или запросе фрагмента, сервер потоковой передачи по запросу организует передачу содержимого по выбранному протоколу. В результате вы сможете хранить и оплачивать файлы только в одном формате, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента.

На схеме ниже показан рабочий процесс, в котором используются традиционные технологии кодирования и статической упаковки.

![Статическое кодирование](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

На схеме ниже показан рабочий процесс, в котором используются технологии динамической упаковки.

![Динамическое кодирование](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>Стандартный сценарий
1. Отправьте входной файл (он также называется мезонинным). Например, это может быть файл в формате H.264, MP4 или WMV (список поддерживаемых форматов см. в статье [Форматы и кодеки стандартного кодировщика служб мультимедиа](media-services-media-encoder-standard-formats.md)).
2. Преобразуйте мезонинный файл в набор мультискоростных MP4-файлов в формате H.264.
3. Опубликуйте ресурс, содержащий набор мультискоростных MP4-файлов, создав указатель OnDemand.
4. Создайте URL-адреса для доступа к содержимому и его потоковой передачи.

## <a name="preparing-assets-for-dynamic-streaming"></a>Подготовка ресурсов для динамической потоковой передачи
Подготовить ресурс для динамической потоковой передачи можно двумя способами.

1. [Отправьте главный файл](media-services-dotnet-upload-files.md).
2. [Сформируйте наборы мультискоростных MP4-файлов в формате H.264 с помощью стандартного кодировщика служб мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Выполните потоковую передачу содержимого](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Форматы, не поддерживаемые динамической упаковкой
Перечисленные ниже форматы файлов не поддерживаются технологией динамической упаковки.

* MP4-файлы в формате Dolby Digital.
* Файлы Smooth в формате Dolby Digital.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

