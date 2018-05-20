---
title: Добавление всплывающего окна с помощью службы "Карты Azure" | Документация Майкрософт
description: Как добавить всплывающее окно на карту в JavaScript
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
ms.openlocfilehash: 7425081597bfa9379594597277555ee30809c4e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-popup-to-the-map"></a>Добавление всплывающего окна на карту

В этой статье объясняется, как добавить всплывающее окно на карту.  

## <a name="understand-the-code"></a>Изучение кода

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Добавление всплывающего окна на карту' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>Добавление всплывающего окна на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается закрепление, добавляемое на карту. См. инструкции по [добавлению закреплений на карту](./map-add-pin.md).

В третьем блоке кода создается содержимое, которое будет отображаться во всплывающем окне. Содержимое всплывающего окна представляет собой HTML-элемент. 

В четвертом блоке кода создается [объект всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) с помощью `new atlas.Popup()`. Такие свойства всплывающего окна, как содержимое и положение, входят в класс [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest). PopupOptions можно определить в конструкторе выплывающего окна или с помощью функции [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) класса Popup.

В последнем блоке кода используется функция [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) класса Map для ожидания передачи данных о событии наведения указателя мыши на закрепление. В нем также используется функция [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) класса Popup, чтобы открыть всплывающее окно, когда возникает это событие.


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
