---
title: Новые возможности Заметки о выпуске для Azure Active Directory | Документация Майкрософт
description: Узнайте о новых возможностях Azure Active Directory (Azure AD), включая последние заметки о выпуске, известные проблемы, исправления ошибок, нерекомендуемые функции и предстоящие изменения.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b7ad535976508cb195991c374995b0a0b6e45e10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159874"
---
# <a name="whats-new-in-azure-active-directory"></a>Новые возможности Azure Active Directory


> Будьте в курсе новых возможностей Azure Active Directory (Azure AD), подписавшись на этот [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [веб-канал](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Azure AD усовершенствуется на постоянной основе. Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

-   Последние выпуски.
-   Известные проблемы
-   Исправления ошибок
-   Нерекомендуемые функции.
-   Планы по изменениям.

Эта страница обновляется ежемесячно, поэтому регулярно пересматривайте ее.


## <a name="april-2018"></a>Апрель 2018 г.
 


### <a name="azure-ad-b2c-access-token-are-ga"></a>Общедоступная версия маркера доступа Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 

Теперь доступ к интерфейсам веб-API, защищенным Azure AD B2C, можно получать с помощью маркеров доступа. Эта функция переходит от общедоступной предварительной версии к общедоступной версии. Улучшен пользовательский интерфейс для настройки приложений Azure AD B2C и веб-API, а также внесены другие незначительные улучшения.
 
Дополнительные сведения см. в статье [Azure AD B2C: запрос маркеров доступа](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).


---
 

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Тестирование конфигурации единого входа для приложений на основе SAML

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 

При настройке приложений с функцией единого входа на основе SAML вы можете протестировать интеграцию на странице конфигурации. Если в процессе входа возникнет ошибка, ее можно указать в интерфейсе тестирования, и Azure AD предоставит вам пошаговые инструкции по решению конкретной проблемы.

Дополнительные сведения можно найти в разделе 

- [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Отладка единого входа на основе SAML в приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)


---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Условия использования Azure AD теперь включают отчеты "на пользователя"

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 

Администраторы теперь могут выбрать заданные условия использования и просмотреть список всех пользователей, которые приняли эти условия, а также дату и время этого события.


Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory"](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: обнаружение ненадежных IP-адресов для защиты блокировки экстрасети AD FS 

**Тип.** Новая функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Мониторинг и отчетность.
 

Connect Health теперь поддерживает обнаружение IP-адресов, для которых превышается пороговое количество неудачных попыток входа с именем пользователя и паролем в час или в день. Эта функция предоставляет следующие возможности:

- Полный отчет, который содержит IP-адрес и число неудачных попыток входа и формируется каждый час или каждый день с настраиваемым пороговым значением.
- Оповещения по электронной почте, которые появляются, когда для конкретного IP-адреса превышается порог неудачных попыток входа с именем пользователя и паролем в час или в день.
- Возможность скачать отчет для подробного анализа данных


Дополнительные сведения см. в разделе об [отчете по ненадежным IP-адресам](https://aka.ms/aadchriskyip).

 

---
 

### <a name="easy-app-config-with-metadata-file-or-url"></a>Простая настройка приложений с помощью файла метаданных или URL-адреса

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 

На странице приложений Enterprise администраторы могут отправить файл метаданных SAML для настройки входа на основе SAML для приложений из коллекции AAD и не входящих в нее.

Кроме того, URL-адрес метаданных федерации приложения Azure AD можно использовать для настройки единого входа в целевом приложении.

Дополнительные сведения см. в статье [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).
 

---
 

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Выпущена общедоступная версия функции "Условия использования"

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 

Функция "Условия использования" Azure AD перемещена из общедоступной предварительной версии в общедоступную версию.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory"](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

 

---
 

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Предоставление или отзыв приглашений пользователям B2B из отдельных организаций

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Теперь можно указать, с какими партнерскими организациями вы хотите совместно использовать данные и совместно работать в службе совместной работы Azure AD B2B. Для этого можно создать список определенных разрешенных или запрещенных доменов. При блокировке домена с помощью этих возможностей сотрудники больше не смогут отправлять приглашения пользователям из этого домена.

Это помогает контролировать доступ к ресурсам, обеспечивая при этом удобную работу для утвержденных пользователей.

Функция совместной работы B2B доступна для всех клиентов Azure Active Directory. Ее можно использовать в сочетании с функциями Azure AD Premium, такими как условный доступ и защита идентификации для более детального контроля над тем, когда и как внешние бизнес-пользователи выполняют вход и получают доступ.

Дополнительные сведения см. в статье [Предоставление или отзыв приглашений пользователям B2B из отдельных организаций](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

 

---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 

В апреле 2018 года мы добавили в коллекцию следующие 13 новых приложений с поддержкой федерации:



Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (локальный)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial), [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf и [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial).



 Список доступных руководств можно найти здесь: [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

Дополнительные сведения см. в статье [Добавление приложения в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).


 

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Предоставление пользователям B2B в Azure AD доступа к локальным приложениям (общедоступная предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** B2B.  
**Возможности продукта.** B2B и B2C.
 

Теперь организации, использующие возможности совместной работы в Azure Active Directory (AAD) B2B для приглашения гостевых пользователей из партнерских организаций в AAD, могут предоставлять таким пользователям B2B доступ к локальным приложениям. В таких приложениях нужно использовать аутентификацию на основе SAML или встроенную проверку подлинности Windows (IWA) с ограниченным делегированием Kerberos (KCD).

Дополнительные сведения см. в статье [Предоставление пользователям B2B в Azure AD доступа к локальным приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).
 

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Получение руководств по интеграции единого входа из Azure Marketplace

**Тип.** Измененная функция.  
**Категория службы.** Другая.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 

Если приложение, указанное в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) поддерживает единый вход на основе SAML, то, щелкнув **Получить**, можно открыть руководство по интеграции, связанное с этим приложением. 


---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Повышение производительности автоматической подготовки пользователей Azure AD в приложениях SaaS

**Тип.** Измененная функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 

Ранее клиенты, которые с помощью соединителей подготавливали пользователей Azure Active Directory для приложений SaaS (например, Salesforce, ServiceNow и Box), могли испытывать значительное снижение производительности. Так происходило, если в их клиентах Azure AD содержалось свыше 100 000 совокупных пользователей и групп, и для определения пользователей, которых следует подготовить, они использовали назначения пользователей и групп.

Второго апреля мы развернули значительные улучшения производительности для службы подготовки Azure AD, которые существенно сокращают время, необходимое на выполнение начальной синхронизации Azure Active Directory с целевыми приложениями SaaS.


В результате многие клиенты, у которых начальная синхронизация с приложениями занимала много дней или никогда не завершалась, сейчас завершают ее в течение нескольких минут или часов.

Дополнительные сведения см. в разделе [Что происходит при подготовке](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning).

---
 

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Самостоятельный сброс пароля с экрана блокировки Windows 10 для компьютеров, присоединенных к гибридной среде Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Проверка подлинности пользователей.
 

Мы обновили компонент Windows 10 SSPR, добавив поддержку компьютеров, присоединенных к гибридной среде Azure AD. Эта функция доступна в Windows 10 RS4 и дает пользователям возможность сбрасывать пароль с экрана блокировки на компьютере с Windows 10. Эту функцию могут применять пользователи, которые включены и зарегистрированы для самостоятельного сброса пароля.

Дополнительные сведения см. в статье [Сброс пароля Azure AD на экране входа](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).
 

---



## <a name="march-2018"></a>Март 2018 г.
 

### <a name="certificate-expire-notification"></a>Уведомление об истечении срока действия сертификата

**Тип.** Исправление.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 
Azure AD отправляет уведомление о сертификатах с истекающим сроком действия для приложений из коллекции или не из коллекции. 

Некоторые пользователи не получают уведомления о корпоративных приложениях, для которых настроен единый вход для на основе SAML. Эта проблема была устранена. Azure AD отправляет уведомление для сертификатов, срок действия которых истекает через 7, 30 и 60 дней. Это событие отображается в журналах аудита. 

Дополнительные сведения можно найти в разделе 

- [Управление сертификатами для федеративного единого входа в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Отчеты о действиях аудита на портале Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

 
---
 

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Поставщики удостоверений Twitter и GitHub в Azure AD B2C

**Тип.** Новая функция.  
**Категория службы.** B2C — управление удостоверениями клиентов.  
**Возможности продукта.** B2B и B2C.
 
Теперь в Azure AD B2C можно добавить Twitter или GitHub в качестве поставщика удостоверений. От общедоступной предварительной версии Twitter переходит к общедоступной версии. Для GitHub выпускается общедоступная предварительная версия.


Дополнительные сведения см. в статье [Что такое служба совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---


### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Ограничение доступа в браузере с помощью Intune Managed Browser и условного доступа на основе приложений Azure AD для iOS и Android

**Тип.** Новая функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.
 

**Сейчас на этапе общедоступной предварительной версии**

**Единый вход в Intune Managed Browser.** Ваши сотрудники могут использовать единый вход через собственные клиенты (например, Microsoft Outlook) и Intune Managed Browser для всех подключенных приложений Azure AD.

**Поддержка условного доступа в Intune Managed Browser.** Теперь вы можете обязать сотрудников использовать Intune Managed Browser с соблюдением политик условного доступа на основе приложений.

Дополнительные сведения об этом см. в нашем [блоге](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Дополнительные сведения можно найти в разделе 

- [Условный доступ на основе приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

- [Управление доступом в Интернет с помощью политик Managed Browser в Microsoft Intune](https://aka.ms/managedbrowser)  



---
 

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Командлеты для прокси приложения в общедоступной версии модуля PowerShell

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможность продукта.** Контроль доступа.
 
Теперь в общедоступной версии модуля PowerShell поддерживаются командлеты для прокси приложения. Обратите внимание на то, что для этого требуется своевременно обновлять модули PowerShell. Если используемая версия модуля не будет обновляться более одного года, то некоторые командлеты могут перестать работать. 


Дополнительные сведения см. в разделе [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Поддержка собственных клиентов Office 365 службой простого единого входа с помощью неинтерактивного протокола

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Пользователь, применяющий собственные клиенты Office 365 (версии 16.0.8730.xxxx и более поздней версии), может использовать автоматический вход с помощью простого единого входа. Поддержка этой возможности обеспечивается за счет добавления в Azure AD неинтерактивного протокола (WS-Trust).

Дополнительные сведения см. в разделе [Как работает вход с функцией простого единого входа в собственном клиенте?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

 
---
 

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenanted-endpoints"></a>Пользователи могут использовать автоматический вход с помощью простого единого входа, если приложение отправляет запросы на вход на клиентские конечные точки Azure AD

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Пользователи могут использовать автоматический вход с помощью простого единого входа, если приложение (например, `https://contoso.sharepoint.com`) отправляет запросы на вход на клиентские конечные точки Azure AD, то есть `https://login.microsoftonline.com/contoso.com/<..>` или `https://login.microsoftonline.com/<tenant_ID>/<..>`, вместо обычной конечной точки Azure AD (`https://login.microsoftonline.com/common/<...>`).

Дополнительные сведения см. в разделе [Устранение неполадок с простым единым входом Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Необходимо добавить в параметры зоны интрасети пользователей только один URL-адрес Azure AD вместо двух (как было раньше) для развертывания простого единого входа

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Чтобы развернуть простой единый вход для пользователей, необходимо добавить только один URL-адрес Azure AD в параметры зоны интрасети пользователей с помощью групповой политики Active Directory: `https://autologon.microsoftazuread-sso.com`. Ранее клиентам требовалось добавить два URL-адреса.

Дополнительные сведения см. в разделе [Устранение неполадок с простым единым входом Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В марте 2018 года мы добавили в коллекцию приложений следующие 15 новых приложений с поддержкой федерации:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Документацию по этим приложениям можно найти здесь: [https://aka.ms/appstutorial](https://aka.ms/appstutorial)


 
---
 

### <a name="pim-for-azure-resources-is-generally-available"></a>Выпущена общедоступная версия PIM для ресурсов Azure

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Теперь при использовании управления привилегированными пользователями Azure AD для ролей каталога можно применять возможности временного ограничения доступа и назначения для таких ролей ресурсов Azure, как подписки, группы ресурсов, виртуальные машины, а также любого другого ресурса, поддерживаемого Azure Resource Manager. Вы можете применять Многофакторную идентификацию при JIT-активации ролей и планировать активацию в соответствии с утвержденными периодами изменений. Кроме того, в этом выпуске добавлены усовершенствования, отсутствующие в общедоступной предварительной версии, включая обновленный пользовательский интерфейс, рабочие процессы утверждения и возможность расширения ролей с истекающим сроком действия и обновления ролей с истекшим сроком действия.

Дополнительные сведения см. в статье [PIM для ресурсов Azure (предварительная версия)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).
 
---
 

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Добавление необязательных утверждений для токенов приложений (предварительная версия)

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Теперь приложение Azure AD может запрашивать пользовательские или необязательные утверждения в токенах JWT или SAML.  Это утверждения о пользователе или клиенте, которые не включены по умолчанию в токен из-за ограничения размера или применимости.  В настоящий момент доступна общедоступная предварительная версия этой функции для приложений Azure AD в конечных точках версии 1.0 и версии 2.0.  Ознакомьтесь с соответствующей документацией, чтобы получить сведения о том, какие утверждения могут быть добавлены и как изменить манифест приложения, чтобы запросить их.  

Дополнительные сведения см. в статье [Optional claims in Azure AD (preview)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) (Необязательные утверждения в Azure AD (предварительная версия)).
 
---
 

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD поддерживает PKCE для более безопасных потоков OAuth

**Тип.** Новая функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Документация по Azure AD была обновлена с учетом поддержки PKCE, что обеспечивает более безопасную связь во время потока предоставления кода авторизации OAuth 2.0.  S256 и code_challenge без шифрования поддерживаются в конечных точках версии 1.0 и версии 2.0. 

Дополнительные сведения см. в статье [Request an authorization code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code) (Запрос кода авторизации). 

 
---
 

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Поддержка подготовки всех значений атрибутов пользователя, доступных в API Get_Workers Workday

**Тип.** Новая функция.  
**Категория службы.** Подготовка приложений.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
Общедоступная предварительная версия функции подготовки входящих данных из Workday в Active Directory и Azure AD теперь поддерживает возможность извлечения и подготовки всех значений атрибутов в API Get_Workers Workday. Добавлена поддержка сотен дополнительных стандартных и пользовательских атрибутов, не входящих в состав первоначальной версии соединителя подготовки входящих данных Workday.

Дополнительные сведения см. в разделе [Настройка списка атрибутов пользователя Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes).

---



### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Изменение динамического членства в группе на статическое (и наоборот)

**Тип.** Новая функция.  
**Категория службы.** Управление группами.  
**Возможности продукта.** Совместная работа.
 
Можно изменить способ управления членством в группе. Это удобно, если требуется сохранить имя и идентификатор группы в системе, чтобы все существующие ссылки на эту группу по-прежнему были действительны. При создании новой группы эти ссылки потребовалось бы обновить.
Мы обновили центр администрирования Azure AD, добавив поддержку этой функции. Теперь клиенты могут изменять динамическое членство в существующих группах на статическое (и наоборот). Существующие командлеты PowerShell по-прежнему доступны.

Дополнительные сведения см. в разделе [Изменение динамического членства на статическое (и наоборот)](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa).

 

 
---
 

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Улучшенный выход при простом едином входе

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Проверка подлинности пользователей.
 
Ранее, если пользователи даже после явного выхода из приложения, защищенного Azure AD, пытались получить доступ к приложению Azure AD в своей корпоративной сети с собственных устройств, присоединенных к домену, они автоматически повторяли вход с помощью простого единого входа. Благодаря данному изменению поддерживается выход.  Это позволяет пользователям выбрать другую или ту же учетную запись Azure AD для входа вместо того, чтобы автоматически входить с помощью простого единого входа.

Дополнительные сведения см. в разделе [Устранение неполадок с простым единым входом Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).

 
---
 

### <a name="application-proxy-connector-version-154020-released"></a>Выпущен соединитель прокси приложения версии 1.5.402.0

**Тип.** Измененная функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Безопасность и защита удостоверений.
 
Эта версия соединителя постепенно разворачивалась в ноябре. Данная новая версия соединителя включает в себя следующие изменения.

- Теперь соединитель задает файлы cookie уровня домена, а не уровня поддомена. Это обеспечивает более удобный единый вход и позволяет избежать избыточных запросов аутентификации.
- Поддержка запросов на поблочное кодирование.
- Улучшенный мониторинг работоспособности соединителей. 
- Устранено несколько ошибок и повышена стабильность.

Дополнительные сведения см. в разделе [Сведения о соединителях прокси приложения Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

 
---
 

 



## <a name="february-2018"></a>Февраль 2018 г.
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>Улучшенная навигация для управления пользователями и группами

**Тип.** Планирование изменений.  
**Категория службы.** Управление каталогами.  
**Возможности продукта.** Каталог.
 

Упрощена навигация для управления пользователями и группами. Теперь вы можете переходить из обзора каталога непосредственно к списку всех пользователей с удобным доступом к списку удаленных пользователей, а также к списку всех групп с удобным доступом к параметрам управления группами. Кроме того, на странице обзора каталога можно выполнить поиск пользователя, группы, корпоративного приложения или регистрацию приложения.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Доступность журналов входов в систему и отчетов об аудите в Microsoft Azure, обслуживаемой 21Vianet (21Vianet в Azure для Китая)

**Тип.** Новая функция.  
**Категория службы.** Национальные облака.  
**Возможности продукта.** Мониторинг и отчетность.
 

Отчеты журнала действий Azure AD теперь доступны в Microsoft Azure, обслуживаемой экземплярами 21Vianet (21Vianet в Azure для Китая). Сюда относятся следующие журналы:

- **Журналы действий входов в систему.** Журналы всех входов в систему, связанные с вашим клиентом.

- **Журналы самостоятельного аудита паролей.** Все журналы аудита SSPR.

- **Журналы аудита управления каталогами.** Все журналы аудита, связанные с управлением каталогами, например управлением пользователями, управлением приложениями и другими видами управления.

Из этих журналов можно получить важные сведения о том, как работает ваша среда. Полученные данные позволят вам выполнять следующее:

- определять, как приложения и службы используются пользователями;

- устранить неполадки, влияющие на работу пользователей.

Дополнительные сведения об использовании этих отчетов см. в статье [Отчеты Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Просмотр отчетов об активности Azure AD с использованием роли "Читатель отчетов" (без прав администратора роли)

**Тип.** Новая функция.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Мониторинг и отчетность.
 

Прислушиваясь к предложениям клиентов предоставить ролям без прав администратора доступ к журналам действий Azure AD, мы предоставили пользователям, которым назначена роль "Читатель отчетов", доступ к действиям, связанным с входом и аудитом, на портале Azure, а также с помощью наших API Graph. 

Дополнительные сведения об использовании этих отчетов см. в статье [Отчеты Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Утверждение EmployeeID, доступное в виде атрибута пользователя и идентификатора пользователя

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Единый вход.
 

Утверждение **EmployeeID** можно настроить как идентификатор пользователя и атрибут пользователя для пользователей-участников и гостей B2B в приложении с входом на основе SAML из пользовательского интерфейса корпоративного приложения.

Дополнительные сведения см. в статье [Настройка утверждений, выпущенных в токене SAML для корпоративных приложений в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Упрощенное управление приложениями с использованием подстановочных знаков в Azure AD Application Proxy

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Проверка подлинности пользователей.
 

Чтобы облегчить процесс развертывания приложений и сократить административные расходы, мы теперь поддерживаем возможность публикации приложений с использованием подстановочных знаков. Чтобы опубликовать приложение с подстановочным знаком, можно выполнить стандартный поток публикации приложения, но во внутренних и внешних URL-адресах использовать подстановочный знак.

Дополнительные сведения см. в статье [Приложения с подстановочным знаком в прокси приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard).

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Новые командлеты для поддержки конфигурации прокси-сервера приложения

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Платформа.
 

В последнем выпуске модуля предварительной версии AzureAD PowerShell содержатся новые командлеты, позволяющие клиентам настраивать приложения прокси-сервера приложения с помощью PowerShell.

Ниже перечислены новые командлеты. 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Новые командлеты для поддержки конфигурации групп

**Тип.** Новая функция.  
**Категория службы.** Прокси-сервер приложения.  
**Возможности продукта.** Платформа.
 

В последнем выпуске модуля AzureAD PowerShell содержатся командлеты для управления группами в Azure AD. Эти командлеты ранее были доступны в модуле AzureADPreview и теперь добавлены в модуль AzureAD

К командлетам группы, которые теперь входят в общедоступную версию, относятся: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Доступна новая версия Azure AD Connect

**Тип.** Новая функция.  
**Категория службы.** AD Sync.  
**Возможности продукта.** Платформа.
 

Azure AD Connect предпочтительней использовать для синхронизации данных между Azure AD и локальными источниками данных, включая Windows Server Active Directory и LDAP.

**Важно!**
 
В этой сборке представлены изменения схем и правил синхронизации. После обновления служба синхронизации Azure AD Connect запустит действия полного импорта и полной синхронизации. Сведения о том, как изменить эту реакцию на событие, см. в разделе [Как отложить полную синхронизацию после обновления](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Этот выпуск содержит следующие обновления и изменения.

**Исправленные проблемы**

- Устранены ошибки с временным окном выполнения фоновых задач для страницы фильтрации разделов при переходе на следующую страницу.
- Устранена ошибка, вызывавшая нарушение прав доступа во время выполнения настраиваемого действия ConfigDB.
- Устранена ошибка восстановления после превышения времени ожидания подключения к SQL.
- Устранена ошибка, из-за которой сертификаты с подстановочными знаками SAN не проходили предварительную проверку.
- Устранена ошибка, из-за которой выполнение файла miiserver.exe завершалось сбоем при экспорте соединителя AAD.
- Устранена ошибка, когда при вводе неправильного пароля выполнялся вход в контроллер домена при запуске мастера AAD Connect для изменения конфигурации.

**Новые функции и внесенные улучшения**

- Для соответствия положениям GDPR мы обязаны указывать виды данных клиента, которые также использует корпорация Майкрософт (телеметрия, работоспособность и т. д.), предоставлять ссылки на подробную электронную документацию и давать вам возможность изменить настройки.  В рамках этого изменения нужно добавить следующее:
    - уведомление об обмене данными и о конфиденциальности на странице чистой установки EULA;

    - уведомление об обмене данными и о конфиденциальности на странице обновления;

    - новую дополнительную задачу **Параметры конфиденциальности**, которая позволяет пользователю изменить свои настройки.
 
- Телеметрия приложений. Администратор может включать и отключать этот класс данных.

- Данные о работоспособности Azure AD. Для управления параметрами работоспособности администратору необходимо зайти на портал работоспособности. После изменения политики службы агенты прочитают и применят ее.

- Добавлены действия настройки обратной записи устройств и индикатор выполнения для инициализации страницы.

- Усовершенствована общая диагностика, предоставляемая в отчете HTML, и полный сбор данных, предоставляемых в ZIP-файле или отчете HTML.

- Усовершенствована надежность автоматического обновления и добавлена дополнительная телеметрия для определения работоспособности сервера.

- Ограничены доступные разрешения привилегированных учетных записей в учетной записи соединителя AD. Мастер ограничивает разрешения привилегированных учетных записей для новых установок после создания учетной записи MSOL. Изменения влияют на быстрые установки и выборочные установки с автоматически создаваемой учетной записью.

- Изменен установщик. Теперь ему не нужно разрешение сопоставления безопасности для чистой установки AAD Connect.

- Новая служебная программа для устранения неполадок с синхронизацией определенного объекта. В настоящее время служебная программа проверяет следующее:

    - Несоответствие UserPrincipalName между синхронизированными объектом пользователя и учетной записью пользователя в клиенте Azure AD.
  
    - Фильтруется ли объект после синхронизации при фильтрации домена.
  
    - Фильтруется ли объект после синхронизации при фильтрации организационной единицы.

- Новая служебная программа для синхронизации текущего хэша паролей, хранящегося в локальном каталоге Active Directory для конкретной учетной записи пользователя. Служебная программа не требует изменения пароля. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Добавлены приложения, поддерживающие политики Защиты приложений Intune для использования с условным доступом на основе приложения Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.
 

Мы добавили больше приложений, которые поддерживают условный доступ на основе приложений. Теперь вы можете получить доступ к Office 365 и другим облачным приложениям, подключенным к Azure AD, с помощью этих клиентских приложений.

К концу февраля будут добавлены следующие приложения: 

- Microsoft PowerBI;

- Microsoft Launcher;

- Microsoft Invoicing.

Дополнительные сведения можно найти в разделе 

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>Условия использования обновлены для работы с мобильным приложением 

**Тип.** Измененная функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 

При отображении условий использования необходимо щелкнуть **Проблемы с просмотром? Щелкните здесь**. При выборе этой ссылки условия использования откроются, адаптировавшись под ваше устройство. Независимо от размера шрифта в документе или размера экрана устройства, документ можно увеличить, чтобы прочитать нужный фрагмент. 
 

---
 
## <a name="january-2018"></a>Январь 2018 г.
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Новые федеративные приложения доступны в коллекции приложений Azure AD 

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 

В январе 2018 года в коллекцию приложений были добавлены следующие новые приложения с поддержкой федерации:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [программное обеспечение управления конфиденциальностью OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [федеративный каталог IriusRisk](https://go.microsoft.com/fwlink/?linkid=864699) и [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Полный обзор всех доступных руководств см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Вход в систему с обнаруженным дополнительным риском

**Тип.** Новая функция.  
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.
 

Аналитические данные, которые вы получаете для обнаруженного события риска, привязаны к вашей подписке Azure AD. Выпуск Azure AD Premium P2 позволяет получать самые подробные сведения обо всех основных обнаружениях.

В выпуске Azure AD Premium P1 обнаружения, которые не покрывает лицензия, отображаются как событие риска "Вход в систему с обнаруженным дополнительным риском".

Дополнительные сведения см. в статье [События риска Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Скрытие приложений Office 365 на панели доступа пользователя

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.
 

Теперь вы можете лучше управлять тем, как приложения Office 365 отображаются на панелях доступа пользователя, с помощью нового пользовательского параметра. Он позволяет уменьшить количество приложений, которые отображаются на панелях доступа пользователей, если вы предпочитаете, чтобы приложения Office отображались только на портале Office. Этот параметр находится в разделе **параметров пользователя** и называется **Пользователи могут видеть приложения Office 365 только на портале Office 365**.
 

Дополнительные сведения см. в статье [Скрытие приложения в пользовательском интерфейсе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Простой вход в приложения, поддерживающие единый вход с помощью пароля, непосредственно из URL-адреса приложения 

**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.
 

Расширение браузера "Мои приложения" теперь доступно с помощью удобного инструмента, который дает возможность выполнять единый вход в "Мои приложения" через ярлык в вашем браузере. После установки появится значок в виде вафли в браузере, который предоставляет быстрый доступ к приложениям. Теперь пользователи могут воспользоваться следующими преимуществами:

- Выполнять прямой вход в приложения с единым входом на основе пароля на странице входа в приложение.
- Запускать любое приложение с помощью функции быстрого поиска.
- Использовать ярлыки для недавно использовавшихся приложений из расширения.
- Расширение доступно для браузеров Edge, Chrome и Firefox.
 
Дополнительные сведения см. в разделе [Расширение защищенного входа на страницу "Мои приложения"](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Интерфейс администрирования Azure AD на классическом портале Azure больше не поддерживается

**Тип.** Прекращение поддержки.   
**Категория службы.** Azure AD.  
**Возможности продукта.** Каталог.
 

Начиная с 8 января 2018 года интерфейс администрирования Azure AD на классическом портале Azure больше не поддерживается. Это произошло в связи с прекращением поддержки самого классического портала Azure. Забегая вперед, отметим, что следует использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) для задач администрирования Azure AD, выполняемых с помощью портала.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Начиная с 8 января 2018 года прекращена поддержка веб-портала.

**Тип.** Прекращение поддержки.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Каталог.
 

Начиная с 8 января 2018 года прекращена поддержка веб-портала PhoneFactor. Этот портал использовался для администрирования сервера MFA, но эти функции были перенесены на портал Azure по адресу portal.azure.com. 

Чтобы перейти к настройке MFA, выберите **Azure Active Directory \> Сервер MFA**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Неподдерживаемые отчеты Azure AD


**Тип.** Прекращение поддержки.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Управление жизненным циклом удостоверений.  


С выходом общедоступной версии новой консоли администрирования Azure Active Directory и новых API-интерфейсов, доступных для отчетов о действиях и безопасности, API-интерфейсы отчетов в конечной точке /reports были удалены 31 декабря 2017 года.


**Доступные возможности**

В рамках перехода на новую консоль администратора мы создали 2 новых программных интерфейса для извлечения журналов действий Azure AD. В новом наборе программных интерфейсов расширены функции фильтрации, сортировки, аудита и действий входа. Доступ к данным, ранее доступным в отчетах по безопасности, теперь можно получить с помощью API событий риска защиты удостоверений в Microsoft Graph.

Дополнительные сведения можно найти в разделе 

- [Приступая к работе с API отчетов Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>Декабрь 2017 г.
 

### <a name="terms-of-use-in-the-access-panel"></a>Условия использования на панели доступа

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 
Теперь можно перейти к панели доступа и просмотреть условия использования, которые были приняты ранее.

Выполните следующие действия.

1. Перейдите на [портал MyApps](https://myapps.microsoft.com) и войдите в систему.

2. В правом верхнем углу щелкните свое имя, а затем в списке выберите **Профиль**. 

3. В **профиле** выберите **Ознакомьтесь с условиями использования**. 

4. Здесь можно просмотреть принятые вами условия использования. 

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Новый вход в систему Azure AD

**Тип.** Новая функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Аутентификация пользователей.
 
Пользовательские интерфейсы системы идентификации пользователей Azure AD и Майкрософт были перепроектированы таким образом, чтобы они имели единый внешний вид. Кроме того, страница входа в Azure AD сначала собирает имя пользователя, а затем учетные данные на втором экране.

Дополнительные сведения см. в записи блога [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (Новый интерфейс входа в Azure AD теперь находится в общедоступной предварительной версии).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Меньше запросов для входа. Новая функция "оставаться в системе" для входа в Azure AD

**Тип.** Новая функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Аутентификация пользователей.
 
Флажок **Оставаться в системе** на странице входа в Azure AD был заменен новым запросом, который появляется после прохождения проверки подлинности. 

Если на такой запрос ответить **Да**, служба предоставит постоянный токен обновления. То же самое происходило при установке флажка **Оставаться в системе** в старой версии. Для федеративных клиентов этот запрос отображается после выполнения проверки подлинности в службе федерации.

Дополнительные сведения см. в записи блога [Fewer login prompts: The new “Keep me signed in” experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Меньшее число запросов пароля. Новая функция "Оставаться в системе" в Azure AD). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Добавление конфигурации для требования развертывания условий использования перед их принятием

**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.
 
Функция для администраторов, позволяющая требовать развертывания условий использования перед их принятием пользователем.

**Включите** или **отключите** параметр "Требовать, чтобы пользователи развернули условия использования". Если выбран вариант **Вкл.**, чтобы принять соглашение, пользователям сначала нужно просмотреть условия.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Активация только соответствующих назначений ролей

**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Вы можете использовать ограниченную активацию для активации подходящих назначений ролей ресурсов Azure с меньшей автономией, чем исходные настройки по умолчанию. Предположим, вы назначены владельцем подписки в своем клиенте. С помощью ограниченной активации вы можете активировать роль владельца для максимум пяти ресурсов, содержащихся в рамках подписки (например, группы ресурсов и виртуальные машины). Ограничение активации может снизить вероятность выполнения нежелательных изменений в критических ресурсах Azure.

Дополнительные сведения см. в статье [Что такое Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Новые федеративные приложения в коллекции приложений Azure AD

**Тип.** Новая функция.  
**Категория службы.** Корпоративные приложения.  
**Возможности продукта.** Интеграция с решениями сторонних производителей.
 
В декабре 2017 года в коллекцию приложений были добавлены следующие новые приложения с поддержкой федерации:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Полный обзор всех доступных руководств см. в статье [Интеграция приложений SaaS с Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Рабочий процесс утверждения ролей каталога Azure AD

**Тип.** Измененная функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Рабочий процесс утверждения ролей каталога Azure AD доступен в общедоступной версии.

С помощью рабочего процесса утверждения администраторы привилегированной роли могут требовать от кандидатов на участников роли запрашивать активацию привилегированной роли, прежде чем они смогут ее использовать. Многим пользователям и группам могут быть делегированы полномочия на утверждение. Кандидаты на участников роли получат уведомления, когда они будут утверждены и их роль будет активирована.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Сквозная проверка подлинности. Поддержка Skype для бизнеса.

**Тип.** Измененная функция.  
**Категория службы.** Проверки подлинности (имена входа).  
**Возможности продукта.** Аутентификация пользователей.


Сквозная проверка подлинности теперь поддерживает вход пользователей в клиентские приложения Skype для бизнеса, которые поддерживают современную проверку подлинности, включая сетевые и гибридные топологии. 

Дополнительные сведения см. в статье [Топологии Skype для бизнеса, поддерживаемые современной проверкой подлинности](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Обновления Azure AD Privileged Identity Management для Azure RBAC (предварительная версия)

**Тип.** Измененная функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.
 
Теперь, когда вышло обновление общедоступной предварительной версии Azure AD Privileged Identity Management (PIM) для управления доступом на основе ролей (RBAC) Azure, вы можете:

* Использовать Just Enough Administration (JEA).
* Требовать утверждения для активации ролей ресурсов.
* Планировать активацию ролей в будущем, которая требует одобрения как для ролей Azure AD, так и для ролей Azure RBAC.

 
Дополнительные сведения см. в статье [PIM для ресурсов Azure (предварительная версия)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>Ноябрь 2017 г.
 
### <a name="access-control-service-retirement"></a>Прекращение поддержки службы контроля доступа



**Тип.** Планирование изменений.  
**Категория службы.** Служба проверки доступа.  
**Возможность продукта.** Служба контроля доступа. 


 Использование службы контроля доступа Azure Active Directory (также называется просто службой контроля доступа) будет прекращено в конце 2018 г. Дополнительные сведения, включая подробное расписание и общее руководство по переходу, будут предоставлены в ближайшие недели. Вы можете оставить на этой странице комментарии с любыми вопросами, касающимися службы контроля доступа, и член нашей команды ответит вам.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Предоставление доступа через браузер к управляемому браузеру Intune 


**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.




Вы можете предоставлять доступ через браузер к Office 365 и другим облачным приложениям, подключенным к Azure AD, используя Intune Managed Browser в качестве утвержденного приложения. 

Теперь можно настраивать следующее условие для условного доступа на основе приложений.

**Клиентские приложения.** Браузер.

**Каков эффект изменения?**

В настоящее время при использовании этого условия доступ заблокирован. Если доступна предварительная версия, для всех операций доступа потребуется приложение управляемого браузера. 

Ищите сведения об этой возможности в предстоящих записях в блогах и заметках о выпуске. 

Дополнительные сведения см. в статье [Условный доступ в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для условного доступа на основе приложения Azure AD

 
**Тип.** Планирование изменений.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.




Следующие приложения будут добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/);
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is).


Дополнительные сведения можно найти в разделе 

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Поддержка условий использования для нескольких языков



**Тип.** Новая функция.    
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.





Теперь администраторы могут создавать новые условия использования, содержащие несколько документов в формате PDF. Вы можете отметить эти документы соответствующими языками. Пользователям будут показаны PDF-файлы на том языке, который соответствует их предпочтениям. Если документа на их языке нет, отображается язык по умолчанию.


---
 

### <a name="real-time-password-writeback-client-status"></a>Состояние обратной записи паролей клиента в реальном времени



**Тип.** Новая функция.  
**Категория службы.** Самостоятельный сброс пароля.  
**Возможности продукта.** Аутентификация пользователей.


 

Теперь вы можете просмотреть состояние обратной записи паролей клиента в локальной среде. Этот параметр доступен в разделе **Интеграция с локальной средой** на странице [Сброс пароля](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Если возникли проблемы с подключением к клиенту обратной записи в локальной среде, появится сообщение об ошибке, содержащее следующую информацию:

- сведения о том, почему не удается подключиться к клиенту обратной записи в локальной среде;
- ссылка на документацию, которая помогает устранить проблему. 


Дополнительные сведения см. в разделе [Интеграция с локальной средой](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Условный доступ на основе приложений Azure AD 



 
**Тип.** Новая функция.  
**Категория службы.** Azure AD.  
**Возможности продукта.** Безопасность и защита удостоверений.





Теперь с помощью [условного доступа на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) вы можете предоставить доступ к Office 365 и другим облачным приложениям, подключенным к Azure AD, [утвержденным клиентским приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement), поддерживающим политики защиты приложений Intune. Политики защиты приложений Intune используются для настройки и защиты данных компании в этих клиентских приложениях.

Объединив политики условного доступа [на основе приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) и [на основе устройства](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications), вы сможете защитить данные корпоративных и личных устройств.

Следующие условия и элементы управления теперь доступны для использования с условным доступом на основе приложения:

**Условие поддерживаемой платформы**

- iOS
- Android

**Условие клиентских приложений**

- Мобильные приложения и настольные клиенты

**Контроль доступа**

- Требование утвержденного клиентского приложения


Дополнительные сведения см. в статье [Условный доступ на основе приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Управление устройствами Azure AD на портале Azure



**Тип.** Новая функция.  
**Категория службы.** Регистрация устройств и управление ими.  
**Возможности продукта.** Безопасность и защита удостоверений.

 



Теперь все устройства, подключенные к Azure AD, и действия, связанные с устройствами, размещены в одном месте. На портале Azure доступен новый интерфейс администрирования для управления удостоверениями и параметрами устройств. В этом выпуске вы можете:

- просматривать все устройства, доступные для условного доступа в Azure AD;
- просматривать свойства, включая гибридные устройства, присоединенные к Azure AD;
- находить ключи BitLocker для устройств, присоединенных к Azure AD, чтобы управлять устройствами с помощью Intune и т. д.;
- управлять параметрами, связанными с устройствами Azure AD.

Дополнительные сведения см. в статье [Управление устройствами с помощью портала Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Поддержка macOS в качестве платформы устройства для условного доступа Azure AD 



**Тип.** Новая функция.    
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений. 
 

Теперь вы можете включить (или исключить) macOS в качестве платформы устройств в политике условного доступа Azure AD. Добавив macOS в качестве поддерживаемой платформы устройства, вы можете:

- **Регистрировать устройства macOS и управлять ими в Intune.** Подобно другим платформам, таким как iOS и Android, приложение корпоративного портала доступно для единой регистрации устройств macOS. Вы можете использовать новое приложение корпоративного портала для macOS, чтобы зарегистрировать устройство в Intune и Azure AD.
- **Гарантировать соблюдение устройствами macOS политик соответствия вашей организации, определенных в Intune.** В Intune на портале Azure вы можете настроить соответствующие политики для устройств macOS. 
- **Предоставлять доступ к приложениям Azure AD только совместимым устройствам macOS.** При создании политики условного доступа macOS доступен как отдельный вариант платформы устройства. Теперь можно создавать политики условного доступа для macOS для целевого приложения, настроенного в Azure.

Дополнительные сведения можно найти в разделе 

- [Создание политики соответствия устройств для устройств macOS (предварительная версия) при помощи Intune](https://aka.ms/macoscompliancepolicy)
- [Условный доступ в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Расширение NPS для Многофакторной идентификации Azure 


**Тип.** Новая функция.    
**Категория службы.** Многофакторная идентификация.  
**Возможности продукта.** Аутентификация пользователей.




Расширение сервера политики сети (NPS) для Многофакторной идентификации Azure (Azure MFA) позволяет добавить многофакторную облачную проверку подлинности в существующую инфраструктуру аутентификации с помощью существующих серверов. Расширение NPS позволяет добавить телефонный звонок, текстовое сообщение или запрос на подтверждение в мобильном приложении в существующую последовательность аутентификации. Можно обойтись без установки, настройки и поддержания новых серверов. 

Это расширение было создано для организаций, которым необходимо защитить VPN-подключения, не развертывая сервер Многофакторной идентификации Azure. Расширение NPS выполняет функции адаптера между RADIUS и облачной службой Azure MFA, предоставляя второй фактор проверки подлинности для федеративных или синхронизированных пользователей.


Дополнительные сведения см. в статье [Интеграция существующей инфраструктуры NPS с Многофакторной идентификацией Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Восстановление или окончательное удаление удаленных пользователей


**Тип.** Новая функция.    
**Категория службы.** Управление пользователями.  
**Возможности продукта.** Каталог. 



Теперь в центре администрирования Azure AD вы можете:

- Восстановить удаленного пользователя. 
- Окончательно удалить пользователя.


**Для этого:**

1. В центре администрирования Azure AD в разделе **Управление** выберите [Все пользователи](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All). 

2. В списке **Показать** выберите **Недавно удаленные пользователи**. 

3. Выберите одного или нескольких недавно удаленных пользователей, а затем восстановите их или удалите без возможности восстановления.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Новые утвержденные клиентские приложения для условного доступа на основе приложения Azure AD

 
**Тип.** Измененная функция.  
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.


Следующие приложения добавлены в список [утвержденных клиентских приложений](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Планировщик (Майкрософт);
- Azure Information Protection 


Дополнительные сведения можно найти в разделе 

- [Требование утвержденного клиентского приложения](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Условный доступ на основе приложений Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Возможность выбора между элементами управления в политике условного доступа 


**Тип.** Измененная функция.    
**Категория службы.** Условный доступ.  
**Возможности продукта.** Безопасность и защита удостоверений.

 
Теперь доступна возможность выбора (необходимо выбрать один из элементов управления) элементов управления условного доступа. Эта функция позволяет создавать политики со значением "или" между элементами управления доступом. Например, используя эту функцию, можно создать политику, в соответствии с которой пользователь должен выполнить вход в систему с помощью многофакторной проверки подлинности "или" на соответствующем устройстве.

Дополнительные сведения см. в статье [Элементы управления условным доступом в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Агрегирование событий риска в реальном времени


**Тип.** Измененная функция.    
**Категория службы.** Защита идентификации.  
**Возможности продукта.** Безопасность и защита удостоверений.


В службе защиты идентификации Azure AD все события риска в реальном времени, возникшие на одном IP-адресе в определенный день, теперь агрегируются для каждого типа событий риска. Это изменение ограничивает объем событий риска, не влияя на безопасность пользователя.

Функция базового обнаружения в реальном времени работает каждый раз, когда пользователь выполняет вход. Если вы настроили для политики безопасности входа использование многофакторной проверки подлинности или блокировку доступа, обнаружение по-прежнему будет запускаться во время каждого входа, представляющего риск.

 
---
 




## <a name="october-2017"></a>Октябрь 2017 г.


### <a name="deprecate-azure-ad-reports"></a>Неподдерживаемые отчеты Azure AD


**Тип.** Планирование изменений.  
**Категория службы.** Отчеты.  
**Возможности продукта.** Управление жизненным циклом удостоверений.  



Портал Azure предлагает:

- новую консоль администрирования Azure AD;
- новые программные интерфейсы (API) для отчетов по действиям и безопасности.
 
Благодаря этим новым возможностям API отчетов на конечной точке /reports были выведены из эксплуатации 10 декабря 2017 г. 

---

### <a name="automatic-sign-in-field-detection"></a>Автоматическое определение полей входа в систему


**Тип.** Исправление.   
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.  



Azure AD поддерживает автоматическое определение поля входа для приложений, преобразовывающих для просмотра поле имени пользователя и пароля HTML. Эти шаги описаны в разделе [Определение полей входа в систему для приложения вручную](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Эту возможность можно найти путем добавления приложения *не из коллекции* на страницу **корпоративных приложений** на [портале Azure](http://aad.portal.azure.com). Кроме того, можно настроить режим **единого входа** в это новое приложение с помощью **единого входа по паролю** путем введения URL-адреса и сохранения страницы.
 
Из-за сбоя в службе эта функциональность была временно отключена. Сейчас эта проблема устранена и автоматическое обнаружение поля входа снова доступно.

---

### <a name="new-multi-factor-authentication-features"></a>Функции многофакторной проверки подлинности


**Тип.** Новая функция.  
**Категория службы.** Многофакторная проверка подлинности.  
**Возможности продукта.** Безопасность и защита удостоверений.  



Многофакторная проверка подлинности (MFA) является важной частью процесса защиты организации. Чтобы сделать учетные данные более адаптивными, а работу — простой, были добавлены следующие возможности: 

- Влияние интеграции многофакторной аутентификации на отчет о входе в Azure AD, включая программный доступ к результатам многофакторной аутентификации.
- Более тесная интеграция конфигурации многофакторной проверки подлинности с конфигурацией Azure AD на портале Azure.

В общедоступной предварительной версии управление и составление отчетов многофакторной проверки подлинности являются неотъемлемой частью настройки основных компонентов Azure AD. Теперь вы можете управлять функциональными возможностями портала управления многофакторной проверки подлинности в Azure AD.

Дополнительные сведения см. в статье [Справочник по созданию отчетов многофакторной проверки подлинности на портале Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Условия использования



**Тип.** Новая функция.  
**Категория службы.** Условия использования.  
**Возможности продукта.** Соответствие требованиям.  



Вы можете использовать условия использования Azure AD для предоставления пользователям такой информации, как соответствующие заявления об отказе для юридических требований и требования к соответствию.

Условия использования Azure AD можно применять в следующих сценариях:

- общие условия использования для всех пользователей в вашей организации;
- определенные условия использования на основе атрибутов пользователя (например, для медсестер и врачей или местных и международных сотрудников, что выполняется с помощью динамических групп);
- определенные условия использования для доступа к важным для бизнеса приложениям, например Salesforce.

Дополнительные сведения см. в статье [Функция "Условия использования Azure Active Directory" (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Улучшение управления привилегированными пользователями


**Тип.** Новая функция.  
**Категория службы.** Управление привилегированными пользователями.  
**Возможности продукта.** Управление привилегированными пользователями.  


С помощью службы Azure AD Privileged Identity Management (PIM) можно администрировать, контролировать и отслеживать доступ к ресурсам Azure (предварительная версия) в пределах организации:

- Подписки
- Группы ресурсов
- Виртуальные машины 

Все ресурсы на портале Azure, который использует функции Azure RBAC, могут воспользоваться всеми преимуществами управления безопасностью и жизненным циклом, которые может предложить Azure AD Privileged Identity Management.

Дополнительные сведения см. в статье [PIM для ресурсов Azure (предварительная версия)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Проверки доступа


**Тип.** Новая функция.  
**Категория службы.** Проверки доступа.  
**Возможности продукта.** Соответствие требованиям.  



Проверки доступа (предварительная версия) позволяют организациям эффективно управлять членством в группе и доступом к корпоративным приложениям. 

- Проверки доступа позволяют повторно оценивать, действительно ли гостевым пользователям нужен доступ к приложениям и членство в группах. Сведения, предоставляемые проверками доступа, позволяют рецензентам решать, нужен ли гостевым пользователям постоянный доступ.
- С их помощью можно повторно подтвердить доступ сотрудников к приложениям и их членство в группах.

Можно собирать элементы управления проверки доступа в программы, соответствующие вашей организации, чтобы отслеживать доступ для приложений, чувствительных к риску или нарушениям соответствия.

Дополнительные сведения см. в статье [Проверки доступа Azure AD (предварительная версия)](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Скрытие сторонних приложений в разделе "Мои приложения" и от средства запуска приложения Office 365



**Тип.** Новая функция.  
**Категория службы.** "Мои приложения".  
**Возможности продукта.** Единый вход.  



Теперь можно эффективнее управлять приложениями, которые отображаются на пользовательском портале, с помощью нового свойства **Скрыть приложение**. Это помогает в случаях, когда плитки приложений отображаются для серверных служб или плиток-дубликатов и перегружают средства запуска пользовательских приложений. Переключатель находится в разделе **Свойства** в стороннем приложении. Он называется **Visible to user?** (Видимый для пользователя?). Скрыть приложение можно также программным способом с помощью PowerShell. 

Дополнительные сведения см. в статье [Скрытие приложения в пользовательском интерфейсе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Доступные возможности**

 В рамках перехода на новую консоль администратора доступны два новых программных интерфейса для извлечения доступных журналов действий Azure AD. В новом наборе программных интерфейсов расширены функции фильтрации, сортировки, аудита и действий входа. Доступ к данным, ранее доступным в отчетах по безопасности, теперь можно получить с помощью API событий риска защиты удостоверений в Microsoft Graph.


## <a name="september-2017"></a>Сентябрь 2017 г.

### <a name="hotfix-for-identity-manager"></a>Исправления для Identity Manager


**Тип.** Измененная функция.  
**Категория службы.** Identity Manager.  
**Возможности продукта.** Управление жизненным циклом удостоверений.  



Накопительный пакет исправлений (сборка 4.4.1642.0) для Identity Manager 2016 с пакетом обновления 1 стал доступен с 25 сентября 2017 года. Этот накопительный пакет исправлений:

- устраняет проблемы и добавляет улучшения;
- является накопительным обновлением, которое заменяет все обновления сборки Identity Manager 2016 с пакетом обновления 1 от Identity Manager 2016 версии 4.4.1459.0; 
- требует наличия сборки Identity Manager 2016 версии 4.4.1302.0. 

Дополнительные сведения см. в статье [Доступен накопительный пакет исправлений (сборка 4.4.1642.0) для пакета обновления 1 для Microsoft Identity Manager 2016](https://support.microsoft.com/help/4021562). 

---
