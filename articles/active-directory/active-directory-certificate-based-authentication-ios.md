---
title: "Аутентификация на основе сертификата в Azure Active Directory на устройстве iOS | Документация Майкрософт"
description: "Узнайте о поддерживаемых сценариях и требованиях к настройке аутентификации на основе сертификата в решениях на устройствах iOS."
services: active-directory
author: MarkusVi
documentationcenter: na
manager: mtillman
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c9ae098df6e2357ca4bda513c0de5f5d42c7d9af
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Аутентификация на основе сертификата в Azure Active Directory на устройстве iOS

Аутентификация на основе сертификата (CBA) позволяет Azure Active Directory выполнять аутентификацию с помощью сертификата клиента на устройстве Windows, Android или iOS при подключении учетной записи Exchange Online к:

* мобильным приложениям Office, таким как Microsoft Outlook и Microsoft Word;   
* клиентам Exchange ActiveSync (EAS).

Настройка данной функции избавляет от необходимости ввода имени пользователя и пароля в определенных почтовых клиентах и приложениях Microsoft Office на мобильных устройствах.

В этой статье приведены требования и поддерживаемые сценарии для настройки CBA на устройстве iOS (Android) для пользователей клиентов в тарифных планах Office 365 корпоративный, бизнес, для образования, для государственных организаций США, Китая и Германии.

В тарифных планах Office 365 US Government Defense и Federal доступна предварительная версия этой функции.




## <a name="microsoft-mobile-applications-support"></a>Поддержка мобильных приложений Microsoft

| Приложения | Поддержка |
| --- | --- |
| Приложение Azure Information Protection |![Проверка][1] |
| Корпоративный портал Intune |![Проверка][1] |
| Microsoft Teams |![Проверка][1] |
| OneNote |![Проверка][1] |
| OneDrive |![Проверка][1] |
| Outlook |![Проверка][1] |
| Power BI |![Проверка][1] |
| Skype для бизнеса |![Проверка][1] |
| Word/Excel/PowerPoint |![Проверка][1] |
| Yammer |![Проверка][1] |


## <a name="requirements"></a>Требования

Устройство должно иметь операционную систему iOS 9 и более поздней версии.

Необходимо настроить сервер федерации.  

Для приложений Office на iOS требуется Microsoft Authenticator.  

Чтобы служба Azure Active Directory могла отзывать сертификат клиента, маркер AD FS должен иметь следующие утверждения:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (серийный номер сертификата клиента);
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (строка для издателя сертификата клиента).

Azure Active Directory добавляет эти утверждения в маркер обновления, если они доступны в маркере AD FS (или любом другом токене SAML). Когда требуется проверить маркер обновления, эта информация используется для проверки отзыва.

Рекомендуется обновить страницы ошибок AD FS следующими сведениями:

* требованием установки Microsoft Authenticator для iOS;
* инструкциями о получении сертификата пользователя.

Дополнительные сведения см. в разделе [Настройка страниц входа AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Некоторые приложения Office (с поддержкой современной проверки подлинности) отправляют в Azure AD запрос с текстом *prompt=login*. По умолчанию Azure AD преобразует этот текст в запросе к службам AD FS в текст *wauth=usernamepassworduri* (запрашивает у AD FS выполнение проверки подлинности с помощью имени пользователя и пароля) и *wfresh=0* (запрашивает у AD FS игнорировать состояние единого входа и выполнять проверку подлинности заново). Чтобы включить проверку подлинности на основе сертификатов для этих приложений, необходимо изменить поведение Azure AD по умолчанию. Просто задайте для параметра *PromptLoginBehavior* в настройках федеративного домена значение *Отключено*.
Для выполнения этой задачи можно использовать командлет [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`


## <a name="exchange-activesync-clients-support"></a>Поддержка клиентов Exchange ActiveSync
В iOS версии 9 и выше поддерживается собственный почтовый клиент iOS. Чтобы определить, поддерживается ли эта функция во всех остальных приложениях Exchange ActiveSync, обратитесь к разработчику приложения.  


## <a name="next-steps"></a>Дополнительная информация

Чтобы настроить аутентификацию на основе сертификата в своей среде, ознакомьтесь с инструкциями в статье [Get started with certificate-based authentication in Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) (Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory).


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
