---
title: "Руководство. Интеграция Azure Active Directory с AppDynamics | Документация Майкрософт"
description: "Узнайте, как использовать AppDynamics вместе с Azure Active Directory для настройки единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13f4e780afc26dc0f1f59408a77f68a7e46308c7


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Руководство. Интеграция Azure Active Directory с AppDynamics
Цель данного учебника — показать интеграцию Azure и AppDynamics. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа AppDynamics

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к AppDynamics, смогут выполнять единый вход в приложение на корпоративном веб-сайте AppDynamics (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для AppDynamics
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>Включение интеграции приложений для AppDynamics
В этом разделе показано, как включить интеграцию приложений для AppDynamics.

### <a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для AppDynamics, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **AppDynamics**.
   
   ![Коллекция приложений](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. В области результатов выберите **AppDynamics** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в AppDynamics со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **AppDynamics** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. На странице **Как пользователи должны входить в AppDynamics?** выберите параметр **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. На странице **Configure App URL** (Настройка URL-адреса приложения) в текстовом поле **AppDynamics Sign On URL** (URL-адрес входа в AppDynamics) введите URL-адрес, используемый для входа в AppDynamics (например, *https://companyname.saas.appdynamics.com*), и щелкните **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. Чтобы скачать сертификат, на странице **Настройка единого входа в AppDynamics** щелкните **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт AppDynamics в качестве администратора.
6. Выберите меню **Settings** (Параметры) на панели инструментов в верхней части экрана и щелкните пункт **Administration** (Администрирование).
   
   ![Администрирование](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. Откройте вкладку **Authentication Provider** (Поставщик проверки подлинности).
   
   ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. В разделе **Authentication Provider** (Поставщик проверки подлинности) выполните следующие действия.
   
   ![Настройка SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")
   
   1. Для параметра **Authentication Provider** (Поставщик проверки подлинности) выберите значение **SAML**.
   2. На классическом портале Azure на диалоговой странице **Настройка единого входа в AppDynamics** скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовом поле **URL-адрес входа**.
   3. На классическом портале Azure на диалоговой странице **Настройка единого входа в AppDynamics** скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода**.
   4. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать в видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат** .
   6. Щелкните **Сохранить**.
      ![Сохранить](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в AppDynamics, они должны быть подготовлены для AppDynamics.  
В случае с AppDynamics подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите на свой корпоративный веб-сайт AppDynamics в качестве администратора.
2. Откройте раздел **Users** (Пользователи) и щелкните значок **+**, чтобы открыть диалоговое окно **Create User** (Создание пользователя).
   
   ![Пользователи](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. В разделе **Создание пользователя** выполните следующие действия.
   
   ![Создать пользователя](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. Введите в текстовых полях **Username** (Имя пользователя), **Name** (Имя), **Email** (Адрес электронной почты), **New Password** (Новый пароль) и **Repeat New Password** (Введите новый пароль еще раз) соответствующие данные действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя AppDynamics или API, предоставляемые AppDynamics для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Чтобы назначить пользователей AppDynamics, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **AppDynamics** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


