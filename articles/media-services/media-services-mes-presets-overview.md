---
title: "Предустановки задач для Media Encoder Standard | Документация Майкрософт"
description: "Этот раздел содержит обзор предустановок задач MES (Media Encoder Standard)."
author: Juliako
manager: SyntaxC4
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: e9018ffa74c2895560adbd4c0d2b83ee67e9eb1c
ms.contentlocale: ru-ru
ms.lasthandoff: 03/03/2017

---

# <a name="task-presets-for-mes-media-encoder-standard"></a>Предустановки задач для Media Encoder Standard

**Media Encoder Standard** определяет набор предустановок кодирования, которые можно использовать при создании заданий кодирования. Рекомендуется использовать предустановку для "адаптивной потоковой передачи" при кодировании видео для потоковой передачи с помощью служб мультимедиа. Если указать эту предустановку, то Media Encoder Standard [автоматически создаст таблицу скоростей и разрешений](media-services-autogen-bitrate-ladder-with-mes.md). 

Однако, если вам необходимо изменить предустановку кодирования, то используйте одну из предустановок кодирования, заданных в этом разделе, в качестве шаблона для своей настраиваемой конфигурации. Описание каждого элемента в этих предустановках и его допустимых значений см. в разделе [Схема Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  При использовании предустановки для кодирования видео в формате 4K необходимо получить зарезервированные единицы типа `S3`. Дополнительные сведения см. в статье [Масштабирование кодирования](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
При работе с Media Encoder Standard поворот включен по умолчанию. Если видео было записано на смартфон или другое мобильное устройство в книжной ориентации, то по умолчанию эти предустановки повернут изображение, сменив его ориентацию на альбомную, перед кодированием (в отличие от кодировщика мультимедиа Azure, в котором поворот видео нужно выполнять вручную, как описано в [этом](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/) блоге, в разделе "Video Rotation" (Поворот видео)).  
  
Доступные предустановки:  
  
 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md). Данная предустановка создает набор из 8 MP4-файлов с одинаковыми группами GOP, скоростями 400–6000 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md). Данная предустановка создает набор из 8 MP4-файлов с одинаковыми группами GOP, скоростями 400–6000 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Multiple Bitrate 16x9 для iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md). Данная предустановка создает набор из 8 MP4-файлов с одинаковыми группами GOP, скоростями 200–8500 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md). Данная предустановка создает набор из 5 MP4-файлов с одинаковыми группами GOP, скоростями 400–1900 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md). Данная предустановка создает набор из 5 MP4-файлов с одинаковыми группами GOP, скоростями 400–1900 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md). Данная предустановка создает набор из 12 MP4-файлов с одинаковыми группами GOP, скоростями 1000–20 000 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md). Данная предустановка создает набор из 12 MP4-файлов с одинаковыми группами GOP, скоростями 1000–20 000 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Multiple Bitrate 4x3 для iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md). Данная предустановка создает набор из 8 MP4-файлов с одинаковыми группами GOP, скоростями 200–8500 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md). Данная предустановка создает набор из 5 MP4-файлов с одинаковыми группами GOP, скоростями 400–1600 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md). Данная предустановка создает набор из 5 MP4-файлов с одинаковыми группами GOP, скоростями 400–1600 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md). Данная предустановка создает набор из 6 MP4-файлов с одинаковыми группами GOP, скоростями 400–3400 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md). Данная предустановка создает набор из 6 MP4-файлов с одинаковыми группами GOP, скоростями 400–3400 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md). Данная предустановка создает отдельный MP4-файл со скоростью 6750 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md). Данная предустановка создает отдельный MP4-файл со скоростью 6750 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md). Данная предустановка создает отдельный MP4-файл со скоростью 18 000 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md). Данная предустановка создает отдельный MP4-файл со скоростью 18 000 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md). Данная предустановка создает отдельный MP4-файл со скоростью 1800 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md). Данная предустановка создает отдельный MP4-файл со скоростью 1800 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md). Данная предустановка создает отдельный MP4-файл со скоростью 2200 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md). Данная предустановка создает отдельный MP4-файл со скоростью 2200 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md). Данная предустановка создает отдельный MP4-файл со скоростью 4500 Кбит/с и звуком в формате AAC 5.1.  
  
 [H264 Single Bitrate 720p для Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md). Данная предустановка создает отдельный MP4-файл со скоростью 2000 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md). Данная предустановка создает отдельный MP4-файл со скоростью 4500 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate High Quality SD для Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md). Данная предустановка создает отдельный MP4-файл со скоростью 500 Кбит/с и стереофоническим звуком в формате AAC.  
  
 [H264 Single Bitrate Low Quality SD для Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md). Данная предустановка создает отдельный MP4-файл со скоростью 56 Кбит/с и стереофоническим звуком в формате AAC.  
  
 Дополнительные сведения о кодировщиках служб мультимедиа см. в разделе [Обзор и сравнение кодировщиков мультимедиа Azure по запросу](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).

