---
title: Отображение маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: В этой статье объясняется, как отобразить маршрут между двумя расположениями на карте в JavaScript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: 9007afd1bc4d2361addc2a554fab1330174e88e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="show-directions-from-a-to-b"></a>Отображение направлений от точки А до точки Б 

В этой статье объясняется, как выполнить запрос маршрута и отобразить маршрут на карте. 

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Отображение направлений от точки А до точки Б на карте' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Отображение направлений от точки А до точки Б на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создаются и добавляются на карту маркеры, представляющие начальную и конечную точку маршрута. См. инструкции по [добавлению маркеров на карту](map-add-pin.md).

В третьем блоке кода используется функция [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) класса Map, чтобы задать координаты ограничивающего прямоугольника карты на основе начальной и конечной точек маршрута.

В четвертом блоке кода [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API маршрутов службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

В последнем блоке кода анализируется входящий ответ. При получении ответа собираются сведения о широте и долготе для каждой точки маршрута. Затем путем соединения каждой точки маршрута со следующей точкой создается массив линий. Все эти линии добавляются на карту для отображения маршрута. См. инструкции по [добавлению линий на карту](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 

* класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest);
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds);
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings);
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins).
