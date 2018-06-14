---
title: Настройте личное доменное имя для веб-приложения в службе приложений Azure, использующей диспетчер трафика для балансировки нагрузки.
description: Используйте личное доменное имя для веб-приложения в службе приложений Azure, которая включает в себя диспетчер трафика.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: c78fb7883559e46ebaa1d8dab59a15c55fb76fdf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2018
ms.locfileid: "27713857"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Настройка личного доменного имени для веб-приложения в службе приложений Azure, использующей диспетчер трафика
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

В этой статье содержатся общие инструкции по использованию личного доменного имени с приложением [службы приложений](app-service-web-overview.md), интегрированным с [диспетчером трафика](../traffic-manager/traffic-manager-overview.md) для балансировки нагрузки.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Общие сведения о записях DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Настройка веб-приложений для режима Standard
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Добавление записи DNS для пользовательского домена
> [!NOTE]
> Если вы приобрели домен через веб-приложения службы приложений Azure, см. только последний шаг статьи [Покупка домена для веб-приложений](custom-dns-web-site-buydomains-web-app.md).
> 
> 

Чтобы связать личный домен с веб-приложением в службе приложений Azure, добавьте новую запись в таблицу DNS для этого домена. Это делается с помощью средств управления поставщика домена.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Несмотря на особенности каждого поставщика домена, вы сопоставляете имя *своего* личного домена (например, **contoso.com**) *с* доменным именем диспетчера трафика (**contoso.trafficmanager.net**), которое интегрируется с вашим веб-приложением.
   
> [!NOTE]
> Если запись уже используется и вам нужно заблаговременно привязать к ней свое приложение, создайте дополнительную запись CNAME. Например, чтобы заблаговременно привязать **www.contoso.com** к веб-приложению, создайте запись CNAME на основе **awverify.www** для **contoso.trafficmanager.net**. Затем можно добавить к веб-приложению фрагмент www.contoso.com, не изменяя запись CNAME www. Дополнительные сведения см. в статье [Создание записей DNS для веб-приложения в пользовательском домене][CREATEDNS].
> 
> 

По завершении добавления или изменения записей DNS сохраните эти изменения в своем поставщике домена.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Включение диспетчера трафика
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию см. в [центре разработчиков Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
