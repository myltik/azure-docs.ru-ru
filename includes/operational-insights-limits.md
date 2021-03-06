---
title: включение файла
description: включение файла
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
---
Ниже приведены ограничения, которые применяются к ресурсам Log Analytics для каждой подписки.

| Ресурс | Ограничение по умолчанию | Комментарии
| --- | --- | --- |
| Количество бесплатных рабочих областей на подписку | 10 | Это ограничение нельзя увеличить. |
| Количество платных рабочих областей на подписку | Недоступно | Вы ограничены количеством ресурсов в группе ресурсов и количеством групп ресурсов на подписку | 

>[!NOTE]
>Начиная со 2 апреля 2018 года новые рабочие области в новой подписке будут автоматически использовать тарифный план *За гигабайт*.  Для имеющихся подписок, созданных до 2 апреля, или подписки, привязанной к имеющейся регистрации EA, можно выбрать одну из трех ценовых категорий новых рабочих областей. 
>

Для каждой рабочей области Log Analytics действуют следующие ограничения:

|  | Free | Стандартная | Premium | Автономный | OMS | "За гигабайт" |
| --- | --- | --- | --- | --- | --- |--- |
| Объем данных, собранных за день |500 МБ<sup>1</sup> |None |None | None | None | None
| Срок хранения данных |7 дней |1 месяц |12 месяцев | 1 месяц<sup>2</sup> | 1 месяц<sup>2</sup>| 1 месяц<sup>2</sup>|

<sup>1</sup> Если клиент достиг ограничения на объем данных, передаваемых ежедневно (500 МБ), анализ данных прерывается и возобновляется в начале следующего дня. День считается по формату времени UTC.

<sup>2</sup> Срок хранения данных для тарифных планов "Автономный", OMS и "За гигабайт" может быть увеличен до 730 дней.

| Категория | Ограничения | Комментарии
| --- | --- | --- |
| API сборщика данных | Максимальный размер одной записи — 30 МБ.<br>Максимальный размер значения поля — 32 КБ. | Разделяйте большие объемы на несколько записей.<br>Поля, длина которых превышает 32 КБ, обрезаются. |
| API поиска | 5000 записей для неагрегированных данных.<br>500 000 записей для агрегированных данных. | Агрегированные данные — это поисковый запрос, включающий в себя команду `summarize`.
 
