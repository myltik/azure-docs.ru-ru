---
title: "Руководство по интеграции Azure Active Directory c Google Apps | Документация Майкрософт"
description: "Узнайте, как использовать Google Apps вместе с Azure Active Directory для настройки единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser-msft
translationtype: Human Translation
ms.sourcegitcommit: 0edd2d9b2d71d95260aa122545930aab64612106
ms.openlocfilehash: cc7348b933b1ed4a79bf282c1d9a8e073b1f3ba4


---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Руководство по интеграции Azure Active Directory с Google Apps
В этом учебнике мы покажем, как подключить среду Google Apps к Azure Active Directory (Azure AD). Вы узнаете, как настроить единый вход в Google Apps, включить автоматическую подготовку пользователей и предоставить пользователям доступ к Google Apps. 

## <a name="prerequisites"></a>Предварительные требования
1. Для доступа к Azure Active Directory через [классический портал Azure](https://manage.windowsazure.com)необходима действующая подписка Azure.
2. У вас должен быть действительный клиент для работы с [Google Apps for Work](https://www.google.com/work/apps/) или [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/). Для любой из этих служб можно воспользоваться бесплатной пробной учетной записью.

## <a name="video-tutorial"></a>Видеоруководство
Как включить единый вход в Google Apps за 2 минуты.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]
> 
> 

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
1. **Вопрос. Поддерживают ли Chromebooks и другие устройства Chrome единый вход Azure AD?**
   
    Ответ. Да, пользователи смогут войти в свои устройства Chromebook, используя свои учетные данные Azure AD. Сведения о том, почему учетные данные могут быть запрошены у пользователей дважды, см. в этой [статье о поддержке Google Apps](https://support.google.com/chrome/a/answer/6060880).
2. **Вопрос. Если включить единый вход, смогут ли пользователи использовать свои учетные данные Azure AD для входа в любой продукт Google, такой как Google Classroom, GMail, Google Drive, YouTube и т. д?**
   
    Ответ. Да, в зависимости от того, [какие приложения Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) вы решили включить или отключить для своей организации.
3. **Вопрос. Можно ли включить единый вход только для подмножества пользователей Google Apps?**
   
    Ответ. Нет, при включении единого входа всем пользователям Google Apps вашей организации сразу же потребуется использовать свои учетные данные для проверки подлинности Azure AD. Поскольку Google Apps не поддерживает наличие нескольких поставщиков удостоверений, то в качестве поставщика удостоверений для среды Google Apps может быть использован либо Azure AD, либо Google — но не оба одновременно.
4. **Вопрос. Если пользователь выполнил вход в Windows, пройдет ли он автоматическую проверку подлинности в Google Apps без запроса пароля?**
   
    Ответ. Существует два варианта включения этого сценария. Во-первых, пользователи могут входить в устройства Windows 10 посредством [присоединения к Azure Active Directory](active-directory-azureadjoin-overview.md). Кроме того, пользователи могут входить на устройства с Windows, присоединенные к домену локального каталога Active Directory, для которого разрешен единый вход Azure AD посредством развертывания [служб федерации Active Directory (AD FS)](active-directory-aadconnect-user-signin.md). Разумеется, в обоих случаях необходимо выполнить действия следующего учебника для включения единого входа между Azure AD и Google Apps.

## <a name="step-1-add-google-apps-to-your-directory"></a>Шаг 1. Добавление Google Apps в каталог
1. На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**.
   
    ![Выберите Active Directory в области навигации слева.][0]
2. В списке **Каталог** выберите каталог, в который нужно добавить Google Apps.
3. Щелкните **Приложения** в меню вверху.
   
    ![Щелкните "Приложения".][1]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Нажмите кнопку "Добавить", чтобы добавить новое приложение.][2]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Нажмите кнопку "Добавить приложение из коллекции".][3]
6. В **поле поиска** введите **Google Apps**. Выберите **Google Apps** из списка результатов и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Добавление Google Apps][4]
7. Вы увидите страницу быстрого запуска Google Apps.
   
    ![Страница быстрого запуска Google Apps в Azure AD][5]

