---
title: "Отладка единого входа на основе SAML в приложения в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как выполнять отладку единого входа на основе SAML в приложения в Azure Active Directory. "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 7aa7ca90f9098f30565524470ca23783e97195e0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Отладка единого входа на основе SAML в приложения в Azure Active Directory
При отладке интеграции приложений на основе SAML часто бывает полезно использовать такой инструмент, как [Fiddler](http://www.telerik.com/fiddler) , чтобы просмотреть запрос SAML, ответ SAML и фактический токен SAML, выданный приложению. Проверив токен SAML, можно убедиться, что все необходимые атрибуты, имя пользователя в теме SAML и универсальный код ресурса (URI) издателя поступают должным образом.

![][1]

Ответ от Azure AD, который содержит токен SAML, обычно создается после перенаправления HTTP 302 с https://login.windows.net и отправляется на настроенный **URL-адрес ответа** приложения. 

Токен SAML можно просмотреть, выбрав эту строку и затем выбрав вкладку **Inspectors > WebForms** (Инспекторы > Веб-формы) на правой панели. После этого щелкните правой кнопкой мыши значение **SAMLResponse** и выберите **Send to TextWizard** (Отправить в TextWizard). В меню **Transform** (Преобразование) выберите **From Base64** (Из Base64), чтобы расшифровать токен и просмотреть его содержимое.

**Примечание**. Чтобы просмотреть содержимое этого HTTP-запроса, Fiddler может предложить настроить шифрование трафика HTTPS. Это будет необходимо сделать.

## <a name="related-articles"></a>Связанные статьи
* [Указатель статьей по управлению приложениями в Azure Active Directory](../active-directory-apps-index.md)
* [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Настройка утверждений, выпущенных в маркере SAML для предварительно интегрированных приложений](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png