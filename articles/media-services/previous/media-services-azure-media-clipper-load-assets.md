---
title: Загрузка ресурсов в Azure Media Clipper | Документация Майкрософт
description: Шаги по загрузке ресурсов в Azure Media Clipper
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782903"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Загрузка ресурсов в Azure Media Clipper
Ресурсы можно загрузить в Azure Media Clipper двумя способами:
1. Cтатическая передача библиотеки ресурсов.
2. Динамическое создание списка ресурсов через API-интерфейс.

## <a name="statically-load-videos-into-clipper"></a>Статическая загрузка видео в Clipper
Используйте статическую загрузку видео в Clipper, чтобы пользователи могли создавать клипы, не выбирая видео в области выбора ресурсов.

В этом случае в Clipper передается статический набор ресурсов. Каждый ресурс включает в себя идентификатор ресурса или фильтра AMS, имя, опубликованный URL-адрес потоковой передачи. Если применимо, может передаваться маркер аутентификации для защиты содержимого или массив URL-адресов эскизов. При передаче эскизов ими заполняется интерфейс. В сценариях, когда библиотека ресурсов статическая и небольшая, можно передать контракт для каждого ресурса в библиотеке.

> [!NOTE]
> При статической загрузке в Clipper ресурсы добавляются **прямо на временную шкалу** и **область ресурсов не преобразуется для просмотра**. Первый ресурс добавляется на временную шкалу, а остальные ресурсы последовательно размещаются с правой стороны временной шкалы.

Для загрузки статической библиотеки ресурсов используйте метод **load**, чтобы передать JSON-представление каждого ресурса. В следующем примере кода показано JSON-представление одного ресурса.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Рекомендуется связать вызов метода load() с методом ready(handler), как показано в предыдущем примере. Этот пример обеспечивает готовность мини-приложения перед загрузкой ресурсов.

> [!NOTE]
> Для работы URL-адресов эскизов ожидаемым образом на временной шкале Clipper они должны быть равномерно распределены по видео (в зависимости от длительности) и в хронологическом порядке в пределах массива. Вы можете воспользоваться следующим предустановленным фрагментом JSON в качестве примера для создания изображений с помощью процессора Media Encoder Standard:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Динамическая загрузка видео в Clipper
Используйте динамическую загрузку видео в Clipper, чтобы пользователи могли выбирать видео на панели выбора ресурсов для создания клипов.

Альтернативным способом является динамическая загрузка ресурсов посредством обратного вызова. В сценариях, когда ресурсы создаются динамически или библиотека имеет большой размер, следует выполнять загрузку через обратный вызов. Для динамической загрузки ресурса необходимо реализовать необязательную функцию обратного вызова onLoadAssets. Эта функция передается в Clipper при инициализации. Разрешенные ресурсы должны придерживаться того же контракта, что и статически загружаемые. В следующем примере кода показана сигнатура метода, ожидаемые входные данные и ожидаемые выходные данные.

> [!NOTE]
> При динамической загрузке в Clipper ресурсы преобразуются для просмотра в **области выбора ресурсов**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```