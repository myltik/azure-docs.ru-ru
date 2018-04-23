---
title: 'Сборник тренировочных заданий по подтверждению концепции для Azure Active Directory: стандартные блоки | Документация Майкрософт'
description: Ознакомление со сценариями управления удостоверениями и доступом и их реализация
services: active-directory
keywords: azure active directory, сборник тренировочных заданий, подтверждение концепции, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: d78b6934e276c53b65f3c171ed980f86458c3a93
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Сборник тренировочных заданий по подтверждению концепции для Azure Active Directory: стандартные блоки

## <a name="catalog-of-roles"></a>Каталог ролей

| Роль | ОПИСАНИЕ | Обязанности по подтверждению концепции |
| --- | --- | --- |
| **Группа по архитектуре удостоверений и разработке** | Обычно эта группа отвечает за проектирование решения, реализацию прототипов, проведение утверждений и передачу решения в работу. | Эти люди предоставляют среды и оценивают различные сценарии с точки зрения управляемости. |
| **Группа по работе с локальными удостоверениями** | Управляет различными источниками удостоверений в локальной среде: лесами Active Directory, каталогами LDAP, системами подбора кадров и поставщиками удостоверений федерации. | Предоставляет доступ к локальным ресурсам, необходимым для сценариев подтверждения концепции.<br/>Этих сотрудников следует вовлекать в работу как можно меньше.|
| **Технические владельцы приложений** | Технические владельцы различных облачных приложений и служб, которые будут интегрированы с Azure AD. | Предоставляют сведения о приложениях SaaS (потенциальные экземпляры для тестирования). |
| **Глобальный администратор Azure AD** | Управляет конфигурацией Azure AD. | Предоставляет учетные данные для настройки службы синхронизации. При подтверждении концепции обычно объединяется с группой архитектуры удостоверений, но в обычных условиях работает отдельно.|
| **Группа баз данных** | Владельцы инфраструктуры баз данных. | Предоставляют доступ к среде SQL (ADFS или Azure AD Connect) для подготовки к конкретному сценарию.<br/>Этих сотрудников следует вовлекать в работу как можно меньше. |
| **Группа по сетям** | Владельцы сетевой инфраструктуры. | Предоставляют требуемый доступ на уровне сети, чтобы обеспечить серверам синхронизации правильный доступ к источникам данных и облачным службам (правила брандмауэра, открытые порты, правила IPSec и т. д.) |
| **Группа безопасности:** | Определяет стратегии безопасности, анализирует отчеты о безопасности из различных источников и обрабатывает результаты. | Предоставляет целевые сценарии по оценке безопасности. |

## <a name="common-prerequisites-for-all-building-blocks"></a>Общие предварительные требования для всех стандартных блоков

Ниже приведены некоторые предварительные требования по любому подтверждению концепции для Azure AD Premium.

