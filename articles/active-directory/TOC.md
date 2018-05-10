# Обзор
## [Что такое Microsoft Azure Active Directory](active-directory-whatis.md)
## [Об управлении удостоверениями Azure](identity-fundamentals.md)
## [Основные сведения о решениях для идентификации в Azure](understand-azure-identity-solutions.md)
## [Выбор решения для гибридной идентификации](choose-hybrid-identity-solution.md)
## [Сопоставление подписок Azure](active-directory-how-subscriptions-associated-directory.md)
## [Часто задаваемые вопросы](active-directory-faq.md)
## [Новые возможности](whats-new.md)


# Начало работы
## [Приступая к работе с Azure AD](get-started-azure-ad.md)
## [Регистрация для использования Azure AD Premium](active-directory-get-started-premium.md)
## [Добавление имени личного домена](add-custom-domain.md)
## [Настройка фирменной символики компании](customize-branding.md)
## [Добавление пользователей в Azure AD](add-users-azure-active-directory.md)
## [Назначение лицензий пользователям](license-users-groups.md)
## [Настройка самостоятельного сброса пароля](authentication/quickstart-sspr.md)


# Практическое руководство
## Планирование и проектирование
### [Основы архитектуры Azure AD](active-directory-architecture.md)
### [Сопоставление утверждений в Azure Active Directory](active-directory-claims-mapping.md)
### [Развертывание решения для гибридной идентификации](active-directory-hybrid-identity-design-considerations-overview.md)
#### Определение требований
##### [Удостоверение](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Синхронизация каталогов](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Многофакторная проверка подлинности](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Стратегия жизненного цикла удостоверений](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Планирование безопасности данных](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Защита данных](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Управление содержимым](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Контроль доступа](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Реагирование на инциденты](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Планирование жизненного цикла удостоверений
##### [Задачи](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Стратегия внедрения](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Дальнейшие действия](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Сравнение инструментов](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Управление пользователями
### [Добавление новых пользователей в Azure AD](add-users-azure-active-directory.md)
### [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
### [Совместное использование учетных записей](active-directory-sharing-accounts.md)
### [Назначение пользователям ролей администратора](active-directory-users-assign-role-azure-portal.md)
### [Добавление гостевых пользователей из другого каталога (B2B)](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Добавление пользователей B2B администраторами](active-directory-b2b-admin-add-users.md)
#### [Добавление пользователей B2B информационными работниками](active-directory-b2b-iw-add-users.md)
#### [API и настройка](active-directory-b2b-api.md)
#### [Примеры кода и сценариев Azure PowerShell](active-directory-b2b-code-samples.md)
#### [Пример самостоятельной регистрации на портале](active-directory-b2b-self-service-portal.md)
#### [Сообщение электронной почты с приглашением](active-directory-b2b-invitation-email.md)
#### [Активация приглашения](active-directory-b2b-redemption-experience.md)
#### [Добавление пользователей B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
#### [Предоставление или отзыв приглашений](active-directory-b2b-allow-deny-list.md)
#### [Условный доступ для B2B](active-directory-b2b-mfa-instructions.md)
#### [Политики общего доступа к B2B](active-directory-b2b-delegate-invitations.md)
#### [Добавление B2B-пользователя к роли](active-directory-b2b-add-guest-to-role.md)
#### [Динамические группы и B2B-пользователи](active-directory-b2b-dynamic-groups.md)
#### [Аудит и отчеты](active-directory-b2b-auditing-and-reporting.md)
#### [B2B для гибридных организаций](active-directory-b2b-hybrid-organizations.md)
##### [Предоставление пользователям B2B доступа к локальным приложениям](active-directory-b2b-hybrid-cloud-to-on-premises.md)
##### [Предоставление локальным пользователям доступа к облачным приложениям](active-directory-b2b-hybrid-on-premises-to-cloud.md)
#### [Доступ внешних пользователей к B2B и Office 365](active-directory-b2b-o365-external-user.md)
#### [Лицензирование B2B](active-directory-b2b-licensing.md)
#### [Текущие ограничения](active-directory-b2b-current-limitations.md)
#### [Часто задаваемые вопросы](active-directory-b2b-faq.md)
#### [Устранение неполадок B2B](active-directory-b2b-troubleshooting.md)
#### [Общие сведения о B2B-пользователе](active-directory-b2b-user-properties.md)
#### [Маркер B2B-пользователя](active-directory-b2b-user-token.md)
#### [B2B для интегрированных приложений Azure AD](active-directory-b2b-configure-saas-apps.md)
#### [Сопоставление утверждений пользователя B2B](active-directory-b2b-claims-mapping.md)
#### [Сравнение совместной работы B2B и B2C](active-directory-b2b-compare-b2c.md)
#### [Получение технической поддержки для B2B](active-directory-b2b-support.md)

