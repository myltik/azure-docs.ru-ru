---
title: Использование b2clogin.com | Документация Майкрософт
description: Сведения об использовании b2clogin.com вместо login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660428"
---
#<a name="using-b2clogincom"></a>С помощью b2clogin.com

>[!IMPORTANT]
>Эта функция является общедоступной 
>

Теперь у вас есть возможность использовать службу Azure AD B2C `<YourTenantName>.b2clogin.com` вместо `login.microsoftonline.com`.  Вы получаете множество преимуществ:
* Вы больше не будете разделять такое же ограничение размера заголовка файла cookie, что и у других продуктов Майкрософт.
* Вы можете удалить все ссылки на корпорацию Майкрософт в своем URL-адресе (вы можете заменить `<YourTenantName>.onmicrosoft.com` своим идентификатором клиента).

 Чтобы воспользоваться преимуществами b2clogin.com, необходимо настроить следующее:

1. Убедитесь, что для **конечной точки немедленного выполнения** используется `<YourTenantName>.b2clogin.com` вместо `login.microsoftonline.com`.
2. Если вы используете MSAL, вам нужно установить `validateauthority=false`.  Это происходит потому, что поставщик маркера — `<YourTenantName>.b2clogin.com`.