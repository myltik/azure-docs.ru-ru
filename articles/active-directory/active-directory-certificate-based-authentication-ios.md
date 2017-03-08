---
title: "Аутентификация на основе сертификата в Azure Active Directory на устройстве iOS | Документация Майкрософт"
description: "Узнайте о поддерживаемых сценариях и требованиях к настройке аутентификации на основе сертификата в решениях на устройствах iOS."
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c4d95068b5e6472dbb809e675a47cb9eada7ca66
ms.openlocfilehash: f643deb0b76c598df9bd983474b4bd2bcba129e7
ms.lasthandoff: 02/16/2017


---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Аутентификация на основе сертификата в Azure Active Directory на устройстве iOS

Аутентификация на основе сертификата (CBA) позволяет Azure Active Directory выполнять аутентификацию с помощью сертификата клиента на устройстве Windows, Android или iOS при подключении учетной записи Exchange Online к: 

* мобильным приложениям Office, таким как Microsoft Outlook и Microsoft Word;   
* клиентам Exchange ActiveSync (EAS). 

Настройка данной функции избавляет от необходимости ввода имени пользователя и пароля в определенных почтовых клиентах и приложениях Microsoft Office на мобильных устройствах. 

В этой статье приведены требования и поддерживаемые сценарии для настройки CBA на устройстве Android для пользователей клиентов в тарифных планах Office 365 корпоративный, бизнес, для образования и для государственных организаций США. 

В тарифных планах Office 365 US Government Defense и Federal доступна предварительная версия этой функции.




## <a name="office-mobile-applications-support"></a>Поддержка мобильных приложений Office

| Приложения | Поддержка |
| --- | --- |
| Word/Excel/PowerPoint |![Проверка][1] |
| OneNote |![Проверка][1] |
| OneDrive |![Проверка][1] |
| Outlook |![Проверка][1] |
| Yammer |![Проверка][1] |
| Skype для бизнеса |Скоро |

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

Некоторые приложения Office (с поддержкой современной проверки подлинности) отправляют в Azure AD запрос с текстом *prompt=login*. По умолчанию Azure AD преобразует этот текст в запросе к службам AD FS в текст *wauth=usernamepassworduri* (запрашивает у AD FS выполнение проверки подлинности с помощью имени пользователя и пароля) и *wfresh=0* (запрашивает у AD FS игнорировать состояние единого входа и выполнять проверку подлинности заново). Чтобы включить проверку подлинности на основе сертификатов для этих приложений, необходимо изменить поведение Azure AD по умолчанию. Просто задайте для параметра *PromptLoginBehavior* в настройках федеративного домена значение *Отключено*. Для выполнения этой задачи можно использовать командлет [MSOLDomainFederationSettings](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msoldomainfederationsettings):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`
  

## <a name="exchange-activesync-clients-support"></a>Поддержка клиентов Exchange ActiveSync
В iOS версии 9 и выше поддерживается собственный почтовый клиент iOS. Чтобы определить, поддерживается ли эта функция во всех остальных приложениях Exchange ActiveSync, обратитесь к разработчику приложения.  


## <a name="next-steps"></a>Дальнейшие действия

Чтобы настроить аутентификацию на основе сертификата в своей среде, ознакомьтесь с инструкциями в статье [Get started with certificate-based authentication in Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) (Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory).


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

