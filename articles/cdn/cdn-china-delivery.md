---
title: Доставка содержимого для пользователей в Китае с помощью Azure CDN | Документация Майкрософт
description: Сведения о доставке содержимого пользователям в Китае с помощью сети доставки содержимого Azure (CDN).
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 59788f301bb501103babd55a2ac37102932f4dcf
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259799"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Доставка содержимого для пользователей в Китае с помощью Azure CDN

Глобальная сеть доставки содержимого Azure (CDN) может предоставлять содержимое пользователям из Китая с помощью точек подключения (POP), расположенных вблизи Китая, или любой POP, которая обеспечивает наилучшую производительность для запросов, поступающих из Китая. Однако, если Китай является значительным рынком для ваших клиентов и им нужна быстрая производительность, вместо этого используйте сеть Azure CDN для Китая.

Сеть Azure CDN для Китая отличается от глобальной сети Azure CDN тем, что она доставляет содержимое из точек POP внутри Китая, сотрудничая с локальными поставщиками. По причине требования соответствия и нормативов в Китае вы должны зарегистрировать отдельную подписку для использования сети Azure CDN для Китая, а ваши веб-сайты должны иметь лицензию ICP. Выполнение доставки содержимого, а также управление ею с помощью портала и API идентичны доставке и управлению с помощью глобальной сети Azure CDN и сети Azure CDN для Китая.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Сравнение глобальной сети Azure CDN и сети Azure CDN для Китая

Для глобальной сети Azure CDN и сети Azure CDN для Китая доступны следующие возможности:

- Глобальная сеть Azure CDN:

     - Портал: https://portal.azure.com  

     - Выполняет доставку содержимого за пределы Китая

     - Четыре ценовые категории: "Стандартный" от Майкрософт, "Стандартный" от Verizon, "Премиум" от Verizon и "Стандартный" от Akamai

     - [Документация](https://docs.microsoft.com/en-us/azure/cdn/)

- Сеть Azure CDN для Китая

     - Портал: https://portal.azure.cn

     - Выполняет доставку содержимого внутри Китая

     - Две ценовые категории: "Стандартный" и "Премиум"

     - [Документация](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Дополнительная информация

Подробнее о сети доставки содержимого Azure для Китая см. по следующим ссылкам:

- [Сеть доставки содержимого (CDN)](https://www.azure.cn/en-us/home/features/cdn/)

- [Общие сведения о сети доставки содержимого Azure](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Использование сети доставки содержимого Azure](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Сведения о доступности служб Azure в Китае](https://docs.microsoft.com/en-us/azure/china/china-get-started-service-availability)



