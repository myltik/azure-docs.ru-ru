---
title: Отправка заданий обрезки из Azure Media Clipper | Документация Майкрософт
description: Действия для отправки заданий обрезки из Azure Media Clipper
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783183"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Отправка заданий обрезки из Azure Media Clipper
Чтобы обрабатывать отправку заданий обрезки, для Azure Media Clipper требуется реализация метода **submitSubclipCallback**. Эта функция служит для реализации отправки методом HTTP POST выходных данных Clipper в веб-службу. Эта веб-служба является местом, куда можно отправить задание кодирования. Выходные данные Clipper представляют собой либо предустановку кодирования Media Encoder Standard для преобразованных для просмотра заданий, либо полезные данные REST API для динамических вызовов фильтров манифестов. Такая модель сквозной передачи необходима, так как данные учетной записи служб мультимедиа не защищены в браузере клиента.

На следующей схеме последовательностей показан рабочий процесс между клиентским браузером, веб-службой и службами мультимедиа Azure: ![Схема последовательностей Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

На предыдущей схеме показано четыре сущности: браузер пользователя, ваша веб-служба, конечная точка CDN, на которой размещены ресурсы Clipper, и службы мультимедиа Azure. Когда конечный пользователь переходит на веб-страницу, страница получает ресурсы JavaScript Clipper и CSS из конечной точки размещения в CDN. Пользователь настраивает в браузере задание обрезки или вызов для создания фильтра динамического манифеста. Когда пользователь отправляет задание или вызов для создания фильтра, браузер помещает полезные данные задания в веб-службу, которую необходимо развернуть. Затем эта веб-служба отправляет задание обрезки или вызов для создания фильтра в службы мультимедиа Azure, используя учетные данные учетной записи служб мультимедиа.

В следующем примере кода показан пример метода **submitSubclipCallback**. В этом методе следует реализовать отправку HTTP POST предустановки кодирования Media Encoder Standard. Если запрос POST был выполнен успешно (**result**), параметр **Promise** разрешается. В противном случае он отклоняется с сообщением об ошибке.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
Выходные данные отправки задания представляют собой либо предустановку кодирования Media Encoder Standard для задания, преобразованного для просмотра, либо полезные данные REST API для фильтров динамических манифестов.

## <a name="submitting-encoding-job-to-create-video"></a>Отправка задания кодирования для создания видео
Вы можете отправить задание кодирования Media Encoder Standard, чтобы создать видеоклип с обрезкой с точностью до кадра. Задание кодирования создает преобразованные для просмотра видео в формате нового фрагментированного MP4-файла.

Контракт на вывод задания для выводимой обрезки представляет собой объект JSON со следующими свойствами:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Чтобы выполнить задание кодирования, отправьте задание кодирования Media Encoder Standard со связанной предустановкой. В этой статье приведены сведения об отправке заданий кодирования с помощью [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) или [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Быстро создание видеоклипов без кодирования
Вместо того чтобы создавать задание кодирования, можно с помощью Azure Media Clipper создать фильтры динамического манифеста. Для фильтров не требуется кодирование, они быстро создаются, так как не нужно создавать новый ресурс. Контракт на вывод для обрезки с фильтром представляет собой объект JSON со следующими свойствами:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Чтобы отправить вызов REST для создания фильтра динамического манифеста, отправьте связанные полезные данные фильтра с помощью [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).