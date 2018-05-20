---
title: Создание карты с помощью службы "Карты Azure" | Документация Майкрософт
description: Создание карты на JavaScript
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
ms.openlocfilehash: 9a7c611860a6d32f82d6714d945c62e6c562f91f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-map"></a>Создание карты

В этой статье показано, как создать карту.  

## <a name="understand-the-code"></a>Изучение кода

Карту можно создать двумя способами. Вы можете навести камеру на карту, указав центральную точку и масштаб изображения, или определить границы камеры на карте, указав ее крайние точки на юго-западе и на северо-востоке.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Настройка камеры

<iframe height='310' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Создание карты с помощью CameraOptions</a> от Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) с помощью `new atlas.Map()`. Свойства карты, такие как положение центральной точки и уровень масштабирования, входят в [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). CameraOptions можно определить через конструктор карты или с помощью функции [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) класса Map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Настройка границ камеры

<iframe height='310' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Создание карты через CameraBoundsOptions</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) с помощью конструктора `new atlas.Map()`. Свойства карты, например расположение ограничивающего прямоугольника, входят в [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions можно определить с помощью функции [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) класса Map.

## <a name="try-out-the-code"></a>Тестирование кода 

Давайте рассмотрим подробнее приведенный выше пример. Вы можете редактировать код JavaScript на вкладке JS в левой части экрана и сразу видеть изменения в представлении карты на вкладке "Результат" справа. Кроме того, вы можете изменить код в CodePen с помощью кнопки "Редактировать в CodePen". 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)