## [Управление группами и членами](active-directory-manage-groups.md)
### Управление группами
#### [портал Azure](active-directory-groups-create-azure-portal.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Управление членами группы](active-directory-groups-members-azure-portal.md)
### [Управление владельцами групп](active-directory-accessmanagement-managing-group-owners.md)
### [Управление членством в группах](active-directory-groups-membership-azure-portal.md)
### [Назначение лицензий с помощью групп](active-directory-licensing-whatis-azure-portal.md)
#### [Назначение лицензий группе](active-directory-licensing-group-assignment-azure-portal.md)
#### [Поиск и устранение проблем с лицензированием групп](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Перевод отдельных лицензированных пользователей на групповое лицензирование](active-directory-licensing-group-migration-azure-portal.md)
#### [Дополнительные сценарии группового лицензирования](active-directory-licensing-group-advanced.md)
#### [Примеры Azure PowerShell для группового лицензирования](active-directory-licensing-ps-examples.md)
#### [Справочник по продуктам и планам службы в Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Настройка срока действия групп Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Просмотр всех групп](active-directory-groups-view-azure-portal.md)
### [Включение доступа для групп в приложениях SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Восстановление удаленной группы Office 365](active-directory-groups-restore-azure-portal.md)
### Управление параметрами группы
#### [портал Azure](active-directory-groups-settings-azure-portal.md)
#### [Командлеты](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Создание дополнительных правил
#### [портал Azure](active-directory-groups-dynamic-membership-azure-portal.md)
### [Настройка групп самообслуживания](active-directory-accessmanagement-self-service-group-management.md)
### [Устранение неполадок](active-directory-accessmanagement-troubleshooting.md)

## [Управление отчетами](active-directory-reporting-azure-portal.md)
### [Действие входа](active-directory-reporting-activity-sign-ins.md)
### [Действие аудита](active-directory-reporting-activity-audit-logs.md)
### [Пользователи под угрозой](active-directory-reporting-security-user-at-risk.md)
### [Вход, представляющий риск](active-directory-reporting-security-risky-sign-ins.md)
### [События риска](active-directory-reporting-risk-events.md)
### [Часто задаваемые вопросы](active-directory-reporting-faq.md)
### Задачи
#### [Настройка именованных расположений](active-directory-named-locations.md)
#### [Поиск отчетов об активности](active-directory-reporting-migration.md)
#### [Использование пакета содержимого Azure Active Directory Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
### Справочные материалы
#### [Сохранение](active-directory-reporting-retention.md)
#### [Задержки](active-directory-reporting-latencies-azure-portal.md)
#### [Уведомления](active-directory-reporting-notifications.md)
#### [Справочные материалы по действиям аудита](active-directory-reporting-activity-audit-reference.md)
#### [Коды ошибок входа в систему](active-directory-reporting-activity-sign-ins-errors.md)
#### [Многофакторная проверка подлинности](active-directory-reporting-activity-sign-ins-mfa.md)
### Устранение неполадок
#### [Отсутствующие данные аудита](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Отсутствующие данные в файлах для скачивания](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Ошибки пакета содержимого журналов действий Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Программный доступ](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Справочные материалы по аудиту](active-directory-reporting-api-audit-reference.md)
#### [Справочник по входу](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Предварительные требования](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Примеры аудита](active-directory-reporting-api-audit-samples.md)
#### [Примеры входа](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Использование сертификатов](active-directory-reporting-api-with-certificates.md)

