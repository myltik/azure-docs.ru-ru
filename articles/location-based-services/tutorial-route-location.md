---
title: "Поиск маршрута с помощью Azure Location Based Services | Документация Майкрософт"
description: "Поиск маршрута к объекту с помощью Azure Location Based Services"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7303347444952d9c09dc6c04eea5b962e18729b4
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Поиск маршрута к объекту с помощью Azure Location Based Services.

В этом руководстве показано, как использовать учетную запись Azure Location Based Services и пакет SDK службы построения маршрутов, чтобы найти маршрут к объекту. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Получение координат адреса.
> * Отправка запроса к службе построения маршрутов поиска маршрута к объекту.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем продолжить, убедитесь, что [учетная запись Azure Location Based Services создана](./tutorial-search-location.md#createaccount) и у вас есть [ключ подписки для учетной записи](./tutorial-search-location.md#getkey). Вы можете также узнать, как использовать интерфейсы API Map Control и службы поиска, из руководства [Поиск ближайшего объекта с помощью Azure Location Based Services](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Получение координат адреса.

Чтобы создать статическую HTML-страницу со встроенным API Map Control API Location Based Services, сделайте следующее. 

1. На локальном компьютере создайте файл **MapRoute.html**. 
2. Добавьте в него следующие компоненты HTML.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Обратите внимание на то, как заголовок HTML внедряет расположения ресурсов для CSS-файлов и файлов JavaScript из библиотеки Azure Location Based Services. Кроме того, обратите внимание на сегмент *script* в блоке body HTML-файла, предназначенный для внутреннего кода JavaScript, используемого для доступа к интерфейсам API Azure Location Based Services.

3. Добавьте следующий код JavaScript в блок *script* HTML-файла. Используйте первичный ключ из учетной записи Location Based Services в скрипте.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты и является компонентом API Azure Map Control.

4. Добавьте следующий код JavaScript в блок *script*. Он добавляет слой объектов *LineString* в Map Control для отображения маршрута.

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

5. Добавьте следующий код JavaScript для создания начальной и конечной точек маршрута.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Этот код создает два [объекта GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), представляющих начальную и конечную точки маршрута. Конечная точка — это комбинация широты и долготы одной из *бензозаправочных станций*, поиск которых выполнялся в предыдущем руководстве, [Поиск ближайшего объекта с помощью Azure Location Based Services](./tutorial-search-location.md).

6. Добавьте следующий код JavaScript для добавления на карту меток начальной и конечной точек.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds** API корректирует окно карты в соответствии с координатами начальной и конечной точек. **map.addPins** API добавляет точки в Map Control в виде визуальных компонентов.

7. Сохраните файл **MapRoute.html** на компьютере. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Отправка запроса к службе построения маршрутов поиска маршрута к объекту.

В этом разделе показано, как использовать API службы построения маршрутов Azure Location Based Services для поиска маршрута из заданной начальной точки к точке назначения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого быстрого, краткого или экономичного маршрута между двумя расположениями с учетом трафика в режиме реального времени. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. 

1. Откройте файл **MapRoute.html**, созданный в предыдущем разделе, и добавьте следующий код JavaScript в блок *script* для демонстрации работы службы построения маршрутов.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    Этот фрагмент кода создает [XMLHttpRequest](https://xhr.spec.whatwg.org/) и добавляет обработчик событий для анализа входящего ответа. При получении успешного ответа он создает массив координат для сегментов линии первого возвращенного маршрута. Затем он добавляет этот набор координат для данного маршрута на слой карты с объектами *LineString*.

2. Добавьте следующий код в блок *script* для отправки XMLHttpRequest в службу построения маршрутов Azure Location Based Services.

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    В запросе выше показаны обязательные параметры. Это ключ вашей учетной записи и координаты начальной и конечной точек (в заданном порядке). 

3. Сохраните файл **MapRoute.html** на локальном компьютере, а затем откройте его в веб-браузере по своему усмотрению и просмотрите результаты. Для успешного подключения к интерфейсам API Location Based Services должна использоваться карта следующего вида. 

    ![Azure Map Control и служба построения маршрутов](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Получение координат адреса.
> * Отправка запроса к службе построения маршрутов поиска маршрута к объекту.

Перейдите к руководству [Поиск маршрутов для различных способов перемещения с помощью Azure Location Based Services](./tutorial-prioritized-routes.md), чтобы научиться использовать Azure Location Based Services для установки приоритетов маршрутов к объектам в соответствии со способом перемещения. 
