---
title: "Ограничения IP-адресов в службе приложений Azure | Документация Майкрософт"
description: "Как использовать ограничения IP-адресов при помощи службы приложений Azure"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/12/2017
ms.author: byvinyal
ms.openlocfilehash: 54c0c5050c812c2a59631541d94c553974acd2f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-static-ip-restrictions"></a>Ограничения статических IP-адресов в службе приложений Azure #

Ограничения IP-адресов позволяют определить список IP-адресов, с которых разрешен доступ к вашему приложению. Список разрешений может содержать отдельные IP-адреса или их диапазон, который определяется маской подсети.

Когда клиент отправляет запрос к приложению, его IP-адрес сравнивается с адресами из списка разрешений. Если IP-адрес отсутствует в списке, приложение возвращает код состояния [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Ограничения IP-адресов определяются для экземпляров плана службы приложений, присвоенных вашему приложению.

Чтобы добавить правило ограничения IP-адреса приложения, в меню откройте **Сеть**>**Ограничения IP-адресов** и выберите команду **Настроить ограничения IP-адресов**

![Ограничения IP-адресов] (media/app-service-ip-restrictions/ip-restrictions.png)

Здесь можно просмотреть список правил для ограничения IP-адресов, которые определены для приложения.

![список ограничений IP-адресов](media/app-service-ip-restrictions/browse-ip-restrictions.png)

Можно щелкнуть **[+] Добавить**, чтобы добавить новое правило ограничения IP-адресов.

![добавление ограничений IP-адресов](media/app-service-ip-restrictions/add-ip-restrictions.png)