## Управление паролями
### [Общие сведения о паролях](authentication/active-directory-passwords-overview.md)
### Документы пользователя
#### [Сброс или изменение пароля](active-directory-passwords-update-your-own-password.md)
#### [Рекомендации по паролям](active-directory-secure-passwords.md)
#### [Регистрация для самостоятельного сброса пароля](active-directory-passwords-reset-register.md)
### [Принципы работы SSPR](authentication/concept-sspr-howitworks.md)
### [Руководство по развертыванию SSPR](authentication/howto-sspr-deployment.md)
### [SSPR и Windows 10](authentication/tutorial-sspr-windows.md)
### [Политики SSPR](authentication/concept-sspr-policy.md)
### [Настройка SSPR](authentication/concept-sspr-customization.md)
### [Требования к данным SSPR](authentication/howto-sspr-authenticationdata.md)
### [Отчеты SSPR](authentication/howto-sspr-reporting.md)
### ИТ-администрирование. Сброс паролей
#### [портал Azure](active-directory-users-reset-password-azure-portal.md)
### [Лицензия SSPR](authentication/concept-sspr-licensing.md)
### [Обратная запись паролей](authentication/howto-sspr-writeback.md)
### [Устранение неполадок](authentication/active-directory-passwords-troubleshoot.md)
### [Часто задаваемые вопросы](authentication/active-directory-passwords-faq.md)


## Управление устройствами
### [Введение](device-management-introduction.md)
### [Использование портала Azure](device-management-azure-portal.md)
### [Планирование присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Часто задаваемые вопросы](device-management-faq.md)
### Задачи
#### [Настройка устройств Windows 10, зарегистрированных в Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Настройка устройств, присоединенных к Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Настройка гибридных устройств, присоединенных к Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Локальное развертывание](active-directory-device-registration-on-premises-setup.md)
#### [Присоединение к Azure AD во время первого запуска Windows 10](device-management-azuread-joined-devices-frx.md)
### Устранение неполадок
#### [Устройства под управлением Windows 10 и Windows Server 2016, присоединенные к гибридному облаку Azure AD](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Устройства под управлением прежних версий Windows, присоединенные к гибридному облаку Azure AD](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Управление приложениями
### [Обзор](active-directory-enable-sso-scenario.md)
### [Приступая к работе](active-directory-integrating-applications-getting-started.md)
### [Руководства по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)
### [Обнаружение облачных приложений](cloudappdiscovery-get-started.md)
#### [Создание отчетов о моментальных снимках](cloudappdiscovery-set-up-snapshots.md)
#### [Настройка непрерывного создания отчетов](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Использование настраиваемого средства синтаксического анализа журналов](https://docs.microsoft.com/cloud-app-security/custom-log-parser)


### [Удаленный доступ к приложениям с помощью прокси приложения](active-directory-application-proxy-get-started.md)
#### Начало работы
##### [Использование прокси приложения](active-directory-application-proxy-enable.md)
##### [Публикация приложений](application-proxy-publish-azure-portal.md)
##### [Пользовательские домены](active-directory-application-proxy-custom-domains.md)
#### [Единый вход](application-proxy-sso-overview.md)
##### [Единый вход с применением ограниченного делегирования Kerberos](active-directory-application-proxy-sso-using-kcd.md)
##### [Единый вход с применением заголовков](application-proxy-ping-access.md)
##### [Единый вход с хранением паролей](application-proxy-sso-azure-portal.md)
#### Основные понятия
##### [Соединители](application-proxy-understand-connectors.md)
##### [Безопасность](application-proxy-security-considerations.md)
##### [Сети](application-proxy-network-topology-considerations.md)


##### [Обновление с TMG или UAG](application-proxy-transition-from-uag-tmg.md)

#### Расширенные конфигурации
##### [Публикация в отдельных сетях](active-directory-application-proxy-connectors-azure-portal.md)
##### [Прокси-серверы](application-proxy-working-with-proxy-servers.md)
##### [Приложения с поддержкой утверждений](active-directory-application-proxy-claims-aware-apps.md)
##### [Приложения Native Client](active-directory-application-proxy-native-client.md)
##### [Автоматическая установка](active-directory-application-proxy-silent-installation.md)
##### [Пользовательская домашняя страница](application-proxy-office365-app-launcher.md)
##### [Преобразование встроенных ссылок](application-proxy-link-translation.md)
#### Пошаговые руководства по публикации
##### [Удаленный рабочий стол](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
##### [Tableau](active-directory-application-proxy-tableau.md)

#### [Устранение неполадок](active-directory-application-proxy-troubleshoot.md)