| Предварительные требования | Ресурсы |
| --- | --- |
| Определен клиент Azure AD с действительной подпиской Azure | [Как получить клиент Azure Active Directory](active-directory-howto-tenant.md)<br/>**Примечание.** Если у вас уже есть среда с лицензиями Azure AD Premium, вы можете получить подписку нулевой емкости по адресу https://aka.ms/accessaad <br/>Дополнительные сведения см. по ссылкам https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ и https://technet.microsoft.com/library/dn832618.aspx |
| Домены определены и проверены | [Добавление имени личного домена в Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Примечание.** Некоторые рабочие нагрузки, такие как Power BI, могли уже подготовить клиент Azure AD. Чтобы проверить, связан ли заданный домен с каким-либо клиентом, перейдите по адресу https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Если вы успешно получили ответ, значит домен уже назначен клиенту и может потребоваться перехват. В этом случае за дальнейшими указаниями обратитесь в корпорацию Майкрософт. Дополнительные сведения о вариантах перехвата см. в статье [Что такое самостоятельная регистрация для Azure?](active-directory-self-service-signup.md). |
| Включена пробная версия EMS или Azure AD Premium | [Бесплатная пробная версия Azure Active Directory Premium на один месяц](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Вы назначили лицензии Azure AD Premium или EMS пользователям подтверждения концепции | [Самостоятельное лицензирование и лицензирование пользователей в Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Учетные данные глобального администратора Azure AD | [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Необязательно, но настоятельно рекомендуется: параллельная лабораторная среда в качестве резерва | [Необходимые условия для Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Синхронизация каталога. Синхронизация хэша паролей (PHS): новая установка

Приблизительное время завершения: один час для менее чем 1000 пользователей подтверждения концепции.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Сервер для запуска Run Azure AD Connect | [Необходимые условия для Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Целевые пользователи подтверждения концепции в одном домене, одной группе безопасности и одном подразделении | [Выборочная установка Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Определяются функции Azure AD Connect, необходимые для подтверждения концепции | [Подключение Active Directory к Azure Active Directory — настройка функций синхронизации](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Наличие необходимых учетных данных для локальных и облачных сред  | [Azure AD Connect: учетные записи и разрешения](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Скачайте новейшую версию Azure AD Connect. | [Скачивание Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Установите Azure AD Connect самым простым способом: быстрая установка. <br/>1. Отфильтруйте до целевого подразделения, чтобы минимизировать время цикла синхронизации.<br/>2. Выберите целевой набор пользователей в локальной группе.<br/>3. Развертывание компонентов, необходимых для других тем подтверждения концепции. | [Azure AD Connect: выборочная установка. Фильтрация домена и подразделения](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: выборочная установка. Фильтрация на основе групп](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: интеграция локальных удостоверений с Azure Active Directory. Настройка функции синхронизации](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Откройте пользовательский интерфейс Azure AD Connect и просмотрите запущенные профили (импорт, синхронизация и экспорт). | [Синхронизация Azure AD Connect: планировщик](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Откройте [портал управления Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), перейдите в колонку "Все пользователи", добавьте столбец "Главный источник" и наблюдайте, как появляются пользователи, помеченные как поступившие из "Windows Server AD". | [Портал управления Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Рекомендации

1. Ознакомьтесь с замечаниями по безопасности для синхронизации хэша паролей [здесь](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Если синхронизация хэша паролей для пользователей пилотной рабочей среды совершенно неприемлема, рассмотрите следующие альтернативы:
   * Создайте тестовых пользователей в рабочем домене. Следите за тем, чтобы не синхронизировать никакие другие учетные записи.
   * Перейдите в среду UAT.
2.  Если вы хотите сохранить федерацию, следует понимать, с какими затратами сопряжено использование федеративного решения с локальным поставщиком удостоверений за пределами подтверждения концепции, и сопоставлять их с потенциальной выгодой:
    * Она находится на критическом пути, поэтому вам нужно предусмотреть высокую доступность.
    * Это локальная служба, для которой нужно планировать емкость.
    * Это локальная служба, которую нужно отслеживать, обслуживать и исправлять.

Дополнительные сведения: [Сведения об удостоверении Office 365 и Azure Active Directory — федеративное удостоверение](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Фирменная символика

Примерное время выполнения: 15 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Ресурсы (изображения, логотипы и т. д.). Для оптимальной визуализации убедитесь, что ресурсы имеют рекомендуемые размеры. | [Добавление фирменной символики организации на страницу входа в Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Необязательно: если в среде присутствует ADFS, обратитесь к нему для настройки веб-темы. | [Настройка входа для пользователя AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Клиентский компьютер для выполнения входа пользователей. |  |
| Необязательно: мобильные устройства для проверки взаимодействия. |  |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Перейдите на портал управления Azure AD. | [Портал управления Azure AD — фирменная символика организации](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Отправьте ресурсы для страницы входа (имиджевый логотип, небольшой логотип, метки и т. д.). Если имеется AD FS, согласуйте эти ресурсы со страницами входа ADFS (необязательно). | [Добавление фирменной символики компании на страницах входа и панели доступа: настраиваемые элементы](customize-branding.md) |
| Подождите несколько минут, чтобы изменения вступили в силу. |  |
| Выполните вход с учетными данными пользователя для подтверждения концепции по адресу https://myapps.microsoft.com |  |
| Оцените внешний вид и удобство использования в браузере. | [Добавление фирменной символики компании на страницах входа и панели доступа](customize-branding.md) |
| При необходимости проверьте внешний вид и удобство использования на других устройствах. |  |

### <a name="considerations"></a>Рекомендации

Если после настройки сохраняется старый внешний вид, очистите клиентский кэш браузера и повторите операцию.

## <a name="group-based-licensing"></a>Групповое лицензирование

Примерное время выполнения: 10 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Все пользователи подтверждения концепции входят в состав группы безопасности (облачной или локальной) | [Создание группы и добавление в нее пользователей в Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Перейдите в колонку лицензий на портале управления Azure AD. | [Портал управления Azure AD: лицензирование](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Назначьте лицензии группе безопасности с пользователями подтверждения концепции. | [Назначение лицензий группе пользователей в Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Рекомендации

При большом количестве проблем см. статью [Сценарии, ограничения и известные проблемы при использовании групп для управления лицензированием в Azure Active Directory](active-directory-licensing-group-advanced.md).

## <a name="saas-federated-sso-configuration"></a>Настройка федеративного единого входа SaaS

Примерное время выполнения: 60 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Доступна тестовая среда для приложения SaaS. В этом руководстве в качестве примера мы используем ServiceNow.<br/>Настоятельно рекомендуется использовать тестовый экземпляр, чтобы минимизировать взаимодействие с существующими данными и их сопоставлениями. | Перейдите по адресу https://developer.servicenow.com/app.do#!/home, чтобы начать процесс получения тестового экземпляра |
| Административный доступ к консоли управления ServiceNow. | [Руководство: интеграция Azure Active Directory с ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Целевой набор пользователей для назначения приложения. Рекомендуется использовать группу безопасности, содержащую пользователей подтверждения концепции. <br/>Если создать такую группу невозможно, назначьте пользователей непосредственно приложению для подтверждения концепции. | [Назначение пользователя или группы корпоративному приложению в Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Предоставьте общий доступ к учебнику всем субъектам из документации Майкрософт.  | [Руководство: интеграция Azure Active Directory с ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Проведите рабочее собрание и выполните инструкции из учебника вместе с каждым субъектом. | [Руководство: интеграция Azure Active Directory с ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Назначьте приложение группе, указанной в предварительных требованиях. Если в область подтверждения концепции входит условный доступ, вы можете вернуться позже, чтобы добавить Многофакторную идентификацию и аналогичные компоненты. <br/>Обратите внимание, что это запустит процесс подготовки (если он настроен). |  [Назначение пользователя или группы корпоративному приложению в Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Создание группы и добавление в нее пользователей в Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Используйте портал управления Azure AD, чтобы добавить приложение ServiceNow из коллекции.| [Портал управления Azure AD: корпоративные приложения](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Новые возможности управления корпоративными приложениями в Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| В колонке "Единый вход" приложения ServiceNow включите параметр "SAML-based Sign-on" (Вход на основе SAML). |  |
| Укажите в полях "URL-адрес для входа" и "Идентификатор" URL-адрес ServiceNow.<br/>Установите флажок "Сделать сертификат активным"<br/>и сохраните параметры. |  |
| Откройте колонку "Configure ServiceNow" (Настройка ServiceNow) в нижней части панели, чтобы просмотреть индивидуальные инструкции для настройки ServiceNow. |  |
| Следуйте инструкциям для настройки ServiceNow. |  |
| В колонке "Подготовка" приложения ServiceNow включите автоматическую подготовку. | [Управление подготовкой учетных записей пользователей для корпоративных приложений на новом портале Azure](active-directory-enterprise-apps-manage-provisioning.md) |
| Подождите несколько минут, пока подготовка закончится.  В это время можно просмотреть отчеты о подготовке. |  |
| Выполните вход по адресу https://myapps.microsoft.com/ как тестовый пользователь, обладающий доступом | [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md) |
| Щелкните элемент для только что созданного приложения. Подтвердите доступ. |  |
| При необходимости вы также можете просмотреть отчеты об использовании приложения. Обратите внимание, что имеется некоторая задержка, поэтому для отражения трафика в отчетах нужно немного подождать. | [Отчеты о действиях входа на портале Azure Active Directory: использование управляемых приложений](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Политики хранения отчетов Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Рекомендации

1. Предыдущий [учебник](active-directory-saas-servicenow-tutorial.md) ссылается на старый интерфейс управления Azure AD. Однако подтверждение концепции основано на интерфейсе [быстрого запуска](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away).
2. Если целевое приложение отсутствует в коллекции, можно использовать собственное приложение. Дополнительные сведения: [Новые возможности управления корпоративными приложениями в Azure Active Directory. Добавление пользовательских приложений из одного расположения](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Настройка единого входа SaaS с помощью пароля

Примерное время выполнения: 15 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Тестовая среда для приложений SaaS. Примером единого входа с паролем является HipChat и Twitter. Для любого другого приложения нужен точный URL-адрес страницы с HTML-формой входа. | [Twitter в Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat в Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Тестовые учетные записи для приложений. | [Регистрация в Twitter](https://twitter.com/signup?lang=en)<br/>[Бесплатная регистрация: HipChat](https://www.hipchat.com/sign_up) |
| Целевой набор пользователей для назначения приложения. Рекомендуется использовать группу безопасности, содержащую этих пользователей. | [Назначение пользователя или группы корпоративному приложению в Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Доступ локального администратора к компьютеру, чтобы развернуть расширение панели доступа для Internet Explorer, Chrome или Firefox. | [Расширение "Панель доступа" для Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Расширение "Панель доступа" для Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Расширение "Панель доступа" для Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Установите расширение браузера. | [Расширение "Панель доступа" для Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Расширение "Панель доступа" для Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Расширение "Панель доступа" для Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Настройте приложение из коллекции. | [Новые возможности управления корпоративными приложениями в Azure Active Directory: обновленная и усовершенствованная коллекция приложений](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Настройте единый вход с паролем. | [Управление параметрами единого входа для корпоративных приложений на новом портале Azure: вход на основе пароля](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Назначьте приложение группе, указанной в предварительных требованиях. | [Назначение пользователя или группы корпоративному приложению в Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Выполните вход по адресу https://myapps.microsoft.com/ как тестовый пользователь, обладающий доступом |  |
| Щелкните элемент для только что созданного приложения. | [Что такое панель доступа? Единый вход по паролю без предоставления идентификатора](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Укажите учетные данные приложения. | [Что такое панель доступа? Единый вход по паролю без предоставления идентификатора](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Закройте браузер и повторите вход. На этот раз пользователь должен получить возможность легкого доступа к приложению. |  |
| При необходимости вы также можете просмотреть отчеты об использовании приложения. Обратите внимание, что имеется некоторая задержка, поэтому для отражения трафика в отчетах нужно немного подождать. | [Отчеты о действиях входа на портале Azure Active Directory: использование управляемых приложений](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Политики хранения отчетов Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Рекомендации

Если целевое приложение отсутствует в коллекции, можно использовать собственное приложение. Дополнительные сведения: [Новые возможности управления корпоративными приложениями в Azure Active Directory. Добавление пользовательских приложений из одного расположения](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Не забывайте о следующих требованиях:
   * Приложение должно иметь известный URL-адрес входа.
   * Страница входа должна содержать HTML-форму с одним или несколькими текстовыми полями, которые расширения браузера могут заполнять автоматически. По меньше мере она должна содержать имя пользователя и пароль.

## <a name="saas-shared-accounts-configuration"></a>Настройка общих учетных записей SaaS.

Примерное время выполнения: 30 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Список целевых приложений и точные URL-адреса входа заранее. Например, можно использовать Twitter. | [Twitter в Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Регистрация в Twitter](https://twitter.com/signup?lang=en) |
| Общие учетные данные для этого приложения SaaS. | [Совместное использование учетных записей с помощью Azure AD](active-directory-sharing-accounts.md)<br/>[Автоматическая смена паролей Azure AD для Facebook, Twitter и LinkedIn перешла на этап предварительной версии! - Блог о корпоративной мобильности и безопасности] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Учетные данные по меньшей мере двух членов группы, которые будут обращаться к одной учетной записи. Они должны входить в группу безопасности. | [Назначение пользователя или группы корпоративному приложению в Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Доступ локального администратора к компьютеру, чтобы развернуть расширение панели доступа для Internet Explorer, Chrome или Firefox. | [Расширение "Панель доступа" для Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Расширение "Панель доступа" для Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Расширение "Панель доступа" для Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Установите расширение браузера. | [Расширение "Панель доступа" для Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Расширение "Панель доступа" для Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Расширение "Панель доступа" для Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Настройте приложение из коллекции. | [Новые возможности управления корпоративными приложениями в Azure Active Directory: обновленная и усовершенствованная коллекция приложений](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Настройте единый вход с паролем. | [Управление параметрами единого входа для корпоративных приложений на новом портале Azure: вход на основе пароля](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Назначьте приложение группе, указанной в предварительных требованиях, пока задаете для нее учетные данные. | [Назначение пользователя или группы корпоративному приложению в Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Входите в качестве разных пользователей, обращающихся к приложению как **к общей учетной записи.**  |  |
| При необходимости вы также можете просмотреть отчеты об использовании приложения. Обратите внимание, что имеется некоторая задержка, поэтому для отражения трафика в отчетах нужно немного подождать. | [Отчеты о действиях входа на портале Azure Active Directory: использование управляемых приложений](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Политики хранения отчетов Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Рекомендации

Если целевое приложение отсутствует в коллекции, можно использовать собственное приложение. Дополнительные сведения: [Новые возможности управления корпоративными приложениями в Azure Active Directory. Добавление пользовательских приложений из одного расположения](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Не забывайте о следующих требованиях:
   * Приложение должно иметь известный URL-адрес входа.
   * Страница входа должна содержать HTML-форму с одним или несколькими текстовыми полями, которые расширения браузера могут заполнять автоматически. По меньше мере она должна содержать имя пользователя и пароль.

## <a name="app-proxy-configuration"></a>Настройка прокси приложения

Примерное время выполнения: 20 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Подписка на Microsoft Azure AD Basic или Premium, а также каталог Azure AD, для которого вы являетесь глобальным администратором. | [Выпуски Azure Active Directory](active-directory-editions.md) |
| Размещенное локально веб-приложение, которое нужно настроить для удаленного доступа. |  |
| Сервер под управлением Windows Server 2012 R2 или Windows 8.1 или более поздней версии, на котором можно установить соединитель прокси приложения. | [Сведения о соединителях прокси приложения Azure AD](application-proxy-understand-connectors.md) |
| Если в сетевом пути используется брандмауэр, убедитесь, что он открыт для запросов HTTPS (TCP), поступающих из соединителя в прокси приложения. | [Включение прокси приложения на портале Azure: необходимые условия для прокси приложения](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Если ваша организация использует прокси-серверы для подключения к Интернету, изучите сведения об их настройке в записи блога "Working with existing on-premises proxy servers" (Работа с имеющимися локальными прокси-серверами). | [Работа с имеющимися локальными прокси-серверами](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Установите соединитель на сервере. | [Включение прокси приложения на портале Azure: установка и регистрация соединителя](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Опубликуйте локальное приложение в Azure AD в качестве приложения прокси приложения. | [Публикация приложений с помощью прокси приложения Azure AD](application-proxy-publish-azure-portal.md) |
| Назначьте тестовых пользователей. | [Публикация приложений с помощью прокси приложения Azure AD: добавление тестового пользователя](application-proxy-publish-azure-portal.md#add-a-test-user) |
| При необходимости настройте процедуру единого входа для пользователей. | [Реализация единого входа с помощью прокси приложения Azure AD](application-proxy-sso-azure-portal.md) |
| Протестируйте приложение, войдя на портал MyApps в качестве назначенного пользователя. | https://myapps.microsoft.com |

### <a name="considerations"></a>Рекомендации

1. Хотя рекомендуется поместить соединитель в корпоративной сети, в некоторых случаях его размещение в облаке повышает производительность. Дополнительные сведения: [Аспекты топологии сети при использовании прокси приложения Azure Active Directory](application-proxy-network-topology-considerations.md).
2. Дополнительные сведения о безопасности и том, как получить решение для безопасного удаленного доступа, поддерживая только исходящие подключения, см. в статье [Вопросы безопасности при удаленном доступе к приложениям через прокси приложения Azure AD](application-proxy-security-considerations.md).

## <a name="generic-ldap-connector-configuration"></a>Настройка универсального соединителя LDAP

Примерное время выполнения: 60 минут.

> [!IMPORTANT]
> Для использования этой расширенной конфигурации нужно сначала ознакомиться с FIM/MIM. Дополнительные сведения о ее использовании в рабочей среде можно получить в [службе поддержки Premier](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Установленная и настроенная среда Azure AD Connect. | Стандартный блок: [Синхронизация каталога. Синхронизация хэша паролей](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Соответствующий требованиям экземпляр ADLDS. | [Технический справочник по универсальному соединителю LDAP: общие сведения об универсальном соединителе LDAP](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Список рабочих нагрузок, используемых пользователями, а также связанные с этими нагрузками атрибуты. | [Службы синхронизации Azure AD Connect: атрибуты, синхронизируемые с Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Добавьте универсальный соединитель LDAP. | [Технический справочник по универсальному соединителю LDAP: создание нового соединителя](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Создайте профили выполнения для созданного соединителя (полный импорт, разностный импорт, полная синхронизация, разностная синхронизация, экспорт). | [Создание профиля выполнения для агента управления](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Использование соединителей с Synchronization Service Manager Azure AD Connect](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Запустите профиль полного импорта и убедитесь, что в пространстве соединителя есть объекты. | [Поиск объекта пространства соединителя](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Использование соединителей с Synchronization Service Manager Azure AD Connect: пространство поиска соединителя](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Создайте правила синхронизации, чтобы объекты в метавселенной имели подходящие атрибуты для рабочих нагрузок. | [Синхронизация Azure AD Connect. Рекомендации по изменению конфигурации по умолчанию: изменения в правилах синхронизации](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Служба синхронизации Azure AD Connect: общие сведения о декларативной подготовке](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Служба синхронизации Azure AD Connect: общие сведения о выражениях декларативной подготовки](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Запустите полный цикл синхронизации. | [Синхронизация Azure AD Connect. Планировщик: запуск планировщика](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| При возникновении проблем выполните устранение неполадок. | [Устранение неполадок синхронизации объекта с Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Убедитесь, что пользователь LDAP может войти и обратиться к приложению. | https://myapps.microsoft.com |

### <a name="considerations"></a>Рекомендации

> [!IMPORTANT]
> Для использования этой расширенной конфигурации нужно сначала ознакомиться с FIM/MIM. Дополнительные сведения о ее использовании в рабочей среде можно получить в [службе поддержки Premier](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Группы — делегированное владение.

Примерное время выполнения: 10 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Приложение SaaS (федеративный единый вход или единый вход с паролем) уже настроено. | Стандартный блок: [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration) |
| Определена облачная группа, которой назначен доступ к приложению в № 1. | Стандартный блок: [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration) <br/>[Создание группы и добавление в нее пользователей в Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Доступны учетные данные для владельца группы. | [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md) |
| Определены учетные данные для информационного работника, обращающегося к приложениям. | [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Определите группу, которой был предоставлен доступ к приложению, настройте ее владельца.| [Управление параметрами группы в Azure Active Directory](active-directory-groups-settings-azure-portal.md) |
| Войдите в качестве владельца группы и просмотрите членство в группах на вкладке групп в панели доступа. | [Страница управления группами Azure Active Directory](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Добавьте информационного работника, которого требуется протестировать. |  |
| Войдите в качестве информационного работника и убедитесь, что элемент доступен. | [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Рекомендации

Если для приложения включена подготовка, может потребоваться подождать несколько минут для завершения подготовки, прежде чем вы сможете обратиться к приложению от лица информационного работника.

## <a name="saas-and-identity-lifecycle"></a>SaaS и жизненный цикл удостоверений

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Приложение SaaS (федеративный единый вход или единый вход с паролем) уже настроено. | Стандартный блок: [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration) |
| Определена облачная группа, которой назначен доступ к приложению в № 1. | Стандартный блок: [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration) <br/>[Создание группы и добавление в нее пользователей в Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Определены учетные данные для информационного работника, обращающегося к приложениям. | [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Удалите пользователя из группы, которой назначено приложение. | [Управление участниками групп в клиенте Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Подождите несколько минут, пока отзыв закончится. | [Автоматическая подготовка пользователей для приложения SaaS в Azure AD: как работает автоматическая подготовка](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| В отдельном сеансе браузера войдите в качестве информационного работника на портал "Мои приложения" и убедитесь, что элемент отсутствует. | http://myapps.microsoft.com |


### <a name="considerations"></a>Рекомендации

Экстраполируйте сценарий подтверждения концепции на сценарии с пользователями, выходящими из состава, и (или) отпусками. Если пользователь отключается в локальной службе AD или удаляется, никакого другого способа войти в приложение SaaS у него нет.

## <a name="self-service-access-to-application-management"></a>Управление самостоятельным доступом к приложениям

Примерное время выполнения: 10 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Определите пользователей подтверждения концепции, которые будут запрашивать доступ к приложениям в составе группы безопасности. | Стандартный блок: [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration) |
| Целевое приложение развернуто. | Стандартный блок: [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Перейдите в колонку "Корпоративные приложения" на портале управления Azure AD. | [Портал управления Azure AD: корпоративные приложения](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Настройте самообслуживание для приложения из предварительных требований. | [Новые возможности управления корпоративными приложениями в Azure Active Directory: настройка самостоятельного доступа к приложениям](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Войдите в качестве информационного работника на портал "Мои приложения". | http://myapps.microsoft.com |
| Обратите внимание на кнопку "+ Добавить приложение" вверху страницы. Используйте ее для получения доступа к приложению. |  |

### <a name="considerations"></a>Рекомендации

Выбранное приложение может иметь требования по подготовке, поэтому немедленный переход в приложение может вызвать ошибки. Если выбранное приложение поддерживает подготовку с помощью Azure AD и эта функция настроена, вы можете использовать данную возможностью, чтобы показать всю рабочую процедуру целиком. Дополнительные рекомендации см. в стандартном блоке [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration).

## <a name="self-service-password-reset"></a>Самостоятельный сброс пароля

Примерное время выполнения: 15 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Включите самостоятельное управление паролями в клиенте. | [Сброс паролей Azure Active Directory для ИТ-администраторов](active-directory-passwords-update-your-own-password.md) |
| Включите обратную запись паролей для управления паролями в локальной среде. Обратите внимание, что для этого требуются определенные версии Azure AD Connect. | [Необходимые условия для обратной записи паролей](active-directory-passwords-writeback.md) |
| Определите пользователей подтверждения концепции, которые будут использовать эту функцию, и убедитесь, что они являются членами группы безопасности. Для полноценной демонстрации возможности пользователи не должны обладать правами администратора. | [Настройка управления паролями Azure Active Directory: ограничение доступа для сброса пароля](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Перейдите на портал управления Azure AD: сброс пароля. | [Портал управления Azure AD: сброс пароля](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Определите политику сброса паролей. Для подтверждения концепции можно использовать телефонный звонок и вопросы и ответы. Рекомендуется включить регистрацию, чтобы потребовалось войти в панель доступа. |  |
| Выйдите из системы и войдите в качестве информационного работника. |  |
| Задайте данные для самостоятельного сброса паролей, как было настроено на шаге 2. | https://aka.ms/ssprsetup |
| Закройте браузер. |  |
| Начните с начала процесс входа в качестве информационного работника, который вы использовали на шаге 4. |  |
| Сбросьте пароль. | [Изменение своего пароля: сброс пароля](active-directory-passwords-update-your-own-password.md) |
| Повторите вход в Azure AD и на локальные ресурсы с новым паролем. |  |

### <a name="considerations"></a>Рекомендации

1. Если обновление Azure AD Connect замедляет работу, рекомендуется использовать его с облачными учетными записями или выполнять в качестве демонстрации для отдельной среды.
2. Для администраторов действует другая политика, и сброс пароля с помощью учетной записи администратора может нарушить проверку концепции и привести к путанице. Чтобы проверить работу сброса, используйте учетную запись обычного пользователя.


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Многофакторная идентификация в Azure с помощью телефонных звонков

Примерное время выполнения: 10 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Удостоверение пользователей подтверждения концепции, которые будут использовать Многофакторную идентификацию.  |  |
| Телефон с хорошим приемом для прохождения Многофакторной идентификации.  | [Что такое Многофакторная идентификация Azure?](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Перейдите в колонку "Пользователи и группы" на портале управления Azure AD. | [Портал управления Azure AD: пользователи и группы](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Выберите колонку "Все пользователи". |  |
| Нажмите кнопку "Многофакторная идентификация" в верхней части. | Прямой URL-адрес портала Azure MFA: https://aka.ms/mfaportal |
| В параметрах "Пользователь" выберите пользователей подтверждения концепции и включите для них Многофакторную идентификацию. | [Состояние пользователей в службе Многофакторной идентификации Azure](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| Войдите в качестве пользователя подтверждения концепции и пройдите процесс подтверждения.  |  |

### <a name="considerations"></a>Рекомендации

1. Шаги по подтверждению концепции в этом стандартном блоке явно задают Многофакторную идентификацию для всех имен входа пользователя. Существуют и другие средства, такие как условный доступ и защита идентификации, включающие Многофакторную идентификацию в более специализированных сценариях. Это будет нужно учитывать при переходе от подтверждения концепции к рабочей среде.
2. Шаги подтверждения концепции в этом стандартном блоке явно используют телефонные звонки как наиболее целесообразный метод Многофакторной идентификации. При переходе от подтверждения концепции к рабочей среде рекомендуется по мере возможности использовать в качестве второго фактора такие приложения, как [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md).
Дополнительные сведения: [черновик специальной публикации NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Условный доступ MFA к приложениям SaaS

Примерное время выполнения: 10 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Определите пользователей подтверждения концепции для ориентации на политику. Эти пользователи должны входить в группу безопасности, чтобы на них распространялась политика условного доступа. | [Настройка федеративного единого входа SaaS](#saas-federated-sso-configuration). |
| Приложение SaaS уже настроено. |  |
| Пользователи подтверждения концепции уже назначены для приложения. |  |
| Учетные данные для пользователя подтверждения концепции доступны. |  |
| Пользователь подтверждения концепции зарегистрирован для Многофакторной идентификации. Используется телефон с хорошим приемом. | https://aka.ms/ssprsetup |
| Устройство во внутренней сети. IP-адрес настроен во внутреннем диапазоне. | Найдите свой IP-адрес: https://www.bing.com/search?q=what%27s+my+ip |
| Устройство во внешней сети (это может быть телефон, использующий сеть оператора мобильной сети). |  |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Перейдите на портал управления Azure AD: колонка условного доступа. | [Портал управления Azure AD: условный доступ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Создайте политику условного доступа:<br/>— Нацельтесь на пользователей подтверждения концепции в разделе "Пользователи и группы".<br/>— Нацельтесь на приложение подтверждения концепции в разделе "Облачные приложения".<br/>— Нацельтесь на все расположения, кроме надежных, в разделе "Условия" -> "Расположения". **Примечание.** Надежные IP-адреса настраиваются на [портале Многофакторной идентификации](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx).<br/>— Запросите Многофакторную идентификацию в разделе "Предоставление". | [Начало работы с условным доступом в Azure Active Directory: настройка политики](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Обратитесь к приложению из корпоративной сети. | [Начало работы с условным доступом в Azure Active Directory: тестирование политики](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Обратитесь к приложению из общедоступной сети. | [Начало работы с условным доступом в Azure Active Directory: тестирование политики](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Рекомендации

При наличии федерации вы можете использовать локальный поставщик удостоверений для обмена данными внутри и вне корпоративной сети с помощью утверждений. Такой подход позволяет не управлять списком IP-адресов, что в крупных организациях может быть непростой задачей. В данной настройке вам нужна учетная запись для сценария "сетевого роуминга" (пользователь входит из внутренней сети, а затем переходит, например, в кафе), а также следует разобраться в наследованиях. Дополнительные сведения: [Защита облачных ресурсов с помощью Многофакторной идентификации Azure и AD FS. Доверенные IP-адреса для федеративных пользователей](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Управление привилегированными пользователями (PIM)

Примерное время выполнения: 15 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Определите глобального администратора, который будет участвовать в подтверждении концепции для PIM. | [Приступая к использованию Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Определите глобального администратора, который станет администратором безопасности. | [Приступая к использованию Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Различные административные роли в Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md) |
| Необязательно: убедитесь, есть ли у глобальных администраторов доступ к электронной почте для использования уведомлений по электронной почте в PIM. | [Что такое Azure AD Privileged Identity Management? Настройка параметров активации ролей](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Выполните вход по адресу https://portal.azure.com как глобальный администратор и запустите начальную загрузку колонки PIM. Глобальный администратор, выполнивший этот шаг, назначается администратором безопасности.  Назовем этот субъект GA1. | [Использование мастера защиты в Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Определите глобального администратора и переведите его из постоянного в соответствующий. Для наглядности этот администратор должен отличаться от использованного в шаге 1. Назовем этот субъект GA2. | [Управление привилегированными пользователями Azure AD: добавление и удаление роли пользователя](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Что такое Azure AD Privileged Identity Management? Настройка параметров активации ролей](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Теперь войдите как GA2 по адресу https://portal.azure.com и попробуйте изменить "Параметры пользователя". Обратите внимание, что некоторые параметры будут недоступны. | |
| На новой вкладке в том же сеансе, что и в шаге 3, перейдите по адресу https://portal.azure.com и добавьте колонку PIM на панель мониторинга. | [Как активировать и деактивировать роли в компоненте управления привилегированными пользователями Azure AD: добавление приложения для управления привилегированными пользователями](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Запросите активацию для роли глобального администратора. | [Как активировать и деактивировать роли в компоненте управления привилегированными пользователями Azure AD: активация роли](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Обратите внимание, что если GA2 никогда не регистрировался для Многофакторной идентификации, потребуется выполнить такую регистрацию. |  |
| Вернитесь на исходную вкладку в шаге 3 и нажмите кнопку обновления в браузере. Обратите внимание, что теперь вы можете изменять "Параметры пользователя". | |
| Кроме того, если у глобальных администраторов включена электронная почта, можно проверить папку "Входящие" для GA1 и GA2 и просмотреть уведомление об активации роли. |  |
| Проверьте журнал аудита и просмотрите отчет, чтобы убедиться, что повышение GA2 отображается. | [Что такое Azure AD Privileged Identity Management: просмотр активности роли](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Рекомендации

Эта возможность является частью Azure AD Premium P2 и/или EMS E5.

## <a name="discovering-risk-events"></a>Обнаружение событий риска

Примерное время выполнения: 20 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Устройство с установленным браузером Tor. | [Скачайте браузер Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Получите доступ к пользователю подтверждения концепции для выполнения входа. | [Тренировочное задание по защите идентификации Azure Active Directory](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Откройте браузер Tor. | [Скачайте браузер Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Выполните вход по адресу https://myapps.microsoft.com с учетной записью пользователя для подтверждения концепции | [Тренировочное задание по защите идентификации Azure Active Directory: моделирование событий риска](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Подождите 5–7 минут. |  |
| Выполните вход по адресу https://portal.azure.com как глобальный администратор и откройте колонку защиты идентификации | https://aka.ms/aadipgetstarted |
| Откройте колонку событий риска. Вы должны увидеть запись в разделе "Попытки входа с анонимных IP-адресов".  | [Тренировочное задание по защите идентификации Azure Active Directory: моделирование событий риска](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Рекомендации

Эта возможность является частью Azure AD Premium P2 и/или EMS E5.

## <a name="deploying-sign-in-risk-policies"></a>Развертывание политик риска при входе в систему

Примерное время выполнения: 10 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Устройство с установленным браузером Tor. | [Скачайте браузер Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Обратитесь в качестве пользователя подтверждения концепции тестирования входа. |  |
| Пользователь подтверждения концепции зарегистрирован для Многофакторной идентификации. Используйте телефон с хорошим приемом. | Стандартный блок: [Многофакторная проверка подлинности в Azure с помощью телефонных звонков](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Выполните вход по адресу https://portal.azure.com как глобальный администратор и откройте колонку защиты идентификации | https://aka.ms/aadipgetstarted |
| Включите политику риска для входа:<br/>— Назначено: пользователь подтверждения концепции<br/>— Условия: риск входа средний или выше (вход из анонимного расположения относится к среднему уровню риска)<br/>— Элементы управления: требовать Многофакторную идентификацию | [Тренировочное задание по защите идентификации Azure Active Directory: риск при входе](active-directory-identityprotection-playbook.md) |
| Откройте браузер Tor. | [Скачайте браузер Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Выполните вход по адресу https://myapps.microsoft.com с учетной записью пользователя для подтверждения концепции |  |
| Обратите внимание на запрос Многофакторной идентификации. | [Процедуры входа с защитой идентификации Azure AD: восстановление входа, представлявшего риск](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Рекомендации

Эта возможность является частью Azure AD Premium P2 и (или) EMS E5. Дополнительные сведения о событиях риска: [События риска Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Настройка проверки подлинности на основе сертификата

Примерное время выполнения: 20 минут.

### <a name="pre-requisites"></a>Предварительные требования

| Предварительные требования | Ресурсы |
| --- | --- |
| Устройство с подготовленным сертификатом пользователя (Windows, iOS или Android) из корпоративной PKI. | [Развертывание сертификатов пользователей](https://msdn.microsoft.com/library/cc770857.aspx) |
| Домен Azure AD в федерации с ADFS. | [Azure AD Connect и федерация](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Обзор служб сертификатов Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Для устройств iOS требуется установленное приложение Microsoft Authenticator. | [Начало работы с приложением Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Действия

| Шаг | Ресурсы |
| --- | --- |
| Включите параметр "Проверка подлинности сертификата" в службах ADFS. | [Настройка политик проверки подлинности: глобальная настройка основной проверки подлинности в Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Необязательно: включите проверку подлинности на основе сертификата в Azure AD для клиентов Exchange Active Sync. | [Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Перейдите к панели доступа и выполните проверку подлинности с помощью сертификата пользователя. | https://myapps.microsoft.com |

### <a name="considerations"></a>Рекомендации

Чтобы получить дополнительные сведения об особенностях этого развертывания, см. [ADFS: проверка подлинности на основе сертификата в Azure AD и Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).



> [!NOTE]
> Владение сертификатом пользователя нужно защитить. Это можно сделать, управляя устройствами или используя ПИН-код (для смарт-карт).



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
