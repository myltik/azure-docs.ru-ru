---
title: "Учебник. Интеграция Azure Active Directory с Citrix ShareFile | Документация Майкрософт"
description: "Узнайте, как использовать Citrix ShareFile вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 30f5ed9a5e9371193ef7663cdd8896793ca6359b


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Учебник. Интеграция Azure Active Directory с Citrix ShareFile
Цель данного учебника — показать интеграцию Azure и Citrix ShareFile.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Citrix ShareFile

После выполнения действий, описанных в этом руководстве, пользователи Azure AD, которых вы прикрепите к Citrix ShareFile, смогут использовать единый вход в приложение на веб-сайте Citrix ShareFile вашей организации (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Citrix ShareFile
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")

## <a name="enabling-the-application-integration-for-citrix-sharefile"></a>Включение интеграции приложений для Citrix ShareFile
В этом разделе показано, как включить интеграцию приложений для Citrix ShareFile.

### <a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Citrix ShareFile, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Citrix ShareFile**.
   
   ![Коллекция приложений](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Application gallery")
7. В области результатов выберите **Citrix ShareFile** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Citrix ShareFile со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Citrix ShareFile** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Доступ с единым входом](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Enable single sign-on")
2. На странице **Как пользователи должны входить в Citrix ShareFile?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** введите в текстовое поле **URL-адрес входа в Citrix ShareFile** свой URL-адрес в формате `https://<tenant-name>.shareFile.com` и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configure App URL")
4. Для скачивания сертификата на странице **Настройка единого входа в Citrix ShareFile** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **Citrix ShareFile** в качестве администратора.
6. На панели инструментов в верхней части экрана нажмите **Администратор**.
7. В левой области навигации нажмите **Настройка единого входа**.
   
   ![Администрирование учетной записи](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")
8. На странице диалогового окна **Single Sign-On/ SAML 2.0 Configuration** (Настройка единого входа или SAML 2.0) в разделе **Основные параметры** выполните следующие действия:
   
   ![Единый вход](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")
   
   1. Выберите команду **Enable SAML**(Включить SAML).
   2. На странице диалогового окна **Настройка единого входа в Citrix ShareFile** классического портала Azure скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **Your IDP Issuer/ Entity ID** (Идентификатор издателя или сущности поставщика удостоверений).
   3. На странице диалогового окна **Настройка единого входа в Citrix ShareFile** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login URL** (URL-адрес для входа).
   4. На странице диалогового окна **Настройка единого входа в Citrix ShareFile** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).
   5. Нажмите **Изменить** рядом с полем **Сертификат X.509** и передайте сертификат, скачанный с классического портала Azure AD.
      ![Основные параметры](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")
9. Нажмите **Сохранить** на портале управления Citrix ShareFile.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Citrix ShareFile, они должны быть подготовлены для Citrix ShareFile.  
В случае с Citrix ShareFile подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Войдите в клиент **Citrix ShareFile** .
2. Последовательно выберите пункты **Manage Users \> Manage Users Home \> + Create Employee** (Управление пользователями > Управление домашним ресурсом пользователей > + Создать сотрудника).
   
   ![Создать сотрудника](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")
3. Введите в поля **Email** (Адрес электронной почты), **First name** (Имя) и **Last name** (Фамилия) соответствующие данные действующей учетной записи Azure AD, которую необходимо подготовить.
   
   ![Основные сведения](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")
4. Нажмите кнопку **Добавить пользователя**.
   
   > [!NOTE]
   > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетной записи пользователя Citrix ShareFile или API, предоставляемые Citrix ShareFile для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Чтобы назначить пользователей Citrix ShareFile, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Citrix ShareFile ** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


