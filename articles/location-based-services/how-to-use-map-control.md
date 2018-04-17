---
title: Использование Azure Location Based Services Map Control | Документация Майкрософт
description: Узнайте, как использовать клиентскую библиотеку Javascript Azure Location Based Services Map Control.
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: ee767c9461f79437ab49d2a919bb82e7de8feba7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Использование Azure Location Based Services Map Control
Клиентская библиотека Javascript службы Map Control позволяет отображать карты и встроенные функциональные возможности Azure Location Based Services в вашем мобильном или веб-приложении. 

## <a name="prerequisites"></a>предварительным требованиям
Ключ и учетная запись Azure Location Based Services. Дополнительные сведения о создании учетной записи и получении ключа см. в статье об [управлении ключами и учетной записью Azure Location Based Services](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Создание карты на веб-странице с помощью API Map Control
Вы можете встроить карту в веб-страницу с помощью клиентской библиотеки Javascript службы Map Control.

1. Создайте файл с именем MapSearch.html.

2. Добавьте ссылки на стили и источник сценария Azure Location Based Services в элемент `<head>` файла:

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
    
4. Для инициализации элемента управления карты определите новый раздел в тексте html и создайте сценарий. Используйте в скрипте собственный ключ учетной записи Azure Location Based Services. 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту.