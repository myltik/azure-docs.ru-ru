---
title: Как использовать библиотеку Map Control в службе "Карты Azure" | Документация Майкрософт
description: Узнайте, как использовать клиентскую библиотеку Javascript Map Control в службе "Карты Azure".
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4ba4b9a9f2357d283ddc03a4723cb08b48d40a9b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599217"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Как использовать библиотеку Map Control в службе "Карты Azure"
Клиентская библиотека Javascript Map Control позволяет отображать карты и встроенные функциональные возможности службы "Карты Azure" в вашем мобильном или веб-приложении. 

## <a name="create-a-new-map-in-a-web-page"></a>Создание карты на веб-странице

Вы можете встроить карту в веб-страницу с помощью клиентской библиотеки Javascript службы Map Control.

1. Создайте файл с именем MapSearch.html.

2. Добавьте ссылки на стили и источник сценария службы "Карты Azure" в элемент `<head>` файла:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Чтобы отобразить новую карту в браузере, добавьте ссылку **#map** в элемент `<style>`.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Для инициализации элемента управления карты определите новый раздел в тексте html и создайте сценарий. Используйте в скрипте ключ своей учетной записи службы "Карты Azure". Если вам нужно создать учетную запись или найти ключ, см. статью об [управлении учетной записью и ключами службы "Карты Azure"](how-to-manage-account-keys.md).

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту.

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как создать базовую карту с помощью ключа службы "Карты Azure". Дополнительные примеры кода для добавления в карты см. в следующих статьях: 

* [Create a map](map-create.md) (Создание карты)
* [Добавление маркера](map-add-pin.md)