### Управление корпоративными приложениями
#### [Назначение пользователей](active-directory-coreapps-assign-user-azure-portal.md)
#### [Настройка фирменной символики](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Отключение входа пользователей](active-directory-coreapps-disable-app-azure-portal.md)
#### [Удаление пользователей](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Просмотр всех приложений](active-directory-coreapps-view-azure-portal.md)
#### [Управление подготовкой учетной записи пользователя](active-directory-enterprise-apps-manage-provisioning.md)
#### [Управление параметрами единого входа для корпоративных приложений](active-directory-enterprise-apps-manage-sso.md)
#### [Расширенные параметры подписи сертификатов для приложений SAML](active-directory-enterprise-apps-advance-certificate-options.md)
#### [Скрытие сторонних приложений в пользовательском интерфейсе](active-directory-coreapps-hide-third-party-app.md)
### [Настройка автоматического ускорения входа с помощью политики обнаружения домашней области](active-directory-auto-acceleration-using-hrd.md)

### [Управление доступом к приложениям](active-directory-managing-access-to-apps.md)
#### [Доступ с единым входом](active-directory-appssoaccess-whatis.md)
#### [Сертификаты для единого входа](active-directory-sso-certs.md)
#### [Ограничения клиента](active-directory-tenant-restrictions.md)
#### [Использование SCIM подготовки пользователей](active-directory-scim-provisioning.md)

### [Устранение неполадок](active-directory-application-troubleshoot-content-map.md)
#### [Разработка приложений](active-directory-application-dev-troubleshoot-content-map.md)
##### [Настройка и регистрация](active-directory-application-dev-config-content-map.md)
##### [Разработка](active-directory-application-dev-development-content-map.md)
#### [Управление приложениями](active-directory-application-management-troubleshoot-content-map.md)
##### [Конфигурация](active-directory-application-config-content-map.md)
##### [Вход](active-directory-application-sign-in-content-map.md)
##### [Подготовка](active-directory-application-provisioning-content-map.md)
##### [Управление доступом](active-directory-application-access-content-map.md)
##### [Панель доступа](active-directory-application-access-panel-content-map.md)
##### [Прокси приложения](active-directory-application-proxy-content-map.md)
##### [Условный доступ](active-directory-application-conditional-access-content-map.md)
### [Разработка приложений](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Библиотека документов](active-directory-apps-index.md)

## Управление каталогом
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Имена пользовательских доменов
#### [Краткое руководство](add-custom-domain.md)
#### [Добавление имен личных доменов](active-directory-domains-manage-azure-portal.md)
### [Администрирование каталога](active-directory-administer.md)
### [Несколько каталогов](active-directory-licensing-directory-independence.md)
### [Самостоятельная регистрация](active-directory-self-service-signup.md)
### [Взятие под контроль неуправляемого каталога](domains-admin-takeover.md)
### [Enterprise State Roaming.](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Включение](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Параметры групповой политики](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Параметры Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Часто задаваемые вопросы](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Устранение неполадок](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Интеграция локальных удостоверений с помощью Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Управление доступом к Azure](../role-based-access-control/toc.yml)

## Делегирование доступа к ресурсам
### [Роли администратора](active-directory-assign-admin-roles-azure-portal.md)
#### [Назначение ролей администратора](active-directory-users-assign-role-azure-portal.md)
#### [Разрешения пользователя по умолчанию](users-default-permissions.md)
### [Административные единицы](active-directory-administrative-units-management.md)
### [Настройка времени существования маркеров](active-directory-configurable-token-lifetimes.md)
### [Управление учетными записями администратора для аварийного доступа](active-directory-admin-manage-emergency-access-accounts.md)
### [Защита привилегированных ролей](admin-roles-best-practices.md)

## Проверки доступа
### [Обзор проверки доступа](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Выполнение проверки доступа](active-directory-azure-ad-controls-complete-access-review.md)
### [Создание проверки доступа](active-directory-azure-ad-controls-create-access-review.md)
### [Как выполнить проверку доступа](active-directory-azure-ad-controls-perform-access-review.md)
### [Как проверить доступ](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Гостевой доступ с использованием проверок доступа](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Управление доступом пользователей с помощью проверок](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Управление программами и элементами управления](active-directory-azure-ad-controls-manage-programs-controls.md)

