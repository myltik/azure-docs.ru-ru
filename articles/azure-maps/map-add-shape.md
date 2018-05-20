---
title: Добавление фигуры с помощью службы "Карты Azure" | Документация Майкрософт
description: Как добавить фигуру на карту в JavaScript
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
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>Добавление фигуры на карту

В этой статье показано, как добавить линию, круг и многоугольник на карту. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Добавление линии

<iframe height='500' scrolling='no' title='Добавление линии на карту' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Добавление линии на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается линия. Линия — это компонент [Feature](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) класса LineString со свойством компонента LineStringProperties. Чтобы создать линию и определить ее свойства, используйте `new atlas.data.Feature(new atlas.data.LineString())`. 

Слой линий представляет собой массив линий. Последний блок кода использует функцию [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) класса карты для добавления слоя линий на карту и определения его свойств. Ознакомьтесь со свойствами слоя линий в разделе о классе [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Добавление круга

<iframe height='500' scrolling='no' title='Добавление круга на карту' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Добавление круга на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается круг. Круг — это компонент [Feature](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) класса [Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) со свойством компонента [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest). Чтобы создать круг и определить его свойства, используйте `new atlas.data.Feature(new atlas.data.Point())`.

Слой кругов является массивом кругов. Последний блок кода использует функцию [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) класса карты для добавления слоя кругов на карту и определения его свойств. Просмотрите свойства слоя кругов в разделе о классе [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Добавление многоугольника
<iframe height='500' scrolling='no' title='Добавление многоугольника на карту ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Добавление многоугольника на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается многоугольник. Многоугольник — это компонент [Feature](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) класса [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) со свойством компонента [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest). Чтобы создать многоугольник и определить его свойства, используйте `new atlas.data.Feature(new atlas.data.Polygon())`. Укажите упорядоченные координаты пути многоугольника в конструкторе многоугольников.

Слой многоугольников является массивом многоугольников. Последний блок кода использует функцию [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) класса карты для добавления слоя многоугольников на карту и определения его свойств. Просмотрите свойства слоя многоугольников в разделе о классе [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 
