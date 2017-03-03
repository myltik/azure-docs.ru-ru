---
title: "Аутентификация на основе сертификата в Azure Active Directory на устройстве Android | Документация Майкрософт"
description: "Узнайте о поддерживаемых сценариях и требованиях к настройке аутентификации на основе сертификата в решениях на устройствах Android."
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c4d95068b5e6472dbb809e675a47cb9eada7ca66
ms.openlocfilehash: 112f28517196b3b656611a03ed12a40f2e049174
ms.lasthandoff: 02/16/2017


---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Аутентификация на основе сертификата в Azure Active Directory на устройстве Android


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
| Skype для бизнеса |![Проверка][1] |

### <a name="implementation-requirements"></a>Требования к реализации

На устройстве должна быть установлена ОС Android версии 5.0 (Lollipop) и выше. 

Необходимо настроить сервер федерации.  

Чтобы служба Azure Active Directory могла отзывать сертификат клиента, маркер AD FS должен иметь следующие утверждения:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (серийный номер сертификата клиента); 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (строка для издателя сертификата клиента). 

Azure Active Directory добавляет эти утверждения в маркер обновления, если они доступны в маркере AD FS (или любом другом токене SAML). Когда требуется проверить маркер обновления, эта информация используется для проверки отзыва. 

Рекомендуется обновить страницы ошибок AD FS инструкциями о получении сертификата пользователя.  
Дополнительные сведения см. в разделе [Настройка страниц входа AD FS](https://technet.microsoft.com/library/dn280950.aspx).  

Некоторые приложения Office (с поддержкой современной проверки подлинности) отправляют в Azure AD запрос с текстом *prompt=login*. По умолчанию Azure AD преобразует этот текст в запросе к службам AD FS в текст *wauth=usernamepassworduri* (запрашивает у AD FS выполнение проверки подлинности с помощью имени пользователя и пароля) и *wfresh=0* (запрашивает у AD FS игнорировать состояние единого входа и выполнять проверку подлинности заново). Чтобы включить проверку подлинности на основе сертификатов для этих приложений, необходимо изменить поведение Azure AD по умолчанию. Просто задайте для параметра *PromptLoginBehavior* в настройках федеративного домена значение *Отключено*. Для выполнения этой задачи можно использовать командлет [MSOLDomainFederationSettings](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msoldomainfederationsettings):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`



## <a name="exchange-activesync-clients-support"></a>Поддержка клиентов Exchange ActiveSync
Поддерживаются некоторые приложения Exchange ActiveSync, работающие на Android 5.0 (Lollipop) или более поздней версии. Чтобы определить, поддерживает ли почтовая программа эту функцию, обратитесь к разработчику приложения. 


## <a name="next-steps"></a>Дальнейшие действия

Чтобы настроить аутентификацию на основе сертификата в своей среде, ознакомьтесь с инструкциями в статье [Get started with certificate-based authentication in Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) (Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory).

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png

