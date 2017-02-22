---
title: "Переход к прокси приложения Azure AD с Microsoft Forefront | Документация Майкрософт"
description: "Основные сведения о соединителях прокси приложения Azure AD."
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
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Переход к прокси приложения Azure AD с Microsoft Forefront

В этой статье описано, как выполнять переход от решений Microsoft Forefront Threat Management Gateway (TMG) и United Access Gateway (UAG) к следующим прокси приложений Azure AD: прокси-службы веб-приложений (WAP) и прокси приложений Azure AD (AADAP). 

> [!NOTE]
> Прокси приложения — это функция, которая доступна только после обновления до выпуска Premium или Basic службы Azure Active Directory. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).
> 
 
Многие клиенты спрашивают нас о переходе от Forefront UAG и TMG к новым прокси приложений. Дополнительные сведения с подробным описанием перехода см. [здесь](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx). 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>Преобразование TMG/UAG в таблицу прокси приложения Azure AD
 
|**Возможности TMG/UAG**|**Прокси-служба веб-приложения (WAP), прокси приложения Azure AD (AADAP)**|
|:-----|:-----|
|Выборочная публикация HTTP для приложений браузера|Доступно для WAP в Windows Server 2012 R2 (сейчас доступно и для AADAP).|
|Интеграция с ADFS|Доступно для WAP в Windows Server 2012 R2 (сейчас доступно и для AADAP).|
|Расширенные протоколы публикации (например, Citrix, Lync, RDG)|Доступно для WAP в Windows Server 2012 R2 (сейчас доступно и для AADAP).|
|Предварительная аутентификация ActiveSync (HTTP Basic) и RDG|Будет доступно для WAP в Windows Server vNext (ожидается и для AADAP)|
|Microsoft Azure|Использование Intune или System Center для WAP (использование панели доступа AAD или средства запуска приложений Office 365 доступно для AADAP)|
|Определение состояния работоспособности конечной точки|Использование Intune или System Center|
|Туннелирование SSH|Использование Windows SSL или возможностей VPN|
|Брандмауэр уровня 2/3|Использование возможностей Windows Server|
|Брандмауэр веб-приложения|Текущее решение от Майкрософт отсутствует.|
|Secure Web Gateway (прокси-сервер переадресации)|Текущее решение от Майкрософт отсутствует.|


## <a name="next-steps"></a>Дальнейшие действия

[Блог о прокси-службе веб-приложения](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Блог о прокси приложения](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->