## <a name="step-2-enable-single-sign-on"></a>Шаг 2. Включение единого входа
1. На странице быстрого запуска Google Apps в Azure AD нажмите кнопку **Настроить единый вход** .
   
    ![Кнопка "Настроить единый вход"][6]
2. Откроется диалоговое окно, и вы увидите экран с вопросом "Как пользователи должны входить в Google Apps?". Выберите **Единый вход Azure AD**, а затем нажмите кнопку **Далее**.
   
    ![Выберите "Единый вход Azure AD".][7]
   
   > [!NOTE]
   > Для получения дополнительных сведений о вариантах единого входа [щелкните здесь](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. На странице **Настроить параметры приложения** введите в поле **URL-адрес входа** URL-адрес клиента Google Apps в следующем формате: `https://mail.google.com/a/<yourdomain>`.
   
    ![Введите URL-адрес клиента.][8]
4. На странице **Автоматическая настройка единого входа** введите имя домена для своего клиента Google Apps. Затем нажмите кнопку **Настроить** .
   
    ![Введите доменное имя и нажмите кнопку "Настроить".](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)
   
   > [!NOTE]
   > Если вы хотите настроить единый вход вручную, см. раздел [Необязательный шаг. Настройка единого входа вручную](#optional-step-manually-configure-single-sign-on).
   > 
   > 
5. Войдите в учетную запись администратора Google Apps. Щелкните **Разрешить** , чтобы разрешить Azure Active Directory изменять конфигурацию в подписке Google Apps.
   
    ![Введите доменное имя и нажмите кнопку "Настроить".](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)
6. Подождите несколько секунд, пока Azure Active Directory выполнит настройку клиента Google Apps. После завершения нажмите кнопку **Далее**.
7. Если вы хотите получать уведомления об ошибках и предупреждения, связанные с обслуживанием конфигурации единого входа, введите адрес электронной почты на последней странице.
   
   ![Введите адрес электронной почты.][14]
8. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно. Сведения о проверке конфигурации см. в разделе [Назначение пользователей Google Apps](#step-4-assign-users-to-google-apps) ниже.

## <a name="optional-step-manually-configure-single-sign-on"></a>Необязательный шаг. Настройка единого входа вручную
Если вы хотите настроить единый вход вручную, выполните следующие действия.

1. На странице быстрого запуска Google Apps в Azure AD нажмите кнопку **Настроить единый вход** .
   
    ![Кнопка "Настроить единый вход"][6]
2. Откроется диалоговое окно, и вы увидите экран с вопросом "Как пользователи должны входить в Google Apps?". Выберите **Единый вход Azure AD**, а затем нажмите кнопку **Далее**.
   
    ![Выберите "Единый вход Azure AD".][7]
   
   > [!NOTE]
   > Для получения дополнительных сведений о вариантах единого входа [щелкните здесь](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. На странице **Настроить параметры приложения** введите в поле **URL-адрес входа** URL-адрес клиента Google Apps в следующем формате: `https://mail.google.com/a/<yourdomain>`.
   
    ![Введите URL-адрес клиента.][8]
4. На странице **Настроить единый вход автоматически** установите флажок **Вручную настроить это приложение для единого входа**. Нажмите кнопку **Далее**.
   
    ![Щелкните "Настроить вручную".](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)
5. На странице **Настройка единого входа в Google Apps** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
    ![Загрузка сертификата][9]
6. Откройте в браузере новую вкладку и войдите в [консоль администратора Google Apps](http://admin.google.com/) с использованием учетной записи администратора.
7. Щелкните **Security**(Безопасность). Если эта ссылка не отображается, она может быть скрыта в меню **More Controls** (Другие элементы управления) в нижней части экрана.
   
    ![Выбор элемента "Безопасность"][10]
8. На странице **Security** (Безопасность) выберите **Set up single sign-on (SSO)** (Настроить единый вход).
   
    ![Выбор элемента "Единый вход"][11]
9. Выполните следующие настройки.
   
    ![Настройка единого входа][12]
   
   * Выберите **Setup SSO with third party identity provider**(Настройка единого входа с помощью стороннего поставщика удостоверений).
   * В Azure AD скопируйте **URL-адрес службы единого входа** и вставьте его в Google Apps в поле **Sign-in page URL** (URL-адрес страницы входа).
   * В Azure AD скопируйте **URL-адрес службы единого выхода** и вставьте его в Google Apps в поле **Sign-out page URL** (URL-адрес страницы выхода).
   * В Azure AD скопируйте **URL-адрес изменения пароля** и вставьте его в Google Apps в поле **Change password URL** (URL-адрес изменения пароля).
   * В Google Apps в поле **Verification certificate**(Сертификат проверки) добавьте сертификат, загруженный на шаге 4.
   * Нажмите кнопку **Save Changes**(Сохранить изменения).
10. В Azure AD установите флажок подтверждения настройки единого входа, чтобы активировать сертификат, который вы отправили в Google Apps. Нажмите кнопку **Далее**.
    
     ![Установите флажок подтверждения.][13]
11. Если вы хотите получать уведомления об ошибках и предупреждения, связанные с обслуживанием конфигурации единого входа, введите адрес электронной почты на последней странице. 
    
    ![Введите адрес электронной почты.][14]
12. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно. Сведения о проверке конфигурации см. в разделе [Назначение пользователей Google Apps](#step-4-assign-users-to-google-apps) ниже.

## <a name="step-3-enable-automated-user-provisioning"></a>Шаг 3. Включение автоматической подготовки пользователей
> [!NOTE]
> Одним из способов автоматизации подготовки пользователей для Google Apps является использование средства синхронизации каталогов [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) , которое позволяет локально подготовить удостоверения Active Directory для Google Apps. В этом же учебнике мы рассмотрим подготовку к использованию Google Apps пользователей Azure Active Directory (облако) и групп, поддерживающих почту.
> 
> 

1. Войдите в [консоль администратора Google Apps](http://admin.google.com/) с использованием учетной записи администратора и щелкните **Security**(Безопасность). Если эта ссылка не отображается, она может быть скрыта в меню **More Controls** (Другие элементы управления) в нижней части экрана.
   
    ![Выбор элемента "Безопасность"][10]
2. На странице **Security** (Безопасность) щелкните **API Reference** (Справочник по API).
   
    ![Выбор элемента "Справочник по API".][15]
3. Выберите команду **Enable API access**(Включить доступ через API).
   
    ![Выбор элемента "Справочник по API".][16]
   
   > [!IMPORTANT]
   > Имя каждого пользователя в Azure Active Directory, которого вы хотите подготовить для Google Apps, *необходимо* привязать к личному домену. Например, имена пользователей, подобные bob@contoso.onmicrosoft.com, не будут приняты Google Apps, а bob@contoso.com — будут. Вы можете изменить текущий домен пользователя, изменив его свойства в Azure AD. Инструкции по настройке личного домена в Azure Active Directory и Google Apps приведены ниже.
   > 
   > 
4. Если вы еще не добавили имя личного домена в Azure Active Directory, выполните следующие действия.
   
   * На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**. Выберите свой каталог в списке каталогов. 
   * Щелкните **Домены** в меню верхнего уровня, а затем щелкните **Добавить пользовательский домен**.
     
       ![Добавить пользовательский домен][17]
   * Введите имя домена в поле **Доменное имя** . Необходимо ввести имя, которое вы будете использовать в Google Apps. Когда все будет готово, нажмите кнопку **Добавить** .
     
       ![Ввод доменного имени][18]
   * Нажмите кнопку **Далее**, чтобы перейти на страницу проверки. Для подтверждения того, что домен принадлежит вам, необходимо изменить его записи DNS в соответствии со значениями, приведенными на этой странице. Вы можете выполнить проверку с помощью **записей MX** или **записей типа TXT**. Для этого выберите соответствующее значение в поле **Тип записи**. Более подробные инструкции по подтверждению доменного имени в Azure AD см. в разделе [Добавление собственного имени домена в Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
       ![Подтверждение доменного имени][19]
   * Повторите описанные выше действия для всех доменов, которые вы хотите добавить в свой каталог.
5. После подтверждения всех доменов в Azure AD необходимо подтвердить их в Google Apps. Для каждого домена, который еще не зарегистрирован в Google Apps, выполните следующие действия.
   
   * В [консоли администратора Google Apps](http://admin.google.com/)щелкните **Domains**(Домены).
     
       ![Выбор элемента "Домены"][20]
   * Щелкните **Add a domain or a domain alias**(Добавить домен или псевдоним домена).
     
       ![Добавление нового домена][21]
   * Выберите команду **Add another domain**(Добавить другой домен) и введите имя домена, который нужно добавить.
     
       ![Ввод доменного имени][22]
   * Щелкните **Continue and verify domain ownership** (Перейти к проверке владельца домена). Затем выполните действия, подтверждающие, что вы владеете доменным именем. Подробные инструкции по проверке домена в Google Apps см. на странице [Подтверждение права собственности на сайт в Google Apps](https://support.google.com/webmasters/answer/35179).
   * Повторите описанные выше шаги для других доменов, которые нужно добавить в Google Apps.
     
     > [!WARNING]
     > Если вы измените основной домен клиента Google Apps после того, как настроите единый вход в Azure AD, вам необходимо будет повторить шаг 3, описанный в разделе [Шаг 2. Настройка единого входа](#step-two-enable-single-sign-on).
     > 
     > 
6. В [Консоли администратора Google Apps](http://admin.google.com/)щелкните **Admin Roles (Административные роли)**.
   
    ![Выбор Google Apps][26]
7. Определите, какую учетную запись администратора вы хотите использовать для управления подготовкой пользователей. Чтобы назначить этой учетной записи **административную роль**, измените для этой роли свойство **Privileges** (Привилегии). Убедитесь, что для учетной записи выбраны все параметры в разделе **Admin API Privileges** (Административные права доступа к API). Это позволит использовать ее для подготовки.
   
    ![Выбор Google Apps][27]
   
   > [!NOTE]
   > При настройке рабочей среды рекомендуется создать новую учетную запись администратора Google Apps специально для этого шага. Этой учетной записи должна быть назначена административная роль с необходимыми правами доступа к API.
   > 
   > 
8. В Azure Active Directory выберите **Приложения** в меню верхнего уровня, а затем щелкните **Google Apps**.
   
    ![Выбор Google Apps][23]
9. На странице быстрого запуска Google Apps нажмите кнопку **Настроить подготовку учетных записей пользователей**.
   
    ![Настроить подготовку учетных записей пользователей][24]
10. В открывшемся диалоговом окне нажмите кнопку **Включить подготовку пользователей** , чтобы проверить подлинность учетной записи администратора Google Apps, которую вы хотите использовать для управления подготовкой.
    
    ![Включение подготовки][25]
11. Подтвердите, что вы готовы предоставить Azure Active Directory разрешение на внесение изменений в клиент Google Apps.
    
    ![Подтверждение разрешения][28]
12. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно.

## <a name="step-4-assign-users-to-google-apps"></a>Шаг 4. Назначение пользователей Google Apps
1. Чтобы проверить конфигурацию, создайте тестовую учетную запись в каталоге.
2. На странице быстрого запуска Google Apps нажмите кнопку **Назначить пользователей** .
   
    ![Щелкните "Назначить пользователей".][29]
3. Выберите тестового пользователя и нажмите кнопку **Назначить** в нижней части экрана.
   
   * Если вы еще не включили автоматическую подготовку пользователей, то появится следующее сообщение:
     
        ![Подтвердите назначение.][30]
   * Если автоматическая подготовка пользователей включена, появится запрос с предложением выбрать тип роли, которую нужно назначить пользователю в Google Apps. Через несколько минут подготовленные пользователи появятся в вашей среде Google Apps.
4. Чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com/), войдите с тестовой учетной записью и щелкните **Google Apps**.

## <a name="related-articles"></a>Связанные статьи
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Список учебников по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png



<!--HONumber=Nov16_HO4-->


