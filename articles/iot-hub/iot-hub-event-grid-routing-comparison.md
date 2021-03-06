---
title: "Сравнение маршрутизации с помощью Центра Интернета вещей до и после интеграции со службой \"Сетка событий\" | Документация Майкрософт"
description: "Центр Интернета вещей предусматривает собственную службу маршрутизации сообщений, но его можно интегрировать со службой \"Сетка событий\", чтобы публиковать события. Сравните эти две службы."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 5a0a97ccf033b2981ba13be455482146ba212228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Сравнение маршрутизации сообщений со службой "Сетка событий" и без нее для Центра Интернета вещей

Центр Интернета вещей Azure позволяет выполнять потоковую передачу данных с подключенных устройств и интегрировать эти данные в бизнес-приложения. Центр Интернета вещей поддерживает два метода интеграции событий Интернета вещей в другие службы Azure или бизнес-приложения. В этой статье описаны такие возможности, что позволяет выбрать, какой вариант больше всего подходит для вашего сценария.

* **Маршрутизация сообщений с помощью Центра Интернета вещей**. Позволяет [маршрутизировать сообщения с устройства в облако](iot-hub-devguide-messages-read-custom.md) к конечным точкам служб, таким как контейнеры службы хранилища Azure, концентраторы событий, очереди и разделы служебной шины. Благодаря правилам маршрутизацию можно осуществлять на основе запросов. Это также позволяет получать [критические оповещения](iot-hub-devguide-messages-d2c.md), активирующие действия с помощью запросов. За основу оповещений можно брать заголовки и текст сообщений. 
* **Интеграция Центра Интернета вещей со службой "Сетка событий".** "Сетка событий Azure" — это полностью управляемая служба маршрутизации событий, использующая принцип "публикации — подписки". Две службы работают вместе, чтобы [интегрировать события Центра Интернета в службы Azure и другие службы](iot-hub-event-grid.md) практически в реальном времени. 

## <a name="similarities-and-differences"></a>Сходства и различия

Маршрутизация сообщений и Сетка событий обеспечивают конфигурацию оповещений, хотя между ними есть кардинальные отличия. Дополнительные сведения см. в таблице ниже.

