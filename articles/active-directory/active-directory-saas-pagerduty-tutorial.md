---
title: "Руководство по интеграции Azure Active Directory с PagerDuty | Документация Майкрософт"
description: "Узнайте, как использовать PagerDuty с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b473d7edb531f4fdab9c2ea8bde969e37558072


---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Учебник. Интеграция Azure Active Directory с PagerDuty
Цель данного учебника — показать интеграцию Azure и PagerDuty.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент PagerDuty.

По завершении работы с этим руководством пользователи Azure AD, назначенные в PagerDuty, будут иметь возможность единого входа в приложение на веб-сайте PagerDuty компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для PagerDuty
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")

## <a name="enabling-the-application-integration-for-pagerduty"></a>Включение интеграции приложений для PagerDuty
В этом разделе показано, как включить интеграцию приложений для PagerDuty.

### <a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для PagerDuty, выполните следующие действия:
1. На портале управления Azure в левой области навигации нажмите **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **PagerDuty**.
   
   ![Коллекция приложений](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application gallery")
7. В области результатов выберите **PagerDuty** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в PagerDuty со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **PagerDuty** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в PagerDuty?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в PagerDuty** введите свой URL-адрес, используя следующий шаблон *https://\<имя-клиента\>.Pagerduty.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configure app url")
4. На странице **Настройка единого входа в PagerDuty** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")
5. В другом окне браузера войдите на свой сайт PagerDuty компании в качестве администратора.
6. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
   ![Параметры учетной записи](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")
7. Щелкните **Единый вход**.
   
   ![Единый вход](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")
8. На странице «Включение единого входа» выполните следующие действия:
   
   ![Включение единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Enable single sign-on")
   
   1. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   2. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** .
   3. На странице **Настройка единого входа в PagerDuty** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес входа**.
   4. На странице **Настройка единого входа в PagerDuty** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода**.
   5. Установите флажок **Включить единый вход**.
   6. Нажмите кнопку **Сохранить изменения**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в PagerDuty, они должны быть подготовлены для PagerDuty.  
В случае с PagerDuty подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход в клиент **PagerDuty** .
2. В меню в верхней части страницы щелкните **Пользователи**.
3. Щелкните **Добавить пользователей**.
   
   ![Добавление пользователей](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Add Users")
4. В диалоговом окне **Invite your team** (Приглашение команды) заполните текстовые поля **First and Last Name** (Имя и фамилия) и **Email** (Адрес электронной почты) данными пользователя Azure AD, которого необходимо подготовить, щелкните **Add** (Добавить) и нажмите кнопку **Send Invites** (Отправить приглашения).
   
   ![Приглашение команды](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invite your team")
   
   > [!NOTE]
   > Все добавленные пользователи получат приглашение создать учетную запись PagerDuty.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя PagerDuty или API, предоставляемые PagerDuty для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Чтобы назначить пользователей PagerDuty, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **PagerDuty** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


