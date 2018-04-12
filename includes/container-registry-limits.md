---
title: включение файла
description: включение файла
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
| Ресурс | базовая; | Стандартная | Премиум |
|---|---|---|---|---|
| Хранилище | 10 ГБ | 100 ГБ| 500 ГБ |
| Операций чтения в минуту<sup>1, 2</sup> | 1000 | 3000 | 10 000 |
| Операций записи в минуту<sup>1, 3</sup> | 100 | 500 | 2000 |
| Пропускная способность скачивания в Мбит/с<sup>1</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>1</sup> | 10 | 20 | 50 |
| Объекты Webhook | 2 | 10 | 100 |
| Георепликация | Недоступно | Недоступно | [Поддерживается *(предварительная версия)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *Операции чтения*, *операции записи* и *пропускная способность* представляют собой минимальные расчеты. ACR стремится к повышению производительности по мере использования.

<sup>2</sup> [При извлечении образа Docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) осуществляется преобразование в несколько операций чтения на основе числа слоев в образе, а также получении манифеста.

<sup>3</sup> [При отправке образа Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) осуществляется преобразование в несколько операций записи, в зависимости от числа слоев, которые необходимо отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.