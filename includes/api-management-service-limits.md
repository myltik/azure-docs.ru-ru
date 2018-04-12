---
title: включение файла
description: включение файла
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
| Ресурс | Ограничение |
| --- | --- |
| Единицы масштабирования | 10 для каждого региона<sup>1</sup> |
| Кэш | 5 ГБ на единицу<sup>1</sup> |
| Параллельные серверные подключения<sup>2</sup> на центр HTTP | 2048 на единицу<sup>3</sup> |
| Максимальный размер кэшированного ответа | 10 МБ |
| Максимальное количество доменов пользовательского шлюза | 20 на экземпляр службы<sup>4</sup> |


<sup>1</sup>Для каждой ценовой категории применяются собственные ограничения на управление API. Сведения о ценовых категориях и их ограничениях масштабирования см. в статье с [ценами на службу управления API](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> Подключения помещаются в пул и повторно используются, если сервер их явно не закрывает.
<sup>3</sup> На единицу для ценовых категорий "Базовый", "Стандартный" и "Премиум". Уровень "Разработка" ограничен значением 1024.
<sup>4</sup> Доступно только для уровня "Премиум".


