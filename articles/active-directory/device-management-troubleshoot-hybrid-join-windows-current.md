---
title: "Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory | Документация Майкрософт"
description: "Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory ."
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
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ffce288555e718b37882f30f877162d0c0467f1f
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory 

Это руководство касается следующих клиентов:

-   Windows 10
-   Windows Server 2016

Сведения о других клиентах Windows см. в статье [Troubleshooting hybrid Azure Active Directory joined down-level devices](device-management-troubleshoot-hybrid-join-windows-legacy.md) (Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory).

В этом разделе предполагается, что вы [настроили гибридное присоединение устройств к Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) для поддержки следующих сценариев:

- Условный доступ на основе устройств

- [Корпоративное перемещение параметров](active-directory-windows-enterprise-state-roaming-overview.md)

- [Настройка Windows Hello для бизнеса](active-directory-azureadjoin-passport-deployment.md)


Этот документ содержит рекомендации по устранению неполадок для устранения потенциальных проблем. 


Для Windows 10 и Windows Server 2016 гибридное присоединение к Azure Active Directory поддерживает обновление Windows 10 от ноября 2015 г. Мы рекомендуем использовать юбилейное обновление.

## <a name="step-1-retrieve-the-join-status"></a>Шаг 1. Получение сведений о состоянии присоединения 

**Для получения сведений о состоянии присоединения выполните следующие действия:**

1. Запустите командную строку от имени администратора.

2. Введите **dsregcmd/status**.



    +----------------------------------------------------------------------+
    | Состояние устройства                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: NO DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft Platform Crypto Provider TpmProtected: YES KeySignTest: : MUST Run elevated to test.
                  Idp: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn:ms-drs:enterpriseregistration.windows.net DomainJoined: YES DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | Состояние пользователя                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizations         WamDefaultId: https://login.microsoft.com       WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt: YES



## <a name="step-2-evaluate-the-join-status"></a>Шаг 2. Анализ состояния присоединения 

Просмотрите следующие поля и убедитесь, что для них заданы ожидаемые значения.

### <a name="azureadjoined--yes"></a>AzureAdJoined: YES  

Это поле показывает, присоединено ли устройство к Azure AD. Если отображается значение **NO**, то присоединение к Azure AD еще не завершено. 

**Возможные причины:**

- Произошла ошибка аутентификации для присоединения компьютера.

- Компьютер не может обнаружить прокси-сервер HTTP в организации.

- Компьютеру не удается получить доступ к Azure AD, чтобы пройти аутентификацию, или к Azure DRS, чтобы пройти регистрацию.

- Компьютер может быть не подключен к внутренней сети организации или к VPN в пределах "прямой видимости" локального контроллера домена AD.

- Доверенный платформенный модуль (TPM) на компьютере может быть неисправен.

- Службы, упомянутые в документе выше, могут быть неправильно настроены. Это понадобится перепроверить. Ниже приведены распространенные примеры.

    - На сервере федерации нет включенных конечных точек WS-Trust.

    - На сервере федерации может быть запрещена входящая аутентификация компьютеров в сети с использованием встроенной проверки подлинности Windows.

    - Нет объекта точки подключения службы, указывающего на имя проверенного домена в Azure AD в лесу AD, к которому относится компьютер.

---

### <a name="domainjoined--yes"></a>DomainJoined: YES  

Это поле показывает, присоединено ли устройство к локальному каталогу Active Directory или нет. Если отображается значение **NO**, то устройство не может выполнить гибридное присоединение к Azure AD.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Это поле показывает, зарегистрировано ли устройство в Azure AD в качестве личного устройства (с пометкой *Присоединено к рабочей области*). Этот параметр должен иметь значение **NO** для присоединенных к домену компьютеров, на которых также настроено гибридное присоединение к Azure AD. Если этот параметр имеет значение **YES**, то рабочая или учебная учетная запись была добавлена до завершения гибридного присоединения к Azure AD. В таком случае при использовании версии юбилейного обновления Windows 10 (1607) учетная запись игнорируется.

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: YES и AzureADPrt: YES
  
Эти поля показывают, прошел ли пользователь аутентификацию в Azure AD при входе в устройство. Если эти параметры имеют значение **NO**, то причины могут быть следующими:

- С устройством при регистрации был связан недопустимый ключ к хранилищу данных в доверенном платформенном модуле (проверьте KeySignTest во время работы с повышенными привилегиями).

- Наличие альтернативного имени пользователя.

- Прокси-сервер HTTP не найден.

## <a name="next-steps"></a>Дальнейшие действия

Ответы на вопросы можно найти в статье [Azure Active Directory device management FAQ](device-management-faq.md) (Часто задаваемые вопросы по управлению устройствами Azure Active Directory). 