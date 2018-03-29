---
title: Поддержка HTTP/2 в Azure CDN | Документы Майкрософт
description: Сведения о поддержке HTTP/2 и CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: a58ab1165f3886484fb695170100422c2f1b0486
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="http2-support-in-azure-cdn"></a>Поддержка HTTP/2 в Azure CDN

HTTP/2 является основной редакцией HTTP/1.1\. Этот протокол обеспечивает более высокую производительность, сокращение времени ответа и более удобную среду работы пользователей наряду с привычными методами, кодами состояния и семантикой HTTP. Хотя HTTP/2 предназначен для работы с HTTP и HTTPS, многие клиентские веб-браузеры поддерживают только HTTP/2 через TLS.

### <a name="http2-benefits"></a>Преимущества HTTP/2

Преимущества HTTP/2 перечислены ниже.

*   **Мультиплексирование и параллелизм**

    При использовании HTTP 1.1 для выполнения нескольких запросов ресурсов требуется несколько TCP-подключений, и с каждым подключением связаны определенные затраты производительности. HTTP/2 позволяет запрашивать несколько ресурсов через одно TCP-подключение.

*   **Сжатие заголовка**

    Благодаря сжатию HTTP-заголовков для обслуживаемых ресурсов значительно уменьшается время в сети.

*   **Зависимости потоков**

    Зависимости потоков позволяют клиенту указывать серверу, какие из ресурсов имеют приоритет.


## <a name="http2-browser-support"></a>Поддержка HTTP/2 в браузерах

Во всех текущих версиях основных браузеров реализована поддержка HTTP/2. Неподдерживаемые браузеры будет автоматически использовать HTTP/1.1.

|"Обзор"|Минимальная версия|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Включение HTTP/2 в Azure CDN

Сейчас поддержка HTTP/2 активна в профилях **Azure CDN от Akamai** и **Azure CDN от Verizon**. Никаких действий со стороны пользователей не требуется.

## <a name="next-steps"></a>Дальнейшие действия

Преимущества HTTP/2 в действии представлены в [этой демонстрации от Akamai](https://http2.akamai.com/demo).

Дополнительные сведения о HTTP/2 см. в следующих ресурсах.

*   [Домашняя страница спецификации HTTP/2](https://http2.github.io/)
*   [Официальная страница часто задаваемых вопросов об HTTP/2](https://http2.github.io/faq/)
*   [Сведения об HTTP/2 на сайте Akamai](https://http2.akamai.com/)

Дополнительные сведения о доступных функциях Azure CDN см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).