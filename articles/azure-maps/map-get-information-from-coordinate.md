---
title: Отображение сведений о координатах в службе "Карты Azure" | Документация Майкрософт
description: Как отобразить сведения об адресе на карте, когда пользователь выбирает координаты
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
ms.openlocfilehash: bb8644724cc872a0a8bc331e76251218492fd93d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="get-information-from-a-coordinate"></a>Получение сведений на основе координат

В этой статье объясняется, как выполнять обратный поиск адреса и сделать так, чтобы при щелчке кнопкой мыши отображался адрес расположения во всплывающем окне. 

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Получение сведений на основе координат' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>получения сведений на основе координат</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода стиль курсора мыши меняется на указатель.

В третьем блоке кода создается всплывающее окно. См. инструкции по [добавлению всплывающего окна на карту](./map-add-popup.md).

В последнем блоке кода добавляется прослушиватель событий для щелчка мышью. Когда пользователь щелкнет кнопкой мыши, [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API обратного поиска адресов для службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). При получении ответа фиксируется адрес щелкнутого расположения. Также в коде определяется содержимое и расположение всплывающего окна с помощью функции [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) класса Popup.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 
* [Обратный поиск адреса](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
