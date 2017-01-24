---
title: "Руководство по интеграции Azure Active Directory с NetSuite | Документация Майкрософт"
description: "Сведения об использовании NetSuite вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 74ef08108a5ff27a50f928781f906b6d769f1085


---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Руководство по интеграции Azure Active Directory с NetSuite
В этом руководстве показано, как подключить среду NetSuite к Azure Active Directory. Вы узнаете, как настроить единый вход в NetSuite, включить автоматическую подготовку пользователей и предоставить пользователям доступ к NetSuite. 

## <a name="prerequisites"></a>Предварительные требования
1. Для доступа к Azure Active Directory через [классический портал Azure](https://manage.windowsazure.com)необходима действующая подписка Azure.
2. Требуется административный доступ к подписке [NetSuite](http://www.netsuite.com/portal/home.shtml). Для любой из этих служб можно воспользоваться бесплатной пробной учетной записью.

## <a name="step-1-add-netsuite-to-your-directory"></a>Этап 1. Добавление NetSuite в каталог
1. На [классическом портале Azure](https://manage.windowsazure.com) в области навигации слева щелкните **Active Directory**.
   
    ![Выберите Active Directory в области навигации слева.][0]
2. В списке **Каталог** выберите каталог, в который хотите добавить NetSuite.
3. Щелкните **Приложения** в верхнем меню.
   
    ![Щелкните "Приложения".][1]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Нажмите кнопку "Добавить", чтобы добавить новое приложение.][2]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Нажмите кнопку "Добавить приложение из коллекции".][3]
6. В **поле поиска** введите **NetSuite**. Выберите **NetSuite** из списка результатов и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Добавление NetSuite][4]
7. Вы увидите страницу быстрого запуска NetSuite.
   
    ![Страница быстрого запуска NetSuite в Azure AD][5]

## <a name="step-2-enable-single-sign-on"></a>Шаг 2. Включение единого входа
1. На странице быстрого запуска NetSuite в Azure AD нажмите кнопку **Настройка единого входа**.
   
    ![Кнопка "Настроить единый вход"][6]
