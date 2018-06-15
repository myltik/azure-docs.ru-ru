---
title: Указатель статей по управлению приложениями в Azure Active Directory | Microsoft Azure
description: Узнайте, как настроить срок действия сертификатов федерации, а также обновлять сертификаты с истекающим сроком действия.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ed1179663823bc5a16f3ef348a649731814cdd5a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293098"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Указатель статей по управлению приложениями в Azure Active Directory
В этой статье представлен полный список всех статей о различных функциях приложений в Azure Active Directory (Azure AD).

Здесь приведено краткое описание каждой основной функции, а также рекомендованные статьи в зависимости от искомых сведений.

## <a name="overview-articles"></a>Обзор статей
Статьи в этом разделе подходят в качестве отправных точек для тех, кому необходимо краткое описание функций управления приложениями в Azure AD.

| Путеводитель по статьям |  |
|:---:| --- |
| Введение в проблемы управления приложениями, решаемые с помощью Azure AD |[Управление приложениями с помощью Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| Обзор различных функций в Azure AD, связанных с включением единого входа, определением пользователей, у которых есть доступ к приложениям, и способов запуска приложений |[Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md) |
| Обзор различных этапов интеграции приложений в Azure AD |[Интеграция Azure Active Directory с приложениями](manage-apps/plan-an-application-integration.md)<br /><br />[Включение единого входа для приложений SaaS](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Управление доступом к приложениям](manage-apps/what-is-access-management.md) |
| Технические сведения о представлении приложений в Azure AD |[Как и почему приложения добавляются в Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Статьи по устранению неполадок
В этом разделе даются ссылки на соответствующие руководства по устранению неполадок. В остальной части страницы вы найдете подробное описание каждой области функций.

| Область функций |  |
|:---:| --- |
| Федеративный единый вход |[Отладка единого входа на основе SAML в приложения в Azure Active Directory](active-directory-saml-debugging.md) |
| Единый вход на основе пароля |[Устранение неполадок, связанных с расширением панели доступа для Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Прокси приложения |[Устранение неполадок прокси-сервера приложений](manage-apps/application-proxy-troubleshoot.md) |
| Единый вход между локальным AD и Azure AD |[Устранение неполадок при синхронизации хэшированных паролей](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Устранение неполадок обратной записи паролей](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Динамическое членство в группах |[Устранение неполадок динамического членства в группах](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Единый вход (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Федеративный единый вход: вход в несколько приложений с помощью одного удостоверения
Благодаря функции единого входа пользователи могут получать доступ к различным приложениям и службам, используя всего один набор учетных данных. Федеративный единый вход — это один из методов единого входа. Когда пользователи предпринимают попытку войти в федеративные приложения, они перенаправляются на официальную страницу входа их организации в Azure Active Directory, а затем после успешной проверки подлинности перенаправляются обратно к приложению.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о федеративном и других способах входа |[Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md) |
| Тысячи приложений SaaS, предварительно интегрированных с Azure AD, для которых можно с легкостью настроить единый вход |[Начало работы с коллекцией приложений Azure AD](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Полный список предварительно интегрированных приложений, поддерживающих федерацию](active-directory-saas-tutorial-list.md)<br /><br />[Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md) |
| Более 150 руководств по настройке единого входа для таких приложений, как [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md) и многих других |[Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Настройка конфигурации единого входа вручную |[Настройка федеративного единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Настройка утверждений, выпущенных в маркере SAML для предварительно интегрированных приложений](active-directory-saml-claims-customization.md) |
| Руководство по устранению неполадок для федеративных приложений, использующих протокол SAML |[Отладка единого входа на основе SAML в приложения в Azure Active Directory](active-directory-saml-debugging.md) |
| Настройка срока действия сертификата приложения и способы обновления сертификатов |[Управление сертификатами для федеративного единого входа в Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

Функция федеративного единого входа доступна для всех выпусков Azure AD, и ее можно использовать для 10 приложений на пользователя. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) поддерживает неограниченное количество приложений. Если ваша организация использует [Azure AD уровня "Базовый"](https://azure.microsoft.com/pricing/details/active-directory/) или [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/), то вы можете [использовать группы для предоставления доступа к федеративным приложениям](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Единый вход на основе пароля: совместное использование учетных записей и единый вход для нефедеративных приложений
Чтобы включить единый вход для приложений, которые не поддерживают федерацию, Azure AD предоставляет функции управления паролями. Они обеспечивают безопасное хранение паролей для приложений SaaS и автоматический вход в эти приложения. Используя эти функции, можно с легкостью задать необходимые учетные данные для новых учетных записей и совместно с другими использовать общие учетные записи. Пользователям не обязательно знать учетные данные учетных записей, к которым им предоставлен доступ.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о принципах работы единого входа на основе паролей и краткий технический обзор |[Единый вход на основе пароля](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| Сводка сценариев совместного использования учетных записей и способов решения этих проблем с помощью Azure AD |[Совместное использование учетных записей в Azure AD](active-directory-sharing-accounts.md) |
| Автоматическое изменение пароля для определенных приложений с определенной периодичностью |[Automated Password Rollover (preview) (Автоматическая смена пароля (предварительная версия))](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Руководства по развертыванию и устранению неполадок для расширения управления паролями Azure AD с использованием Internet Explorer |[Развертывание расширения панели доступа для Internet Explorer с помощью групповой политики](active-directory-saas-ie-group-policy.md)<br /><br />[Устранение неполадок, связанных с расширением панели доступа для Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Функция единого входа на основе паролей доступна для всех выпусков Azure AD, и ее можно использовать для 10 приложений на пользователя. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) поддерживает неограниченное количество приложений. Если ваша организация использует [Azure AD уровня "Базовый"](https://azure.microsoft.com/pricing/details/active-directory/) или [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/), то вы можете [использовать группы для предоставления доступа к приложениям](#managing-access-to-applications). Автоматическая смена пароля — это функция [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Прокси приложения: единый вход в локальные приложения и удаленный доступ к ним
Если вам необходимо предоставить безопасный удаленный доступ к приложениям для пользователей и устройств за пределами частной сети, для этого можно использовать прокси приложения Azure AD.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о прокси приложения Azure AD и принципах его работы |[Предоставление безопасного удаленного доступа к локальному приложению](manage-apps/application-proxy.md) |
| Руководства по настройке прокси приложения и публикации первого приложения |[Настройка прокси приложения Azure AD](manage-apps/application-proxy-enable.md)<br /><br />[Автоматическая установка соединителя прокси приложения](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Публикация приложений с помощью прокси приложения](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Работа с пользовательскими доменами в прокси приложения Azure AD](manage-apps/application-proxy-configure-custom-domain.md) |
| Включение единого входа и условного доступа для приложений, опубликованных с помощью прокси приложения |[Единый вход с помощью прокси приложения](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Работа с условным доступом](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Рекомендации по использованию прокси приложения в определенных сценариях |[Включение собственных клиентских приложений для взаимодействия с приложениями прокси](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Работа с приложениями, поддерживающими утверждения, в прокси приложения](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Публикация приложений в отдельных сетях и расположениях с помощью групп соединителей](manage-apps/application-proxy-connector-groups.md) |
| Руководство по устранению неполадок прокси приложения |[Устранение неполадок прокси-сервера приложений](manage-apps/application-proxy-troubleshoot.md) |

Прокси приложения доступно для всех выпусков Azure AD, и его можно использовать для 10 приложений на пользователя. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) поддерживает неограниченное количество приложений. Если ваша организация использует [Azure AD уровня "Базовый"](https://azure.microsoft.com/pricing/details/active-directory/) или [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/), то вы можете [использовать группы для предоставления доступа к приложениям](#managing-access-to-applications).

Возможно, вас также заинтересуют [доменные службы Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), с помощью которых можно переносить локальные приложения в Azure, поддерживая их методы идентификации.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Включение единого входа между Azure AD и локальной службой AD
Если ваша организация использует Windows Server Active Directory локально, а Azure Active Directory — в облаке, скорее всего, вам потребуется включить единый вход между двумя этими системами. В Azure AD Connect (средство, с помощью которого выполняется интеграция этих двух систем) представлены несколько вариантов для настройки единого входа: установка федерации с помощью ADFS или другого поставщика федерации или включение синхронизации паролей.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о параметрах единого входа, представленных в Azure AD Connect, а также сведения об управлении гибридными средами |[Параметры входа в Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Общие рекомендации по управлению средами с помощью локальной службы Active Directory и Azure Active Directory |[Рекомендации по разработке архитектуры гибридной идентификации в Azure AD](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md) |
| Руководство по реализации единого входа с помощью синхронизации паролей |[Реализация синхронизации паролей с помощью Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Устранение неполадок синхронизации паролей](https://support.microsoft.com/en-us/kb/2855271) |
| Руководство по реализации единого входа с помощью обратной записи паролей |[Приступая к работе с компонентами управления паролями](authentication/quickstart-sspr.md)<br /><br />[Устранение неполадок в обратной записи паролей](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Руководство по реализации единого входа с использованием сторонних поставщиков удостоверений |[Список совместимых сторонних поставщиков удостоверений, которых можно использовать для реализации единого входа](https://aka.ms/ssoproviders) |
| Каким образом пользователи Windows 10 могут получить преимущества единого входа с помощью присоединения к Azure AD |[Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-overview.md) |

Azure AD Connect доступно для [всех выпусков Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Функция самостоятельного сброса пароля Azure AD поддерживается в выпусках [Azure AD уровня "Базовый"](https://azure.microsoft.com/pricing/details/active-directory/) и [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/). Компонент обратной записи паролей в локальную службу AD — это функция [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Условный доступ: принудительное выполнение дополнительных требований к безопасности для приложений с высоким уровнем риска
После настройки единого входа для приложений и ресурсов можно дополнительно защитить важные приложения путем принудительного выполнения требований к безопасности при каждой операции входа. Например, Azure AD можно использовать, чтобы принудить пользователей проходить многофакторную проверку подлинности при каждом доступе к определенному приложению вне зависимости от того, поддерживает ли приложение эту функцию. Другой распространенный пример условного доступа — это требование, чтобы пользователи были подключены к доверенной сети организации для доступа к особо важным приложениям.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о возможностях условного доступа, предлагаемых в Azure AD, Office 365 и Intune |[Управление рисками с помощью условного доступа](active-directory-conditional-access-azure-portal.md) |
| Включение условного доступа для определенных типов ресурсов |[Условный доступ к приложениям SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Условный доступ к службам Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Условный доступ к локальным приложениям](active-directory-conditional-access-azure-portal.md)<br /><br />[Работа с условным доступом](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Регистрация устройств в Azure Active Directory для включения политики условного доступа на основе устройств |[Общие сведения о регистрации устройств Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Включение автоматической регистрации присоединенных к домену устройств Windows](active-directory-conditional-access-automatic-device-registration.md)<br />— [Действия для устройств Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Действия для устройств Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Использование приложения Microsoft Authenticator для двухфакторной проверки подлинности |[Начало работы с приложением Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

Условный доступ — это функция [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="apps--azure-ad"></a>Приложения и Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud App Discovery: поиск приложений SaaS, используемых в вашей организации
С помощью службы Cloud App Discovery ИТ-отделы могут узнать, какие приложения SaaS используются в организации. Эта служба позволяет оценить использование и популярность приложений. Таким образом ИТ-отделы смогут определить, какие приложения выгоднее всего контролировать и интегрировать с Azure AD.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о принципах работы Cloud App Discovery |[Поиск несанкционированных облачных приложений с Cloud App Discovery](manage-apps/cloud-app-discovery.md) |
| Подробные сведения о принципах работы Cloud App Discovery и ответы на вопросы о конфиденциальности |[Вопросы безопасности и конфиденциальности Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Часто задаваемые вопросы |[FAQ for Cloud App Discovery (Часто задаваемые вопросы о Cloud App Discovery)](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Руководства по развертыванию Cloud App Discovery |[Руководство по развертыванию групповой политики](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Руководство по развертыванию System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Параметры реестра Cloud App Discovery для прокси-служб](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Журнал изменений для обновлений агента Cloud App Discovery |[Change log (Журнал изменений)](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery — это функция [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Автоматическая подготовка учетных записей пользователей и ее отзыв в приложениях SaaS
Автоматизируйте создание, обслуживание и удаление удостоверений пользователей в таких приложениях SaaS, как Dropbox, Salesforce, ServiceNow и других. Сопоставляйте и синхронизируйте существующие удостоверения между Azure AD и приложениями SaaS, а также управляйте доступом путем автоматического отключения учетных записей в случае ухода пользователей из организации.

| Путеводитель по статьям |  |
|:---:| --- |
| Дополнительные сведения о принципах работы и ответы на часто задаваемые вопросы |[Автоматическая подготовка пользователей и ее отзыв для приложений SaaS](active-directory-saas-app-provisioning.md) |
| Настройка способов сопоставления сведений между Azure AD и приложением SaaS |[Настройка сопоставлений атрибутов](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Запись выражений для сопоставления атрибутов](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Включение автоматической подготовки в любом приложении, которое поддерживает протокол SCIM |[Автоматическая подготовка пользователей и групп из Azure Active Directory в приложениях с использованием SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) |
| Составление отчетов о подготовке пользователей и устранение неполадок |[Отчеты об автоматической подготовке пользователей](active-directory-saas-provisioning-reporting.md)<br><br>[Устранение неполадок при подготовке пользователей](active-directory-application-provisioning-content-map.md) |
| Ограничение количества пользователей, которые будут подготовлены для работы с приложением, на основе значений их атрибутов |[Подготовка приложений на основе атрибутов с использованием фильтров области](active-directory-saas-scoping-filters.md) |

Автоматическая подготовка пользователей доступна для всех выпусков Azure AD, и ее можно использовать для 10 приложений на пользователя. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) поддерживает неограниченное количество приложений. Если ваша организация использует [Azure AD уровня "Базовый"](https://azure.microsoft.com/pricing/details/active-directory/) или [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/), вы можете [использовать группы для управления отбором пользователей для подготовки](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Создание приложений, которые интегрируются с Azure AD
Если ваша организация разрабатывает и обслуживает бизнес-приложения или вы являетесь разработчиком приложений для клиентов, которые используют Azure Active Directory, следующие руководства помогут вам интегрировать приложения с Azure AD.

| Путеводитель по статьям |  |
|:---:| --- |
| Рекомендации для ИТ-специалистов и разработчиков приложений по интеграции приложений с Azure AD |[Руководство для ИТ-специалистов по разработке приложений для Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md) |
| Практическое руководство для поставщиков приложений по добавлению приложений в коллекцию приложений Azure AD |[Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md) |
| Управление доступом к разработанным приложениям с помощью Azure Active Directory |[Включение назначения пользователей для разработанных приложений](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Назначение пользователей для приложения](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Azure AD и приложения: назначение групп для приложения](active-directory-applications-guiding-developers-assigning-groups.md) |

Тех, кто разрабатывает потребительские приложения, может заинтересовать [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/). Благодаря этому решению вам не придется разрабатывать систему идентификации для управления пользователями. [Подробнее](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Управление доступом к приложениям
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Использование групп и функции самообслуживания для управления доступом к определенным приложениям
Для управления доступом к определенным ресурсам в Azure Active Directory можно задавать назначения и разрешения нужного уровня, используя группы. ИТ-специалисты могут включать функции самообслуживания, чтобы пользователи могли просто запросить разрешение при необходимости.

| Путеводитель по статьям |  |
|:---:| --- |
| Обзор функций управления доступом Azure AD |[Введение в управление доступом к приложениям](manage-apps/what-is-access-management.md)<br /><br />[Как работает управление доступом в Azure AD](active-directory-manage-groups.md)<br /><br />[Использование группы для управления доступом к приложениям SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Включение самостоятельного управления приложениями и группами |[Самостоятельное управление приложениями](active-directory-self-service-application-access.md)<br /><br />[Настройка Azure Active Directory для управления самостоятельным доступом к приложениям](active-directory-accessmanagement-self-service-group-management.md) |
| Указания по настройке групп в Azure AD |[Создание групп безопасности](active-directory-groups-create-azure-portal.md)<br /><br />[Назначение владельцев для группы](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Выделенные группы в Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md) |
| Использование динамических групп для автоматического заполнения участников группы с помощью правил членства на основе атрибутов |[Динамическое членство в группах: расширенные правила](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Устранение неполадок динамического членства в группах](active-directory-accessmanagement-troubleshooting.md) |

Управление доступом к приложениям на основе групп поддерживается в [Azure AD уровня "Базовый"](https://azure.microsoft.com/pricing/details/active-directory/) и [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/). Самостоятельное управление группами и приложениями, а также динамические группы — это функции [Azure AD уровня "Премиум"](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Совместная работа B2B: партнерский доступ к приложениям
Если ваша организация сотрудничает с другими компаниями, скорее всего, вам необходимо управлять партнерским доступом к корпоративным приложениям. Служба совместной работы Azure Active Directory B2B предоставляет простой и безопасный способ совместного использования приложений с партнерами.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о различных функциях Azure AD, с помощью которых можно управлять внешними пользователями, такими как партнеры, клиенты и т. д. |[Сравнение возможностей управления внешними удостоверениями с помощью Azure Active Directory](active-directory-b2b-compare-external-identities.md) |
| Общие сведения о службе совместной работы B2B и указания по началу работы с ней. |[Простая и надежная облачная интеграция партнеров с Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Служба Azure Active Directory B2B Collaboration](active-directory-b2b-collaboration-overview.md) |
| Подробные сведения о службе совместной работы Azure AD B2B и о способах ее использования |[Принципы работы службы совместной работы B2B](active-directory-b2b-how-it-works.md)<br /><br />[Текущие ограничения службы совместной работы Azure AD B2B](active-directory-b2b-current-limitations.md)<br /><br />[Подробное пошаговое руководство по использованию службы совместной работы Azure Active Directory (Azure AD) B2B](active-directory-b2b-detailed-walkthrough.md) |
| Справочные статьи, содержащие технические сведения о том, как работает служба совместной работы Azure AD B2B |[Формат CSV-файла для добавления пользователей партнера](active-directory-b2b-references-csv-file-format.md)<br /><br />[Атрибуты пользователя, затронутые службой совместной работы Azure AD B2B](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Формат внешнего пользовательского токена для предварительной версии службы Azure Active Directory B2B Collaboration](active-directory-b2b-references-external-user-token-format.md) |

Служба совместной работы B2B в настоящее время доступна для [всех выпусков Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Панель доступа: портал для доступа к приложениям и функциям самообслуживания
На панели доступа Azure AD конечные пользователи могут запускать приложения и получать доступ к функциям самообслуживания, которые позволяют управлять приложениями и членством в группах. Помимо панели доступа в списке ниже приведены и другие варианты получения доступа к приложениям с поддержкой единого входа.

| Путеводитель по статьям |  |
|:---:| --- |
| Сравнение различных параметров развертывания приложений с поддержкой единого входа для пользователей |[Deploying Azure AD Integrated Applications to Users (Развертывание интегрированных приложений Azure AD для пользователей)](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| Обзор панели доступа и эквивалентной мобильной версии MyApps |[Общие сведения о панели доступа и MyApps](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Получение доступа к приложениям Azure AD на веб-сайте Office 365 |[Средство запуска приложений Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Получение доступа к приложениям Azure AD с мобильного приложения Intune Managed Browser |[Управление доступом в Интернет с помощью политик управляемого браузера в Microsoft Intune](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Получение доступа к приложениям Azure AD с использованием прямых ссылок для запуска единого входа |[Ссылки для прямого входа в федеративные приложения, приложения с паролем или существующие приложения](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Панель доступа доступна для [всех выпусков Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Отчеты: простой аудит изменений доступа к приложениям и отслеживание операций входа в приложения
В службе Azure Active Directory можно настроить получение отчетов и оповещений для отслеживания доступа сотрудников организации к приложениям. Вы можете получать оповещения об аномальных операциях входа в приложение, а также отслеживать время и причину изменения доступа пользователей к приложению.

| Путеводитель по статьям |  |
|:---:| --- |
| Общие сведения о функциях отчетности в Azure Active Directory |[Приступая к работе со средством создания отчетов Azure Active Directory](active-directory-reporting-getting-started.md) |
| Отслеживание операций входа и использования приложений пользователями |[Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md) |
| Отслеживание изменений в списке пользователей, которые могут иметь доступ к определенным приложениям |[События отчета аудита Azure Active Directory](active-directory-reporting-audit-events.md) |
| Экспорт данных из этих отчетов в предпочитаемое средство с помощью API отчетов |[Приступая к работе с API отчетов Azure AD](active-directory-reporting-api-getting-started.md) |

Чтобы просмотреть, какие отчеты включены в различные выпуски Azure Active Directory, [щелкните здесь](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>См. также
[Что такое Microsoft Azure Active Directory](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Доменные службы Azure Active Directory](https://azure.microsoft.com/services/active-directory-ds/)

[Многофакторная идентификация Azure](https://azure.microsoft.com/services/multi-factor-authentication/)
