---
title: Использование нескольких маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ea68c70befde1922fc30d06386a2ae326460c722
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601417"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"

В этом руководстве показано, как использовать учетную запись службы "Карты Azure" и службу построения маршрутов, чтобы найти маршрут к объекту с учетом приоритета способа перемещения. На карте отображается два разных маршрута: один для легковых автомобилей, а другой для грузовиков, маршрут которых может ограничиваться из-за высоты, веса или опасного груза. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Визуализация потока трафика на карте.
> * Создание запросов маршрута по видам транспорта.
> * Отображение нескольких маршрутов на карте.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем продолжить, выполните действия из первого руководства, чтобы [создать учетную запись службы "Карты Azure"](./tutorial-search-location.md#createaccount) и [получить для нее ключ подписки](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Создание карты 
Чтобы создать статическую HTML-страницу со встроенным API элементов управления картой, сделайте следующее. 

1. На локальном компьютере создайте файл **MapTruckRoute.html**. 
2. Добавьте в него следующие компоненты HTML.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    Заголовок HTML внедряет расположения ресурсов для CSS-файлов и файлов JavaScript из библиотеки службы "Карты Azure". Сегмент *script* в тексте HTML-файла будет содержать встроенный код JavaScript для карты.
3. Добавьте следующий код JavaScript в блок *script* HTML-файла. Замените строку **\<your account key\>** первичным ключом, скопированным из учетной записи службы "Карты Azure".

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты и является компонентом API Azure Map Control.

4. Сохраните файл и откройте его в браузере. На этом этапе у вас есть базовая карта, которую можно будет дополнительно доработать. 

   ![Просмотр базовой карты](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Визуализация потока трафика

1. Если вы не укажите на карте конкретный маршрут, отобразится представление целого мира. Чтобы иметь возможность просматривать данные трафика, задайте центральную точку и уровень масштаба на карте. Замените код, который объявляет `new atlas.Map`, следующим кодом JavaScript: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Этот код по умолчанию задает центральную точку для карты и объявляет уровень масштаба, чтобы вы имели возможность сосредоточиться на конкретной области. 

1. Добавьте отображение потока трафика на карту:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Этот код задает для потока трафика значение `relative`. Это скорость на дороге относительно безостановочного потока. Вы также можете задать скорость `absolute` или `relative-delay`, тогда будут отображаться отличия относительной скорости от безостановочного потока. 

2. Сохраните файл **MapTruckRoute.html** и обновите страницу браузера. Должны отобразиться улицы Лос-Анджелеса с текущими данными трафика.

   ![Просмотр карты с трафиком](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Установка начальных и конечных точек

В этом руководстве задайте в качестве начальной точки вымышленную компанию Fabrikam в Сиэтле, а в качестве пункта назначения — офис корпорации Майкрософт. 

1. Добавьте следующий код JavaScript для создания меток начальной и конечной точек маршрута.

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Этот код создает два [объекта GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), представляющих начальную и конечную точки маршрута. 

2. Добавьте следующий код JavaScript для добавления на карту начальной и конечной точек.

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    Вызов **map.setCameraBounds** корректирует окно карты в соответствии с координатами начальной и конечной точек. **map.addPins** API добавляет точки в Map Control в виде визуальных компонентов.

3. Сохраните файл и обновите браузер, чтобы на карте отобразились пометки. Несмотря на то, что вы определили на карте центральную точку в Лос-Анджелесе, **map.setCameraBounds** передвинуло представление, чтобы отобразить начальную и конечную точку. 

   ![Просмотр карт с начальной и конечной точкой](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Построение маршрутов учетом приоритета способа перемещения

В этом разделе показано, как использовать API службы построения маршрутов "Карты" для поиска нескольких маршрутов из заданной начальной точки к точке назначения в соответствии со способом перемещения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого *быстрого*, *краткого*, *экономичного* или *захватывающего* маршрута между двумя расположениями с текущими условиями трафика. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. Дополнительные сведения см. в статье [Route — Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Маршрут. Получение направления маршрута).


1. Во-первых, добавьте новый слой на карту, чтобы отобразить путь маршрута (или *linestring*). В этом руководстве приведено два разных маршрута **car-route** и **truck-route**, каждый из которых имеет собственный стиль. Добавьте следующий код JavaScript в блок *script*.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. Добавьте следующий код JavaScript в блок *script*, чтобы запросить маршрут для грузовика и отобразить результаты на карте.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Этот фрагмент кода создает [XMLHttpRequest](https://xhr.spec.whatwg.org/) и добавляет обработчик событий для анализа входящего ответа. Получив успешный ответ, он создает массив координат для полученного маршрута и добавляет его на слой карты `truckRouteLayerName`. 
    
    Фрагмент кода также создает запрос для службы построения маршрутов "Карты" с помощью ключа учетной записи. Запрос содержит координаты начальной и конечной точки и дополнительные параметры, указывающие, что маршрут предназначен для тяжелого грузовика.

2. Добавьте следующий код JavaScript, чтобы запросить маршрут для легкового автомобиля и отобразить результаты.

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Этот фрагмент кода создает еще один [XMLHttpRequest](https://xhr.spec.whatwg.org/) и добавляет обработчик событий для анализа входящего ответа. Получив успешный ответ, он создает массив координат для полученного маршрута и добавляет его на слой карты `carRouteLayerName`. 
    
    Фрагмент кода также создает запрос для службы построения маршрутов "Карты" с помощью ключа учетной записи. Запрос содержит координаты начальной и конечной точки. Так как дополнительные параметры не указаны, служба построения маршрута по умолчанию определяет в качестве способа передвижения *легковой автомобиль*. 

3. Сохраните файл **MapTruckRoute.html** и обновите браузер, чтобы просмотреть результат. Для успешного подключения к интерфейсам API службы "Карты Azure" должна использоваться карта следующего вида. 

    ![Поиск маршрутов с учетом приоритета с помощью службы построения маршрутов Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Линия маршрута грузовика голубая и более толстая, а линия маршрута легкового автомобиля сиреневая и более тонкая. Маршрут легкового автомобиля проходит через туннели на озере Вашингтон (федеральная автострада номер 90), которые расположены в жилых районах. Поэтому здесь запрещается перевозить опасные отходы. Маршрут грузовика, указывающий тип груза USHazmatClass2, использует другую магистраль. 

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Визуализация потока трафика на карте.
> * Создание запросов маршрута по видам транспорта.
> * Отображение нескольких маршрутов на карте.

Дополнительные сведения об охвате и возможностях службы "Карты Azure" см. в статье [об уровнях увеличения и параметрами сетки](zoom-levels-and-tile-grid.md) и других концептуальных статьях. 

Дополнительные примеры кода и сведения о возможностях интерактивного программирования см. в статье [How to use the Azure Maps Map Control](how-to-use-map-control.md) (Как использовать библиотеку Map Control в службе "Карты Azure") и других практических руководствах. 
