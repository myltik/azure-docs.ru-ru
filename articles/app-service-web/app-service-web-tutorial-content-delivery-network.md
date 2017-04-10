---
title: "Подключение веб-приложения к сети доставки содержимого | Документация Майкрософт"
description: "Подключите веб-приложение к сети доставки содержимого для доставки статических файлов из граничных узлов."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d616ca49da7a909a1a104409c5845f39c4a5377a
ms.lasthandoff: 04/04/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Подключение веб-приложения к сети доставки содержимого

В этом руководстве вы создадите профиль и конечную точку CDN Azure для обслуживания статических файлов из веб-приложения через расположения POP Azure CDN.

> [!TIP]
> Просмотрите обновленный список [расположений POP Azure CDN](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Шаг 1. Вход на портал Azure

Сначала откройте свой браузер и перейдите на [портал](https://portal.azure.com) Azure.

## <a name="step-2---create-a-cdn-profile"></a>Шаг 2. Создание профиля CDN

В области навигации слева нажмите кнопку `+ New` и щелкните **Интернет и мобильные устройства**. В категории "Интернет и мобильные устройства" выберите **CDN**.

Укажите **имя**, **расположение**, **группу ресурсов**, **ценовую категорию**, после чего нажмите кнопку **Создать**.

В области навигации слева откройте центр групп ресурсов и выберите **myResourceGroup**. В списке ресурсов выберите **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Шаг 3. Создание конечной точки CDN

В командах возле поля поиска щелкните `+ Endpoint`. После этого запустится колонка создания конечной точки.

Укажите **имя**, **тип источника**, **узел источника** и нажмите кнопку **Добавить**.

После создания конечной точки сети доставки содержимого состояние изменится на **Выполняется**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Шаг 4. Использование CDN

Теперь, когда конечная точка работает, давайте убедимся, что содержимое доступно на сервере POP. Для этого найдите статический файл на веб-сервере, а затем измените имя узла с `http://<app_name>.azurewebsites.net/path/to-static-file` на `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Дальнейшие действия


