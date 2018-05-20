---
title: Примеры политик службы управления API Azure | Документация Майкрософт
description: Сведения о политиках, доступных для использования в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 3f1d9a96888732e6221722102a687e09f37333c0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="api-management-policy-samples"></a>Примеры политик службы управления API Azure

[Политики](api-management-howto-policies.md) представляют собой одну из эффективных функций системы, позволяющих издателю изменять поведение API-интерфейса путем его настройки. Политика — это коллекция правил, которые выполняются последовательно над запросом или ответом API. В следующей таблице содержатся ссылки на примеры и дается краткое описание каждого примера.

|||
|---|---|
|**Входящие политики**||
|[Добавление заголовка перенаправления, позволяющее серверному API создавать правильные URL-адреса](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Добавление заголовка перенаправления во входящий запрос, что позволяет серверному API создавать правильные URL-адреса.|
|[Добавление заголовка, который содержит идентификатор корреляции](./policies/add-correlation-id.md?toc=api-management/toc.json) |Добавление заголовка, который содержит идентификатор корреляции для входящего запроса.|
|[Добавление функций в серверную службу и кэширование ответа](./policies/cache-response.md?toc=api-management/toc.json) |Добавление функций в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды.|
|[Авторизация доступа на основе утверждений JWT](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Авторизация доступа к определенным методам HTTP в API на основе утверждений JWT.|
|[Авторизация доступа с помощью токена OAuth Google](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Авторизация доступа к конечным точкам с использованием Google в качестве поставщика токена OAuth.|
|[Создание подписанного URL-адреса и запроса на перенаправление к службе хранилища Azure](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Создание [подписанного URL-адреса](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) с помощью выражений и перенаправление запроса в службу хранилища Azure с использованием политики rewrite-uri. |
|[Получение маркера доступа OAuth2 из AAD и его перенаправление в серверную часть](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Пример использования OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть.|
|[Получение маркера X-CSRF из шлюза SAP с помощью политики отправки запроса](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Реализация шаблона X-CSRF, используемого во многих API-интерфейсах. Этот пример относится только к шлюзу SAP. |
|[Маршрутизация запроса в зависимости от размера его текста](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Маршрутизация запроса в зависимости от размера его текста.|
|[Отправка контекстной информации запроса во внутреннюю службу](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Отправка контекстной информации запроса во внутреннюю службу для ведения журнала или обработки.|
|[Установка длительности для кэширования ответа](./policies/set-cache-duration.md?toc=api-management/toc.json) |Установка длительности кэширования ответа при помощи значения maxAge в заголовке Cache-Control, который отправляется с сервера.|
|**Исходящие политики**||
|[Фильтрация содержимого ответа](./policies/filter-response-content.md?toc=api-management/toc.json) | Фильтрация элементов данных из полезных данных ответа по продукту, связанному с запросом.|
|**Политики обработки ошибок**||
|[Запись ошибок в журнал для Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Добавление политики регистрации ошибок для их отправки в Stackify и записи в журнал.|
