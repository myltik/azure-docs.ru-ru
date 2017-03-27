---
title: "Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для Windows 10 и Windows Server 2016 | Документация Майкрософт"
description: "Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для Windows 10 и Windows Server 2016."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для Windows 10 и Windows Server 2016

Это руководство касается следующих клиентов:

-    Windows 10
-    Windows Server 2016

Сведения о других клиентах Windows см. в статье [Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для клиентов Windows нижнего уровня](active-directory-device-registration-troubleshoot-windows-legacy.md).

В этом руководстве предполагается, что вы [настроили автоматическую регистрацию присоединенных к домену устройств Windows в Azure Active Directory](active-directory-device-registration-get-started.md) для выполнения следующих сценариев:

- [Условный доступ на основе устройств](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Корпоративное перемещение параметров](active-directory-windows-enterprise-state-roaming-overview.md)

- [Настройка Windows Hello для бизнеса](active-directory-azureadjoin-passport-deployment.md)


Этот документ содержит рекомендации по устранению неполадок для устранения потенциальных проблем. 

Регистрация поддерживается в обновлении Windows 10 от ноября 2015 г. и в более поздних обновлениях.  
Чтобы выполнить приведенные выше сценарии, мы рекомендуем использовать юбилейное обновление.

## <a name="step-1-retrieve-the-registration-status"></a>Шаг 1. Получение сведений о состоянии регистрации 

**Чтобы получить сведения о состоянии регистрации, сделайте следующее:**

1. Запустите командную строку от имени администратора.

2. Введите **dsregcmd/status**.



    +----------------------------------------------------------------------+
    | Состояние устройства                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | Состояние пользователя                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>Шаг 2. Анализ сведений о состоянии регистрации 

Просмотрите следующие поля и убедитесь, что для них заданы ожидаемые значения.

### <a name="azureadjoined--yes"></a>AzureAdJoined: YES  

Это поле показывает, зарегистрировано ли устройство в Azure AD. Если в поле отображается значение NO, то регистрация не завершена. 

**Возможные причины:**

- Произошла ошибка аутентификации для регистрации компьютера.

- Компьютер не может обнаружить прокси-сервер HTTP в организации.

- Компьютеру не удается получить доступ к Azure AD, чтобы пройти аутентификацию, или Azure DRS, чтобы пройти регистрацию.

- Компьютер может быть не подключен к внутренней сети организации или к VPN в пределах "прямой видимости" локального контроллера домена AD.

- Доверенный платформенный модуль на компьютере может быть неисправен.

- Службы, упомянутые в документе выше, могут быть неправильно настроены. Это понадобится перепроверить. Ниже приведены распространенные примеры.

    - На сервере федерации нет включенных конечных точек WS-Trust.

    - На сервере федерации может быть запрещена входящая аутентификация компьютеров в сети с использованием встроенной проверки подлинности Windows.

    - Нет объекта точки подключения службы, указывающего на имя проверенного домена в Azure AD в лесу AD, к которому относится компьютер.

---

### <a name="domainjoined--yes"></a>DomainJoined: YES  

Это поле показывает, присоединено ли устройство к локальному каталогу Active Directory или нет. Устройство не сможет автоматически зарегистрироваться в Azure AD, если отображается значение **NO**. Сначала проверьте, может ли устройство присоединиться к локальному каталогу Active Directory перед регистрацией в Azure AD. Узнайте о возможностях присоединения Azure Active Directory, если вы хотите присоединить компьютер к Azure AD напрямую.

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Это поле показывает, зарегистрировано ли устройство в Azure AD в качестве личного устройства (с пометкой "Присоединено к рабочей области"). Для присоединенного к домену компьютера, зарегистрированного в Azure AD, значение этого поля должно быть NO. Однако если отображается значение YES, это означает, что до выполнения регистрации компьютера была добавлена рабочая или учебная учетная запись. В таком случае при использовании версии юбилейного обновления Windows 10 учетная запись будет игнорироваться (версия 1607 при выполнении команды WinVer в окне "Запуск" или в окне командной строки).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: YES и AzureADPrt: YES
  
Эти поля показывают, что при входе в систему устройства пользователь успешно прошел аутентификацию в Azure AD. Ниже приведены возможные причины, по которым может отображаться значение NO.

- С устройством при регистрации был связан недопустимый ключ к хранилищу данных в доверенном платформенном модуле (проверьте KeySignTest во время работы с повышенными привилегиями).

- Наличие альтернативного имени пользователя.

- Прокси-сервер HTTP не найден.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Automatic device registration FAQ](active-directory-device-registration-faq.md) (Часто задаваемые вопросы об автоматической регистрации устройств) 
