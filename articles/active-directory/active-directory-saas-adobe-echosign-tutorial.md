---
title: "Руководство по интеграции Azure Active Directory с Adobe EchoSign | Документация Майкрософт"
description: "Узнайте, как использовать Adobe EchoSign вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63c93a9f6812607cf8ce98f4f32bcb2c8b8823c3


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Руководство. Интеграция Azure Active Directory с Adobe EchoSign
Цель данного учебника — показать интеграцию Azure и Adobe EchoSign.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа в Adobe EchoSign

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, назначенные Adobe EchoSign, смогут выполнять единый вход в приложение на корпоративном веб-сайте Adobe EchoSign (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Adobe EchoSign
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scenario")

## <a name="enabling-the-application-integration-for-adobe-echosign"></a>Включение интеграции приложений для Adobe EchoSign
В этом разделе показано, как включить интеграцию приложений для Adobe EchoSign.

### <a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Adobe EchoSign, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Adobe EchoSign**.
   
   ![Коллекция приложений](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Application Gallery")
7. В области результатов выберите **Adobe EchoSign** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Adobe EchoSign со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Adobe EchoSign** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Adobe EchoSign?** выберите **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес для входа в Adobe EchoSign** введите свой URL-адрес в формате *https://company.echosign.com/* и щелкните **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configure App URL")
4. На странице **Настройка единого входа в Adobe EchoSign** щелкните **Загрузить сертификат** и сохраните файл сертификата локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите в свой корпоративный веб-сайт Adobe EchoSign в качестве администратора.
6. В меню в верхней части экрана выберите **Account** (Учетная запись), а затем в области навигации с левой стороны выберите **SAML Settings** (Параметры SAML) в разделе **Account Settings** (Параметры учетной записи).
   
   ![Учетная запись.](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Account")
7. В разделе «Параметры SAML» выполните следующие действия.
   
   ![Настройка SAML](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML Settings")
   
   1. В разделе **SAML Mode** (Режим SAML) выберите параметр **SAML Mandatory** (SAML обязательно).
   2. Установите флажок **Allow EchoSign Account Administrators to log in using their EchoSign Credentials**(Разрешить администраторам учетных записей EchoSign вход с использованием учетных данных EchoSign).
   3. В разделе **User Creation** (Создание пользователей) установите флажок **Automatically add users authenticated through SAML** (Автоматически добавлять пользователей, прошедших проверку подлинности с использованием SAML).
8. После этого выполните следующие действия.
   
   ![Настройка SAML](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML Settings")
   
   1. На классическом портале Azure на диалоговой странице **Настройка единого входа в Adobe EchoSign** скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **IdP Entity ID** (Идентификатор сущности IdP).
   2. На классическом портале Azure на диалоговой странице **Настройка единого входа в Adobe EchoSign** скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **IdP Login URL** (URL-адрес входа для IdP).
   3. На классическом портале Azure на диалоговой странице **Настройка единого входа в Adobe EchoSign** скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **IdP Logout URL** (URL-адрес выхода для IdP).
   4. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. Откройте сертификат в кодировке Base-64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **IdP Certificate** (Сертификат IdP).
   6. Нажмите кнопку **Сохранить изменения**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли входить в Adobe EchoSign, они должны быть подготовлены для Adobe EchoSign.  
В случае Adobe EchoSign подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход на корпоративный веб-сайт **Adobe EchoSign** в качестве администратора.
2. В меню в верхней части экрана щелкните **Account** (Учетная запись), а затем в области навигации слева выберите **Users & Groups** (Пользователи и группы), а затем — **Create a new user** (Создать пользователя).
   
   ![Учетная запись.](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Account")
3. В разделе **Создание нового пользователя** выполните следующие действия.
   
   ![Создать пользователя](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Create User")
   
   1. Введите в текстовые поля **Email Address** (Адрес электронной почты), **First Name** (Имя) и **Last Name** (Фамилия) соответствующие данные действующей учетной записи AAD, которую нужно подготовить.
   2. Щелкните **Создать пользователя**.
      
      > [!NOTE]
      > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.
      > 
      > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Adobe EchoSign или API, предоставляемые Adobe EchoSign для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Чтобы назначить пользователей Adobe EchoSign, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Adobe EchoSign** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


