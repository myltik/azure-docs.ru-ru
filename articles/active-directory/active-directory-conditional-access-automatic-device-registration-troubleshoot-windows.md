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
ms.date: 02/15/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: d3688b6a71dcd9964839da3cda35413d00f362d1
ms.openlocfilehash: 836d43b17515beb565748f28603069c7c6a52fbf
ms.lasthandoff: 02/15/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для Windows 10 и Windows Server 2016

Это руководство касается следующих клиентов:

-    Windows 10
-    Windows Server 2016

Сведения о других клиентах Windows см. в статье [Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров для клиентов Windows нижнего уровня](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md).

В этом руководстве предполагается, что вы настроили автоматическую регистрацию присоединенных к домену устройств в соответствии со сведениями, указанными в статье [Настройка автоматической регистрации присоединенных к домену устройств Windows в Azure Active Directory](active-directory-conditional-access-automatic-device-registration-get-started.md), чтобы обеспечить выполнение следующих сценариев:

1.    [Условный доступ на основе устройств](active-directory-conditional-access-automatic-device-registration-setup.md)

2.    [Корпоративное перемещение параметров](active-directory-windows-enterprise-state-roaming-overview.md)

3.    [Настройка Windows Hello для бизнеса](active-directory-azureadjoin-passport-deployment.md)


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

1.    Произошла ошибка аутентификации для регистрации компьютера.

2.    Компьютер не может обнаружить прокси-сервер HTTP в организации.

3.    Компьютеру не удается получить доступ к Azure AD, чтобы пройти аутентификацию, или Azure DRS, чтобы пройти регистрацию.

4.    Компьютер может быть не подключен к внутренней сети организации или к VPN в пределах "прямой видимости" локального контроллера домена AD.

5.    Доверенный платформенный модуль на компьютере может быть неисправен.

6.    Службы, упомянутые в документе выше, могут быть неправильно настроены. Это понадобится перепроверить. Ниже приведены распространенные примеры.

    1.    На сервере федерации нет включенных конечных точек WS-Trust.

    2.    На сервере федерации может быть запрещена входящая аутентификация компьютеров в сети с использованием встроенной проверки подлинности Windows.

    3.    Нет объекта точки подключения службы, указывающего на имя проверенного домена в Azure AD в лесу AD, к которому относится компьютер.

---

### <a name="domainjoined--yes"></a>DomainJoined: YES  

Это поле показывает, присоединено ли устройство к локальному каталогу Active Directory или нет. Устройство не сможет автоматически зарегистрироваться в Azure AD, если отображается значение NO. Сначала проверьте, может ли устройство присоединиться к локальному каталогу Active Directory перед регистрацией в Azure AD. Узнайте о возможностях присоединения Azure Active Directory, если вы хотите присоединить компьютер к Azure AD напрямую.

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Это поле показывает, зарегистрировано ли устройство в Azure AD в качестве личного устройства (с пометкой "Присоединено к рабочей области"). Для присоединенного к домену компьютера, зарегистрированного в Azure AD, значение этого поля должно быть NO. Однако если отображается значение YES, это означает, что до выполнения регистрации компьютера была добавлена рабочая или учебная учетная запись. В таком случае при использовании версии юбилейного обновления Windows 10 учетная запись будет игнорироваться (версия 1607 при выполнении команды WinVer в окне "Запуск" или в окне командной строки).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: YES и AzureADPrt: YES
  
Эти поля показывают, что при входе в систему устройства пользователь успешно прошел аутентификацию в Azure AD. Ниже приведены возможные причины, по которым может отображаться значение NO.

1.    С устройством при регистрации был связан недопустимый ключ к хранилищу данных в доверенном платформенном модуле (проверьте KeySignTest во время работы с повышенными привилегиями).

2.    Наличие альтернативного имени пользователя.

3.    Прокси-сервер HTTP не найден.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Automatic device registration FAQ](active-directory-conditional-access-automatic-device-registration-faq.md) (Часто задаваемые вопросы об автоматической регистрации устройств) 