## Защита удостоверений
### [Условный доступ](active-directory-conditional-access-azure-portal.md)
#### [Условия](active-directory-conditional-access-conditions.md)
#### [Условия расположения](active-directory-conditional-access-locations.md)
#### [Элементы управления](active-directory-conditional-access-controls.md)
#### [Начало работы](active-directory-conditional-access-azure-portal-get-started.md)
#### [Рекомендации по использованию хранилища данных SQL Azure](active-directory-conditional-access-best-practices.md)
#### [Политики условного доступа к службам Office 365 с устройств](active-directory-conditional-access-device-policies.md)
#### [Миграция классических политик](active-directory-conditional-access-migration.md)
#### [Средство"Что если"](active-directory-conditional-access-whatif.md)
#### Быстрый запуск
##### [Настройка отдельных облачных приложений MFA](active-directory-conditional-access-app-based-mfa.md)
#### Задачи
##### [Перенос классической политики MFA](active-directory-conditional-access-migration-mfa.md)
##### [Настройка условного доступа на основе устройств](active-directory-conditional-access-policy-connected-applications.md)
##### [Настройка условного доступа на основе приложений](active-directory-conditional-access-mam.md)
##### [Условия использования для пользователей и приложений](active-directory-tou.md)
##### [Настройка VPN-подключения](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [Настройка SharePoint и Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Исправление](active-directory-conditional-access-device-remediation.md)
#### [Технический справочник: условный доступ к приложениям Azure AD](active-directory-conditional-access-technical-reference.md)
#### [Часто задаваемые вопросы](active-directory-conditional-faqs.md)

### Windows Hello
#### [Проверка подлинности без использования паролей](active-directory-azureadjoin-passport.md)
#### [Включение Windows Hello для бизнеса](active-directory-azureadjoin-passport-deployment.md)
### Аутентификация на основе сертификата
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Начало работы](active-directory-certificate-based-authentication-get-started.md)

### [Защита идентификации Azure AD](active-directory-identityprotection.md)
#### [Включение](active-directory-identityprotection-enable.md)
#### [Обнаружение уязвимостей](active-directory-identityprotection-vulnerabilities.md)
#### [События риска](active-directory-identity-protection-risk-events.md)
#### [Уведомления](active-directory-identityprotection-notifications.md)
#### [Процедура входа](active-directory-identityprotection-flows.md)
#### [Моделирование событий риска](active-directory-identityprotection-playbook.md)
#### [Разблокирование пользователей](active-directory-identityprotection-unblock-howto.md)
#### [Часто задаваемые вопросы](active-directory-identity-protection-faqs.md)
#### [Глоссарий](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [управление привилегированными пользователями;](active-directory-privileged-identity-management-configure.md)

## [Интеграция других служб с Azure AD]()
### [Включение интеграции с LinkedIn](linkedin-integration.md)

## [Развертывание AD FS в Azure](active-directory-aadconnect-azure-adfs.md)
### [Высокая доступность](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Изменение хэш-алгоритма подписи](active-directory-federation-sha256-guidance.md)

## [Устранение неполадок](active-directory-troubleshooting-support-howto.md)
### [Устранение неполадок в Active Directory: элемент отсутствует или недоступен](active-directory-troubleshooting.md)

## Развертывание подтверждения концепции Azure AD (PoC)
### [Сборник тренировочных заданий по PoC: введение](active-directory-playbook-intro.md)
### [Сборник тренировочных заданий по PoC: ингредиенты](active-directory-playbook-ingredients.md)
### [Сборник тренировочных заданий по PoC: реализация](active-directory-playbook-implementation.md)
### [Сборник тренировочных заданий по PoC: стандартные блоки](active-directory-playbook-building-blocks.md)


# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Командлеты Azure PowerShell](/powershell/azure/overview)
## [Справочник по API Java](/java/api)
## [API для .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Ограничения службы](active-directory-service-limits-restrictions.md)

# Сопутствующие материалы
## [Многофакторная идентификация](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Информация для разработчиков об Azure AD](./develop/active-directory-how-to-integrate.md)
## [Управление привилегированными пользователями Azure AD](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Ресурсы
## [Форум отзывов и предложений по Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Цены](https://azure.microsoft.com/pricing/details/active-directory/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
