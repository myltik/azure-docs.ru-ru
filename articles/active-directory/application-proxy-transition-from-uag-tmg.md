---
title: "Установка новой версии прокси приложения Azure AD | Документация Майкрософт"
description: "Выберите, какой прокси-сервер лучше всего подходит для обновления Microsoft Forefront или Unified Access Gateway."
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Обновление Microsoft Forefront или Unified Access Gateway до прокси-серверов Azure AD

В этой статье описано, как выполнять переход от решений Microsoft Forefront Threat Management Gateway (TMG) и Unified Access Gateway (UAG) на прокси приложения Azure AD.

Подробные сведения о переходе с Forefront TMG и UAG на прокси приложения можно получить в [техническом документе Майкрософт](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Сведения о возможностях для преобразования

|**Возможности TMG/UAG**|**Современное решение**|
|:-----|:-----|
|Выборочная публикация HTTP для приложений браузера|Прокси приложения Azure AD.|
|Интеграция со службами федерации Active Directory (AD FS)|Прокси приложения Azure AD.|
|Расширенные протоколы публикации (например, Citrix, Lync, RDG)|Прокси приложения Azure AD.|
|Microsoft Azure|Панель доступа Azure AD или средство запуска приложений Office 365 для прокси приложения Azure AD.|
|Определение состояния работоспособности конечной точки|Intune или System Center.|
|Туннелирование SSH|SSL или VPN в Windows.|
|Брандмауэр уровня 2/3|Windows Server|
|Предварительная аутентификация ActiveSync (HTTP Basic) и RDG|Текущее решение от Майкрософт отсутствует.|
|Брандмауэр веб-приложения|Текущее решение от Майкрософт отсутствует.|
|Secure Web Gateway (прокси-сервер переадресации)|Текущее решение от Майкрософт отсутствует.|


## <a name="next-steps"></a>Дальнейшие действия

[Блог о прокси-службе веб-приложения](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Блог. Характеристики прокси приложения Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