| Функция | Маршрутизация сообщений с помощью Центра Интернета вещей | Интеграция Центра Интернета вещей со службой "Сетка событий" |
| ------- | --------------- | ---------- |
| **Сообщения с устройств** | Да, маршрутизацию сообщений можно использовать для формирования данных телеметрии. | Нет, службу "Сетка событий" можно использовать только для нетелеметрических событий из Центра Интернета вещей. |
| **Тип события** | Да, в ходе маршрутизации сообщений можно получать сведения об изменениях двойников и событиях жизненного цикла устройств. | Да, "Сетка событий" может передавать отчеты о регистрации устройств в Центре Интернета вещей и об их удалении. |
| **Упорядочение** | Да, упорядочение событий поддерживается.  | Нет, порядок событий не гарантируется. | 
| **Максимальный размер сообщения** | 256 КБ (с устройства в облако). | 64 КБ |
| **Фильтрация** | Расширенную фильтрацию на языке на основе SQL можно применять к заголовкам и тексту сообщений. Примеры см. в статье о [языке запросов Центра Интернета вещей](iot-hub-devguide-query-language.md). | Фильтрация идентификаторов устройств по суффиксу или префиксу, которая отлично подходит для иерархических служб, таких как хранилища. |
| **Конечные точки** | <ul><li>Концентратор событий</li> <li>Большой двоичный объект службы хранилища</li> <li>Очередь служебной шины</li> <li>Разделы служебной шины</li></ul><br>Для платных номеров SKU Центра Интернета вещей (S1, S2 и S3) действует ограничение: до 10 пользовательских конечных точек. Для Центра Интернета вещей можно создать 100 маршрутов. | <ul><li>Функции Azure</li> <li>Служба автоматизации Azure</li> <li>Концентратор событий</li> <li>приложения логики;</li> <li>Microsoft Flow</li> <li>Сторонние службы с использованием веб-перехватчиков</li></ul><br>Самый новый список конечных точек см. в разделе об [обработчиках событий для службы "Сетка событий"](../event-grid/overview.md#event-handlers). |
| **Стоимость** | За маршрутизацию сообщений отдельная плата не взимается. Оплачиваются только данные телеметрии, входящие в Центр Интернета вещей. Например, если сообщение направлено в три различные конечные точки, плата взимается только за одно сообщение. | Центр Интернета вещей не взимает плату. В службе "Сетка событий" предусмотрены первые 100 000 операций в месяц бесплатно, по исчерпании которых действует тариф 0,60 долл. США за миллион операций. |

У службы "Сетка событий" и маршрутизации сообщений с помощью Центра Интернета вещей много сходств, некоторые из которых представлены в таблице ниже:

| Функция | Маршрутизация сообщений с помощью Центра Интернета вещей | Интеграция Центра Интернета вещей со службой "Сетка событий" |
| ------- | --------------- | ---------- |
| **Надежность** | Высокая. Каждое сообщение доставляется в конечную точку по крайней мере единожды для каждого маршрута. У всех сообщений, недоставленных в течение одного часа, истекает срок действия. | Высокая. Каждое сообщение доставляется веб-перехватчику по крайней мере один раз для каждой подписки. У всех событий, недоставленных в течение 24 часов, истекает срок действия. | 
| **Масштабируемость** | Высокая. Оптимизирована для поддержки миллионов одновременно подключенных устройств. | Высокая. Поддерживается маршрутизация 10 000 000 событий в секунду для каждого региона. |
| **Задержка** | Малая. Практически в реальном времени. | Малая. Практически в реальном времени. |
| **Отправка в несколько конечных точек** | Да, одно сообщение можно отправить в несколько конечных точек. | Да, одно сообщение можно отправить в несколько конечных точек.  | 
| **Безопасность** | Центр Интернета вещей обеспечивает идентификацию каждого устройства и управление доступом с возможностью отзыва. Дополнительные сведения см. в статье об [управлении доступом в Центре Интернета вещей](iot-hub-devguide-security.md). | "Сетка событий" обеспечивает проверку трех пунктов: подписки на событие, публикации событий и доставки событий веб-перехватчику. Дополнительные сведения см. в разделе [Сетка событий: безопасность и проверка подлинности](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Как выбрать

Маршрутизация сообщений Центра Интернета вещей и его интегрированная версия со службой "Сетка событий" выполняют разные действия для достижения одинаковых результатов. Они принимают сведения из вашего решения Центра Интернета вещей и передают их, чтобы могли отреагировать другие службы. Как же решить, какой компонент использовать? В дополнение к данным из раздела выше при принятии решения задайтесь такими вопросами: 

* **Какие данные следует отправлять в конечные точки?**

   Маршрутизацию сообщений с помощью Центра Интернета вещей лучше использовать, если нужно отправлять данные телеметрии в другие службы. Маршрутизация сообщений также позволяет запрашивать заголовки и текст сообщений. 

   Центр Интернета вещей, интегрированный со службой "Сетка событий", работает с событиями, происходящими в службе "Центр Интернета вещей". К таким событиям Центра Интернета вещей относятся создание и удаление устройств. 

* **Какие конечные точки должны получать эти сведения?**

   В Центре Интернета вещей ограничены конечные точки, но можно создать соединители, чтобы перенаправлять данные и события в дополнительные точки. Полный список поддерживаемых конечных точек см. в таблице в разделе выше. 

   Центр Интернета вещей, интегрированный со службой "Сетка событий", поддерживает больше конечных точек. Он также работает с веб-перехватчиками, за счет чего маршрутизация выходит за пределы экосистемы служб Azure к сторонним бизнес-приложениям. 

* **Важен ли порядок получения данных?**

   Центр поддерживает порядок отправки сообщений при их маршрутизации.

   Сетка событий не гарантирует, что конечные точки получат события в порядке их возникновения. Однако в схеме события есть метка времени, с помощью которой можно узнать порядок поступления событий в конечную точку. 

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о маршрутизации сообщений и о конечных точках Центра Интернета вещей см. [здесь](iot-hub-devguide-messages-d2c.md) и [здесь](iot-hub-devguide-endpoints.md).

* Дополнительные сведения о службе "Сетка событий" Azure см. [здесь](../event-grid/overview.md).

* Дополнительные сведения о том, как использовать интеграцию со службой "Сетка событий", см. в статье [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка электронных уведомлений о событиях Центра Интернета вещей с помощью Logic Apps).