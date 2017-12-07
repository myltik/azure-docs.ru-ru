---
title: "Использование нескольких маршрутов с помощью Azure Location Based Services | Документация Майкрософт"
description: "Поиск маршрутов для различных способов перемещения с помощью Azure Location Based Services."
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
ms.openlocfilehash: 3631bab8e5cb505689e92d2862c6863bcd56404d
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Поиск маршрутов для различных способов перемещения с помощью Azure Location Based Services

В этом руководстве показано, как использовать учетную запись Azure Location Based Services и пакет SDK службы построения маршрутов, чтобы найти маршрут к объекту с учетом приоритета способа перемещения. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка запроса службы построения маршрутов.
> * Построение маршрутов учетом приоритета способа перемещения.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, убедитесь, что [учетная запись Azure Location Based Services создана](./tutorial-search-location.md#createaccount) и у вас есть [ключ подписки для учетной записи](./tutorial-search-location.md#getkey). Вы можете также узнать, как использовать интерфейсы API Map Control и службы поиска, как описано в руководстве [Поиск ближайшего объекта с помощью Azure Location Based Services](./tutorial-search-location.md), а также изучить основы использования интерфейсов API службы построения маршрутов, которые приведены в руководстве [Поиск маршрута к объекту с помощью Azure Location Based Services](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Настройка запроса службы построения маршрутов.

Чтобы создать статическую HTML-страницу со встроенным API Map Control API Location Based Services, сделайте следующее. 

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
    Обратите внимание на то, что заголовок HTML внедряет расположения ресурсов файлов CSS и JavaScript из библиотеки Azure Location Based Services. Кроме того, имейте ввиду, что в блок body HTML добавляется сегмент *script*, предназначенный для внутреннего кода JavaScript, используемого для доступа к API Azure Map Control.
3. Добавьте следующий код JavaScript в блок *script* HTML-файла. Замените заполнитель *<insert-key>* первичным ключом учетной записи Location Based Services.

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты и является компонентом API Azure Map Control.

4. Добавьте следующий код JavaScript в блок *script*, чтобы добавить отображение потока трафика на карту.

    ```HTML
            // Add Traffic Flow to the Map
            map.setTraffic({
                flow: "relative"
            });
    ```
    Этот код задает для потока трафика значение `relative`. Это скорость на дороге относительно безостановочного потока. Можно также задать скорость `absolute` или `relative-delay`, тогда будут отображаться отличия относительной скорости от безостановочного потока. 

5. Добавьте следующий код JavaScript для создания меток начальной и конечной точек маршрута.

    ```HTML
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

6. Добавьте следующий код JavaScript, чтобы добавить объекты *LineString* в Map Control для отображения маршрутов в зависимости от способа перемещения, например _car_ (Легковой автомобиль) и _truck_ (Грузовик).

    ```HTML
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

7. Добавьте следующий код JavaScript для добавления на карту начальной и конечной точек.

    ```HTML
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
    **map.setCameraBounds** API корректирует окно карты в соответствии с координатами начальной и конечной точек. **map.addPins** API добавляет точки в Map Control в виде визуальных компонентов.

8. Сохраните файл **MapTruckRoute.html** на компьютере. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Построение маршрутов учетом приоритета способа перемещения

В этом разделе показано, как использовать API службы построения маршрутов Azure Location Based Services для поиска нескольких маршрутов из заданной начальной точки к точке назначения в соответствии со способом перемещения. Служба построения маршрутов предоставляет интерфейсы API для планирования самого быстрого, краткого или экономичного маршрута между двумя расположениями с учетом трафика в режиме реального времени. Она также позволяет пользователям планировать маршруты в будущем с помощью обширной базы данных Azure, содержащей исторический трафик, и прогнозирования длительности маршрутов в любой день и любое время. 

1. Откройте файл **MapTruckRoute.html**, созданный в предыдущем разделе, и добавьте следующий код JavaScript в блок *script* для получения маршрута для грузовика с помощью службы построения маршрутов.

    ```HTML
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
            truckRouteUrl += "&subscription-key=" + subscriptionKey;
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
    
    Этот фрагмент кода также отправляет запрос к службе построения маршрутов, чтобы получить маршрут для указанной начальной и конечной точек для ключа подписки вашей учетной записи. Следующие необязательные параметры используются для указания маршрута для тяжелого грузовика. Параметр `travelMode=truck` указывает способ перемещения *truck*. Поддерживаются также способы перемещения *taxi* (Такси), *bus* (Автобус), *van* (Фургон), *motorcycle* (Мотоцикл) и используемый по умолчанию *car* (Легковой автомобиль). 
        Параметры `vehicleWidth`, `vehicleHeight`, и `vehicleLength` задают размеры транспортного средства в метрах и учитываются, только если выбран способ перемещения *truck* (Грузовик). 
        Параметр `vehicleLoadType` позволяет классифицировать груз как опасный и допустимый только на некоторых дорогах. В настоящее время он тоже учитывается только для способа перемещения *truck* (Грузовик). 

2. Добавьте следующий код JavaScript для получения маршрута для легкового автомобиля с помощью службы построения маршрутов.

    ```HTML
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
            carRouteUrl += "&subscription-key=" + subscriptionKey;
            carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

            xhttpCar.open("GET", carRouteUrl, true);
            xhttpCar.send();
    ```
    Этот фрагмент кода создает еще один [XMLHttpRequest](https://xhr.spec.whatwg.org/) и добавляет обработчик событий для анализа входящего ответа. Получив успешный ответ, он создает массив координат для полученного маршрута и добавляет его на слой карты `carRouteLayerName`. 
    
    Этот фрагмент кода также отправляет запрос к службе построения маршрутов, чтобы получить маршрут для указанной начальной и конечной точек для ключа подписки вашей учетной записи. Так как другие параметры не используются, возвращается маршрут для способа перемещения по умолчанию *car* (Легковой автомобиль). 

3. Сохраните файл **MapTruckRoute.html** на локальном компьютере, а затем откройте его в веб-браузере по своему усмотрению и просмотрите результаты. Для успешного подключения к интерфейсам API Location Based Services должна использоваться карта следующего вида. 

    ![Поиск маршрутов с учетом приоритета с помощью службы построения маршрутов Azure](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Обратите внимание на то, что маршрут для грузовика отображается синим цветом, а для легкового автомобиля — фиолетовым.

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка запроса службы построения маршрутов.
> * Построение маршрутов учетом приоритета способа перемещения

Перейдите к статьям, содержащим **понятия** и **практические руководства**, чтобы подробнее изучить пакет SDK для Azure Location Based Services. 
