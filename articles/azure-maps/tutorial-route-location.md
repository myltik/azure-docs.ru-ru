---
title: Поиск маршрута с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск маршрута к точке интереса с помощью службы "Карты Azure"
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fc5dfafec303a439d8a1092771fd2247ab305172
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601349"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Поиск маршрута к точке интереса с помощью службы "Карты Azure"

В этом руководстве показано, как использовать учетную запись службы "Карты Azure" и пакет SDK службы построения маршрутов, чтобы найти маршрут к объекту. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Настройка координат адреса.
> * Отправка запроса к службе построения маршрутов поиска маршрута к объекту.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем продолжить, выполните действия из предыдущего руководства, чтобы [создать учетную запись службы "Карты Azure"](./tutorial-search-location.md#createaccount) и [получить для нее ключ подписки](./tutorial-search-location.md#getkey). 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Создание карты 
Чтобы создать статическую HTML-страницу со встроенным API элементов управления картой, сделайте следующее. 

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
    Заголовок HTML внедряет расположения ресурсов для CSS-файлов и файлов JavaScript из библиотеки службы "Карты Azure". Сегмент *script* в блоке body HTML-файла, предназначенный для внутреннего кода JavaScript, используемого для доступа к интерфейсам API службы "Карты Azure".

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

   ![Просмотр базовой карты](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Установка начальных и конечных точек

В этом руководстве задайте в качестве начальной точки офис корпорации Майкрософт, а в качестве пункта назначения — заправку в Сиэтле. 

1. В том же блоке *script* файла **MapRoute.html** добавьте следующий код JavaScript для создания начальной и конечной точек маршрута.

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
    Этот код создает два [объекта GeoJSON](https://en.wikipedia.org/wiki/GeoJSON), представляющих начальную и конечную точки маршрута. 

2. Добавьте следующий код JavaScript для добавления на карту меток начальной и конечной точек.

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
    **map.setCameraBounds** корректирует окно карты в соответствии с координатами начальной и конечной точек. **map.addPins** API добавляет точки в Map Control в виде визуальных компонентов.

7. Сохраните файл **MapRoute.html** и обновите страницу в браузере. Теперь на карте будет крупным планом показан Сиэтл. Вы можете видеть круглую голубую пометку, обозначающую начальную точку, и голубую пометку, обозначающую конечную точку.

   ![Просмотр карт с помеченными начальной и конечной точкой](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Получение направлений

В этом разделе показано, как использовать API службы построения маршрутов "Карты" для поиска маршрута из заданной начальной точки к точке назначения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого *быстрого*, *краткого*, *экономичного* или *захватывающего* маршрута между двумя расположениями. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. Дополнительные сведения см. в статье [Route — Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Маршрут. Получение направления маршрута).


1. Во-первых, добавьте новый слой на карту, чтобы отобразить путь маршрута (или *linestring*). Добавьте следующий код JavaScript в блок *script*.

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

2. Создайте [XMLHttpRequest](https://xhr.spec.whatwg.org/) и добавьте обработчик событий, чтобы проанализировать ответ JSON, отправленный службой построения маршрутов "Карты". Этот код создает массив координат для сегментов линии маршрута, а затем добавляет набор координат на уровень linestrings карты. 

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

3. Добавьте следующий код, чтобы создать запрос и отправить XMLHttpRequest в службу построения маршрутов "Карты".

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. Сохраните файл **MapRoute.html** и обновите страницу в браузере. Для успешного подключения к интерфейсам API службы "Карты Azure" должна использоваться карта следующего вида. 

    ![Azure Map Control и служба построения маршрутов](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание веб-страницы с помощью API элементов управления картой;
> * Настройка координат адреса.
> * Отправка запроса к службе построения маршрутов для поиска маршрута к точке интереса.

В следующем руководстве показано, как создать запрос на поиск маршрута с такими ограничениями, как вид транспорта или тип груза, а затем отобразить несколько маршрутов на одной карте. 

> [!div class="nextstepaction"]
> [Поиск маршрутов для различных способов перемещения с помощью службы "Карты Azure"](./tutorial-prioritized-routes.md)
