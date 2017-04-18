## <a name="playbook-steps"></a>Шаги сборника тренировочных заданий
1. [Введение](../articles/active-directory/active-directory-playbook-intro.md)
2. [Компоненты](../articles/active-directory/active-directory-playbook-ingredients.md).
    * [Тема](../articles/active-directory/active-directory-playbook-ingredients.md).
    * [Среда](../articles/active-directory/active-directory-playbook-ingredients.md#theme)
    * [Целевые пользователи](../articles/active-directory/active-directory-playbook-ingredients.md#environment).
3. [Реализация](../articles/active-directory/active-directory-playbook-implementation.md)
   * [Основа: синхронизация AD с Azure AD](../articles/active-directory/active-directory-playbook-implementation.md#foundation---syncing-ad-to-azure-ad).
     * [Расширение действия локальных удостоверений в облако](../articles/active-directory/active-directory-playbook-implementation.md#extending-your-on-premises-identity-to-the-cloud).  
     * [Назначение лицензий Azure AD с помощью групп](../articles/active-directory/active-directory-playbook-implementation.md#assigning-azure-ad-licenses-using-groups).
   * [Тема: большое количество приложений, одно удостоверение](../articles/active-directory/active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity).
     * [Интеграция приложений SaaS: федеративный единый вход](../articles/active-directory/active-directory-playbook-implementation.md#integrate-saas-applications---federated-sso).
     * [Единый вход и события жизненного цикла удостоверений](../articles/active-directory/active-directory-playbook-implementation.md#sso-and-identity-lifecycle-events).
     * [Интеграция приложений SaaS: единый вход с помощью пароля](../articles/active-directory/active-directory-playbook-implementation.md#integrate-saas-applications---password-sso).
     * [Безопасный доступ к общим учетным записям](../articles/active-directory/active-directory-playbook-implementation.md#secure-access-to-shared-accounts).
     * [Безопасный удаленный доступ к локальным приложениям](../articles/active-directory/active-directory-playbook-implementation.md#secure-remote-access-to-on-premises-applications).
     * [Синхронизация удостоверений LDAP в Azure AD](../articles/active-directory/active-directory-playbook-implementation.md#synchronize-ldap-identities-to-azure-ad).
   * [Тема: повышение уровня безопасности](../articles/active-directory/active-directory-playbook-implementation.md#theme---increase-your-security).
     * [Безопасный доступ к учетной записи администратора](../articles/active-directory/active-directory-playbook-implementation.md#secure-administrator-account-access).
     * [Безопасный доступ к приложениям](../articles/active-directory/active-directory-playbook-implementation.md#secure-access-to-applications).
     * [Включение JIT-администрирования](../articles/active-directory/active-directory-playbook-implementation.md#enable-just-in-time-jit-administration).
     * [Защита удостоверений в зависимости от рисков](../articles/active-directory/active-directory-playbook-implementation.md#protect-identities-based-on-risk).
     * [Проверка подлинности без использования паролей с помощью сертификатов](../articles/active-directory/active-directory-playbook-implementation.md#authenticate-without-passwords-using-certificate-based-authentication).
   * [Тема: самостоятельное масштабирование](../articles/active-directory/active-directory-playbook-implementation.md#theme---scale-with-self-service).
     * [Самостоятельный сброс пароля](../articles/active-directory/active-directory-playbook-implementation.md#self-service-password-reset).
     * [Самостоятельный доступ к приложениям](../articles/active-directory/active-directory-playbook-implementation.md#self-service-access-to-applications).
4. [Стандартные блоки](../articles/active-directory/active-directory-playbook-building-blocks.md).
   * [Каталог субъектов](../articles/active-directory/active-directory-playbook-building-blocks.md#catalog-of-actors).
   * [Общие предварительные требования для всех стандартных блоков](../articles/active-directory/active-directory-playbook-building-blocks.md#common-prerequisites-for-all-building-blocks).
   * [Синхронизация каталога. Синхронизация хэша паролей (PHS): новая установка](../articles/active-directory/active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation).
   * [Фирменная символика](../articles/active-directory/active-directory-playbook-building-blocks.md#branding).
   * [Групповое лицензирование](../articles/active-directory/active-directory-playbook-building-blocks.md#group-based-licensing).
   * [Настройка федеративного единого входа SaaS](../articles/active-directory/active-directory-playbook-building-blocks.md#saas-federated-sso-configuration).
   * [Настройка единого входа SaaS с помощью пароля](../articles/active-directory/active-directory-playbook-building-blocks.md#saas-password-sso-configuration).
   * [Настройка общих учетных записей SaaS](../articles/active-directory/active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration).
   * [Настройка прокси приложения](../articles/active-directory/active-directory-playbook-building-blocks.md#app-proxy-configuration).
   * [Настройка универсального соединителя LDAP](../articles/active-directory/active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration).
   * [Группы — делегированное владение](../articles/active-directory/active-directory-playbook-building-blocks.md#groups---delegated-ownership).
   * [SaaS и жизненный цикл удостоверений](../articles/active-directory/active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle).
   * [Управление самостоятельным доступом к приложениям](../articles/active-directory/active-directory-playbook-building-blocks.md#self-service-access-to-application-management).
   * [Самостоятельный сброс пароля](../articles/active-directory/active-directory-playbook-building-blocks.md#self-service-password-reset).
   * [Многофакторная идентификация в Azure с помощью телефонных звонков](../articles/active-directory/active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls).
   * [Условный доступ MFA к приложениям SaaS](../articles/active-directory/active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications).
   * [Управление привилегированными пользователями (PIM)](../articles/active-directory/active-directory-playbook-building-blocks.md#privileged-identity-management-pim).
   * [Обнаружение событий риска](../articles/active-directory/active-directory-playbook-building-blocks.md#discovering-risk-events).
   * [Развертывание политик риска при входе в систему](../articles/active-directory/active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies).
   * [Настройка проверки подлинности на основе сертификата](../articles/active-directory/active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication).