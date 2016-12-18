---
title: "Руководство по интеграции Azure Active Directory с Thoughtworks Mingle | Документация Майкрософт"
description: "Узнайте, как использовать Thoughtworks Mingle вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6318f2fad781118b0f2c364d1cbc2b686f46a95e


---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Учебник. Интеграция Azure Active Directory с Thoughtworks Mingle
Цель данного учебника — показать интеграцию Azure и Thoughtworks Mingle.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Thoughtworks Mingle

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Thoughtworks Mingle
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenario")

## <a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Включение интеграции приложений для Thoughtworks Mingle
В этом разделе показано, как включить интеграцию приложений для Thoughtworks Mingle.

### <a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Thoughtworks Mingle, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **thoughtworks mingle**.
   
   ![Коллекция приложений](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Application Gallery")
7. В области результатов выберите **Thoughtworks Mingle** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![thoughtworks mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Thoughtworks Mingle с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат в Thoughtworks Mingle.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Thoughtworks Mingle** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configure single sign-on")
2. На странице **Как пользователи будут входить в Thoughtworks Mingle** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес клиента Thoughtworks Mingle** введите свой URL-адрес в формате *http://company.mingle.thoughtworks.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configure App URL")
4. На странице **Настройка единого входа в Thoughtworks Mingle** нажмите кнопку "Загрузить метаданные", а затем сохраните эти данные локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configure single sign-on")
5. Войдите на корпоративный веб-сайт **Thoughtworks Mingle** в качестве администратора.
6. Откройте вкладку **Admin** (Администратор), а затем щелкните **SSO Config** (Конфигурация единого входа).
   
   ![Конфигурация единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")
7. В разделе **Конфигурация единого входа** выполните следующие действия.
   
   ![Конфигурация единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")
   
   1. Чтобы отправить файл метаданных, нажмите кнопку **Выбрать файл**.
   2. Нажмите кнопку **Сохранить изменения**.
8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи AAD могли войти систему, они должны быть подготовлены для приложения Thoughtworks Mingle с использованием их имен пользователей Azure Active Directory.  
В случае с Thoughtworks Mingle подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите на корпоративный веб-сайт Thoughtworks Mingle в качестве администратора.
2. Щелкните **Профиль**.
   
   ![Ваш первый проект](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Your First Project")
3. Откройте вкладку **Admin** (Администратор), а затем щелкните **Users** (Пользователи).
   
   ![Пользователи](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Users")
4. Щелкните **Новый пользователь**.
   
   ![Новый пользователь](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "New User")
5. На странице диалогового окна **Новый пользователь** выполните следующие действия.
   
   ![Новый пользователь](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "New User")
   
   1. Введите в текстовые поля **Sign-in name** (Учетное имя), **Display name** (Отображаемое имя), **Choose password** (Выберите пароль) и **Confirm password** (Подтвердите пароль) соответствующие данные действующей учетной записи AAD, которую вы хотите подготовить.
   2. В поле **User type** (Тип пользователя) выберите значение **Full user** (С полным доступом).
   3. Щелкните **Создать этот профиль**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Thoughtworks Mingle или API-интерфейсы, предоставляемые Thoughtworks Mingle для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Чтобы назначить пользователей Thoughtworks Mingle, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Thoughtworks Mingle** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