2. Откроется диалоговое окно, и вы увидите экран с вопросом "Как пользователи должны входить в NetSuite?". Выберите **Единый вход Azure AD**, а затем нажмите кнопку **Далее**.
   
    ![Выберите "Единый вход Azure AD".][7]
   
   > [!NOTE]
   > Для получения дополнительных сведений о вариантах единого входа [щелкните здесь](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. На странице **Configure App Settings** (Настройка параметров приложения) введите в поле **URL-адрес ответа** URL-адрес клиента NetSuite в одном из следующих форматов:
   
   * `https://<tenant-name>.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na1.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na2.netsuite.com/saml2/acs`
   * `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`
     
     ![Введите URL-адрес клиента.][8]
4. На странице **Настройка единого входа в NetSuite** щелкните **Скачать метаданные**, а затем сохраните файл сертификата на локальном компьютере.
   
    ![Загрузите метаданные.][9]
5. Откройте новую вкладку в браузере и войдите как администратор корпоративного сайта NetSuite.
6. На панели инструментов в верхней части страницы щелкните **Setup** (Настройка) и выберите **Setup Manager** (Диспетчер настройки).
   
    ![Перейдите в диспетчер настройки][10]
7. Из списка **Setup Tasks** (Задачи настройки) выберите пункт **Integration** (Интеграция).
   
    ![Перейдите в раздел "Интеграция"][11]
8. В разделе **Manage Authentication** (Управление аутентификацией) щелкните **SAML Single Sign-on** (Единый вход на основе SAML).
   
    ![Перейдите в раздел "Единый вход SAML"][12]
9. На странице **Настройка SAML** выполните следующие действия.
   
   * В Azure Active Directory скопируйте значение из поля **URL-адрес удаленного входа** и вставьте его в поле **Identity Provider Login Page** (Страница входа поставщика удостоверений) в NetSuite.
     
       ![Страница настройки SAML.][13]
   * В NetSuite выберите раздел **Primary Authentication Method** (Основной метод проверки подлинности).
   * В поле **SAMLV2 Identity Provider Metadata** (Метаданные поставщика удостоверений на основе SAMLv2) выберите **Upload IDP Metadata File** (Передать файл метаданных поставщика удостоверений). Затем нажмите кнопку **Обзор** , чтобы добавить файл метаданных, загруженный на шаге 4.
     
       ![Загрузите метаданные][16]
   * Нажмите кнопку **Submit**(Отправить).
10. В Azure AD установите флажок подтверждения настройки единого входа, чтобы включить сертификат, который вы загрузили в NetSuite. Нажмите кнопку **Далее**.
    
     ![Установите флажок подтверждения.][14]
11. Если вы хотите получать уведомления об ошибках и предупреждения, связанные с обслуживанием конфигурации единого входа, введите адрес электронной почты на последней странице. 
    
    ![Введите адрес электронной почты.][15]
12. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно. Затем щелкните **Атрибуты** в верхней части страницы.
    
    ![Щелкните "Атрибуты".][17]
13. Щелкните **Добавить атрибут пользователя**.
    
    ![Щелкните "Добавить атрибут пользователя".][18]
14. В поле **Имя атрибута** введите `account`. В поле **Значение атрибута** введите идентификатор учетной записи NetSuite. Инструкции по поиску идентификатора учетной записи приведены ниже.
    
    ![Добавьте идентификатор учетной записи NetSuite.][19]
    
    * В верхнем меню навигации NetSuite щелкните **Setup** (Настройка).
    * Щелкните **Setup Tasks** (Задачи настройки) в меню навигации слева, выберите раздел **Integration** (Интеграция), а затем щелкните **Web Services Preferences** (Параметры веб-служб).
    * Скопируйте идентификатор своей учетной записи NetSuite и вставьте его в поле **Значение атрибута** в Azure AD.
      
        ![Получите идентификатор учетной записи][20]
15. В Azure AD нажмите кнопку **Завершить** , чтобы завершить добавление атрибута SAML. Нажмите кнопку **Применить изменения** в нижнем меню.
    
    ![Сохраните изменения.][21]
16. Прежде чем пользователи смогут выполнять единый вход в NetSuite, необходимо назначить им соответствующие разрешения в NetSuite. Чтобы предоставить необходимые разрешения, выполните следующие инструкции.
    
    * В верхнем меню навигации выберите **Setup** (Настройка) и щелкните **Setup Manager** (Диспетчер настройки).
      
        ![Перейдите в диспетчер настройки][10]
    * В меню навигации слева выберите **Users/Roles** (Пользователи и роли), а затем — **Manage Roles** (Управление ролями).
      
        ![Перейдите в раздел "Управление ролями"][22]
    * Нажмите кнопку **Создать роль**.
    * Введите **имя** новой роли и установите флажок **Single Sign-On Only** (Только единый вход).
      
        ![Присвойте имя новой роли.][23]
    * Щелкните **Сохранить**.
    * В верхнем меню щелкните **Разрешения**. Затем щелкните **Настройка**.
      
        ![Перейдите в раздел "Разрешения"][24]
    * Выберите **Set Up SAM Single Sign-on** (Настройка единого входа управления лицензиями) и нажмите кнопку **Add** (Добавить).
    * Щелкните **Сохранить**.
    * В верхнем меню навигации выберите **Setup** (Настройка) и щелкните **Setup Manager** (Диспетчер настройки).
      
        ![Перейдите в диспетчер настройки][10]
    * В меню навигации слева выберите **Users/Roles** (Пользователи и роли), а затем — **Manage Users** (Управление пользователями).
      
        ![Перейдите в раздел "Управление пользователями"][25]
    * Выберите тестового пользователя. Нажмите кнопку **Изменить**.
      
        ![Перейдите в раздел "Управление пользователями"][26]
    * В диалоговом окне "Роли" выберите роль, которую вы создали, и нажмите кнопку **Добавить**.
      
        ![Перейдите в раздел "Управление пользователями"][27]
    * Щелкните **Сохранить**.
17. Сведения о проверке конфигурации см. в разделе [Назначение пользователей NetSuite](#step-4-assign-users-to-netsuite) ниже.

## <a name="step-3-enable-automated-user-provisioning"></a>Шаг 3. Включение автоматической подготовки пользователей
> [!NOTE]
> По умолчанию подготовленные пользователи будут добавлены в дочерний корневой раздел среды NetSuite.
> 
> 

1. На странице быстрого запуска NetSuite в Azure Active Directory нажмите кнопку **Настроить подготовку учетных записей пользователей**.
   
    ![Настроить подготовку учетных записей пользователей][28]
2. В открывшемся диалоговом окне введите учетные данные администратора NetSuite, а затем нажмите кнопку **Далее**.
   
    ![Введите учетные данные администратора в NetSuite.][29]
3. На странице подтверждения введите свой адрес электронной почты для получения уведомлений о сбоях при подготовке.
   
    ![Подтвердите выбор.][30]
4. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно.

## <a name="step-4-assign-users-to-netsuite"></a>Этап 4. Назначение пользователей NetSuite
1. Чтобы проверить конфигурацию, создайте тестовую учетную запись в каталоге.
2. На странице быстрого запуска NetSuite нажмите кнопку **Назначить пользователей**.
   
    ![Щелкните "Назначить пользователей".][31]
3. Выберите тестового пользователя и нажмите кнопку **Назначить** в нижней части экрана.
   
   * Если вы еще не включили автоматическую подготовку пользователей, то появится следующее сообщение:
     
        ![Подтвердите назначение.][32]
   * Если автоматическая подготовка пользователей включена, появится запрос на выбор роли, которую нужно назначить пользователю в NetSuite. Через несколько минут подготовленные пользователи появятся в вашей среде NetSuite.
4. Чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com/), выполните вход с тестовой учетной записью и щелкните **NetSuite**.

## <a name="related-articles"></a>Связанные статьи
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Список учебников по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png



<!--HONumber=Dec16_HO2-->


