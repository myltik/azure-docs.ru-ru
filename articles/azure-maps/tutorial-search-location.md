---
title: Поиск с помощью службы "Карты Azure" | Документация Майкрософт
description: Поиск ближайшей точки интереса с помощью службы "Карты Azure"
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4a414b5b865d31dd99b54ef9693abafb5490a50f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601791"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Поиск ближайшей точки интереса с помощью службы "Карты Azure"

В этом руководстве показано, как настроить учетную запись службы "Карты Azure", а затем использовать API службы "Карты Azure" для поиска точки интереса. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * создание учетной записи службы "Карты Azure";
> * получение первичного ключа для учетной записи службы "Карты Azure";
> * создание веб-страницы с помощью API элементов управления картой;
> * использование службы поиска "Карты Azure" для поиска ближайшей точки интереса.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.
Войдите на [портал Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Создание учетной записи службы "Карты Azure"

Создайте учетную запись службы "Карты Azure" с помощью следующих действий:

1. В верхнем левом углу [портала Azure](https://portal.azure.com) щелкните **Создать ресурс**.
2. В поле *Поиск по Marketplace* введите **Карты**.
3. Выберите *Результаты*, а затем — **Карты**. Нажмите кнопку **Создать**, расположенную под картой. 
4. На странице **Создание учетной записи Azure Maps** введите следующие значения:
    - *Имя* новой учетной записи. 
    - *Подписку*, которую необходимо использовать для этой учетной записи.
    - Имя *группы ресурсов* для этой учетной записи. Вы *создать новую* или *использовать существующую* группу ресурсов.
    - Выберите *расположение группы ресурсов*.
    - Ознакомьтесь с *лицензией* и *заявлением о конфиденциальности*, а затем установите флажок, чтобы принять условия соглашения. 
    - Нажмите кнопку **Создать** .
   
    ![Создание учетной записи службы "Карты Azure" на портале](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Получение первичного ключа для учетной записи

После успешного создания учетной записи службы "Карты Azure" извлеките ключ, позволяющий запрашивать API службы "Карты Azure".

1. Откройте учетную запись службы "Карты Azure" на портале.
2. В разделе параметров выберите **Ключи**.
3. Скопируйте **первичный ключ** в буфер обмена. Сохраните его локально для использования в этом руководстве позже. 

    ![Получение первичного ключа на портале](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>Создание карты 
API элементов управления картой — это удобная клиентская библиотека, которая позволяет легко интегрировать службу "Карты Azure" в веб-приложение. Она скрывает сложность "чистых" вызовов службы REST и ускоряет работу с помощью компонентов, поддерживающих изменение стилей и настройку. Чтобы создать статическую HTML-страницу со встроенным API элементов управления картой, сделайте следующее. 

1. На локальном компьютере создайте файл **MapSearch.html**. 
2. Добавьте в него следующие компоненты HTML.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    Обратите внимание на то, что заголовок HTML содержит файлы ресурсов CSS и JavaScript, размещенные в библиотеке Azure Map Control. Обратите внимание на сегмент *script*, добавленный в блок *body* HTML-файла. Этот сегмент будет содержать внутренний код JavaScript для доступа к интерфейсам службы "Карты Azure".
 
3. Добавьте следующий код JavaScript в блок *script* HTML-файла. Замените строку **\<your account key\>** первичным ключом, скопированным из учетной записи службы "Карты Azure".

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Этот сегмент инициирует API элементов управления картой для ключа учетной записи службы "Карты Azure". **Atlas** — это пространство имен, которое содержит API и связанные визуальные компоненты. **atlas.Map** предоставляет элемент управления для визуальной интерактивной веб-карты. 
    
4. Сохраните изменения в файл и откройте HTML-страницу в браузере. Это самая простая карта, которую можно создать, вызвав **atlas.map** и указав ключ учетной записи. 

   ![Просмотр карты](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Добавление возможностей поиска

В этом разделе показано, как использовать API службы "Карты Azure" для поиска точки интереса на карте. Это RESTful API, предназначенный для разработчиков. Он позволяет находить адреса, объекты и другие географические сведения. Служба поиска назначает широту и долготу для определенного адреса. 

1. Добавьте новый слой на карту для отображения результатов поиска. Добавьте следующий код Javascript в блок *script* после кода, который инициализирует карту. 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Создайте [XMLHttpRequest](https://xhr.spec.whatwg.org/) и добавьте обработчик событий, чтобы проанализировать ответ JSON, отправленный службой поиска "Карты Azure". Этот фрагмент кода создает обработчик событий для сбора сведений об адресах, названиях, широте и долготе для каждого расположения, возвращенного в переменной `searchPins`. Наконец, он добавляет коллекцию расположений в элемент управления `map` в виде маркеров. 

    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```

3. Добавьте следующий код в блок *script* для создания запроса и отправки XMLHttpRequest в службу поиска "Карты Azure":

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Этот фрагмент кода использует базовый API службы поиска, **Fuzzy Search**. Он обрабатывает нечеткие входные данные, принимая любое сочетание маркеров адреса или точек интереса. Он выполняет поиск ближайшей **бензозаправочной станции** для заданных координат широты и долготы в пределах указанного адреса. Он использует первичный ключ вашей учетной записи, ранее указанный в примере файла, в вызове к службе "Карты". Этот API возвращает результаты в виде пар широты и долготы найденных мест. 
    
4. Сохраните файл **MapSearch.html** и обновите страницу в браузере. Теперь на карте будет крупным планом показан Сиэтл, и синие пометки указывают расположение бензозаправочных станций в области. 

   ![Просмотр карты с результатами поиска](./media/tutorial-search-location/pins-map.png)

5. Чтобы просмотреть необработанные данные, которые карта преобразовывает для просмотра, введите в браузере запрос XMLHTTPRequest, созданный в файле. Замените \<ключ учетной записи\> на ваш первичный ключ. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

На этом этапе страница MapSearch может отображать расположения точек интереса, возвращаемых из запроса нечеткого поиска. Давайте добавим некоторые интерактивные возможности и дополнительные сведения о расположениях. 

## <a name="add-interactive-data"></a>Добавление интерактивных данных

Карта, которую мы создали, учитывает только данные широты и долготы в результатах поиска. Если взглянуть на необработанные данные JSON, которые возвращает служба поиска "Карты Azure", вы увидите, что они содержат дополнительные сведения о каждой заправочной станции, включая название и адрес. Эти данные можно включить в карту с помощью интерактивных всплывающих окон. 

1. Добавьте следующие строки в блок *script*, чтобы создать всплывающие элементы для объектов, возвращенных службой поиска.

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    **atlas.Popup** API предоставляет информационное окно, которое можно привязать к нужной позиции на карте. Этот фрагмент кода задает содержимое и позицию всплывающего элемента, а также добавляет прослушиватель событий в элемент управления `map`, который ожидает наведения указателя _мыши_ на данный элемент. 

4. Сохраните файл и обновите страницу в браузере. Теперь на карте в браузере при наведении указателя мыши на любой показанный маркер поиска отображаются информационные всплывающие элементы. 

    ![Azure Map Control и служба поиска](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание учетной записи службы "Карты Azure"
> * Получение первичного ключа для учетной записи
> * Создание веб-страницы с помощью API Map Control.
> * Использование службы поиска для поиска ближайшего объекта.

В следующем руководстве показано, как отобразить маршрут между двумя расположениями. 

> [!div class="nextstepaction"]
> [Прокладывание маршрута до точки интереса в службе "Карты Azure"](./tutorial-route-location.md)
