---
title: "Переход на прокси приложений Azure AD с Microsoft Forefront | Документация Майкрософт"
description: "В этой статье представлены основные сведения о том, как перейти от решений Microsoft Forefront TMG и UAG на прокси приложений Azure Active Directory."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: kgremban
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 6e95e9abac988ae54a401927a92bdb397dd63eed
ms.lasthandoff: 04/21/2017

---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Переход на прокси приложений Azure AD с Microsoft Forefront

В этой статье описано, как выполнять переход от решений Microsoft Forefront Threat Management Gateway (TMG) и Unified Access Gateway (UAG) на прокси приложения Azure AD.

Подробные сведения о переходе с Forefront TMG и UAG на прокси приложения можно получить в [техническом документе Майкрософт](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Сведения о возможностях для преобразования

|**Возможности TMG/UAG**|**Прокси-служба веб-приложения, прокси приложения Azure AD**|
|:-----|:-----|
|Выборочная публикация HTTP для приложений браузера|Доступно в прокси-службе веб-приложения в Windows Server 2012 R2. Доступно в прокси приложения Azure AD уже сейчас.|
|Интеграция со службами федерации Active Directory (AD FS)|Доступно в прокси-службе веб-приложения в Windows Server 2012 R2. Доступно в прокси приложения Azure AD уже сейчас.|
|Расширенные протоколы публикации (например, Citrix, Lync, RDG)|Доступно в прокси-службе веб-приложения в Windows Server 2012 R2. Доступно в прокси приложения Azure AD уже сейчас.|
|Предварительная аутентификация ActiveSync (HTTP Basic) и RDG|В настоящее время недоступно в прокси-службе веб-приложения или прокси приложения Azure AD.|
|Microsoft Azure|Используйте Intune или System Center для работы с прокси-службой веб-приложения. Используйте панель доступа Azure AD или средство запуска приложений Office 365 для работы с прокси приложения Azure AD.|
|Определение состояния работоспособности конечной точки|Используйте Intune или System Center.|
|Туннелирование SSH|Используйте Windows SSL или возможности VPN.|
|Брандмауэр уровня 2/3|Используйте возможности Windows Server.|
|Брандмауэр веб-приложения|Текущее решение от Майкрософт отсутствует.|
|Secure Web Gateway (прокси-сервер переадресации)|Текущее решение от Майкрософт отсутствует.|


## <a name="next-steps"></a>Дальнейшие действия

[Блог о прокси-службе веб-приложения](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Блог. Характеристики прокси приложения Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

