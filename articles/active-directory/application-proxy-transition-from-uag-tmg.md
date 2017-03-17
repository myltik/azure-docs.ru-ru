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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Переход на прокси приложений Azure AD с Microsoft Forefront

В этой статье описано, как выполнять переход от решений Microsoft Forefront Threat Management Gateway (TMG) и Unified Access Gateway (UAG) на следующие прокси приложений Azure Active Directory (Azure AD): прокси-службу веб-приложения и прокси приложений Azure AD.

> [!NOTE]
> Прокси-сервер приложений Azure AD доступен только после обновления до выпуска Premium или Basic службы Azure AD. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).


Подробные сведения о переходе с Forefront TMG и UAG на новые прокси приложений можно получить, [скачав технический документ Майкрософт](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx).

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

