---
title: "Руководство. Интеграция Azure Active Directory с Syncplicity | Документация Майкрософт"
description: "Узнайте, как использовать Syncplicity вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d8464b1ca5051b2bdac61f0e637ed1b2ff3df3ab


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Учебник. Интеграция Azure Active Directory с Syncplicity
Цель этого руководства — продемонстрировать, как настроить единый вход между Azure Active Directory (AAD) и Syncplicity.

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Syncplicity

По завершении работы с этим руководством пользователи AAD, которым будет назначен доступ к Syncplicity, смогут выполнять единый вход в приложение на веб-сайте Syncplicity вашей компании (вход, инициированный поставщиком услуг) или с помощью панели доступа AAD.

1. Включение интеграции приложений для Syncplicity
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scenario")

## <a name="enabling-the-application-integration-for-syncplicity"></a>Включение интеграции приложений для Syncplicity
В этом разделе рассматривается включение интеграции приложений для Syncplicity.

### <a name="to-enable-the-application-integration-for-syncplicity-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Syncplicity, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Syncplicity**.
   
   ![Коллекция приложений Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity application gallery")
7. В области результатов выберите **Syncplicity** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности на Syncplicity с помощью своей учетной записи Azure Active Directory, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Syncplicity** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Syncplicity** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Единый вход Microsoft Azure AD](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD Single Sign-On")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в Syncplicity** введите URL-адрес, используемый пользователями для входа в приложение Syncplicity, и щелкните **Далее**. 
   
   URL-адресом приложения является URL-адрес клиента Syncplicity (например, *http://company.Syncplicity.com*).
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configure app URL")
4. На странице **Настройка единого входа в Syncplicity** нажмите кнопку **Скачать сертификат**, чтобы скачать сертификат, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configure single sign-on")
5. Войдите в клиент **Syncplicity** .
6. В меню в верхней части страницы щелкните **Администрирование**, выберите **Параметры**, а затем — **Личный домен и единый вход**.
   
   ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")
7. На странице диалогового окна **Единый вход** сделайте следующее:
   
   ![Единый вход \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \\\(SSO\\\)")
   
   1. В текстовом поле **Личный домен** введите имя своего домена.
   2. В поле **Состояния единого входа** задайте значение **Включено**.
   3. На странице **Настройка единого входа в Syncplicity** классического портала Azure скопируйте значение **Идентификатор сущности**, а затем вставьте его в текстовое поле **Идентификатор сущности**.
   4. На странице диалогового окна **Настройка единого входа в Syncplicity** классического портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес страницы входа**.
   5. На странице **Настройка единого входа в Syncplicity** классического портала Azure скопируйте значение **URL-адрес удаленного выхода**, а затем вставьте его в текстовое поле **Logout page URL** (URL-адрес страницы выхода).
   6. Напротив пункта **Identity Provider Certificate** (Сертификат поставщика удостоверений) нажмите кнопку **Выбрать файл**, а затем передайте сертификат, скачанный с классического портала Azure.
   7. Нажмите кнопку **Сохранить изменения**.
8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Подтверждение](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmation")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи AAD могли входить систему, их необходимо подготовить для Syncplicity. В этом разделе описывается порядок создания учетных записей пользователей AAD в Syncplicity.

### <a name="to-provision-a-user-account-to-syncplicity-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей для Syncplicity, выполните следующие действия:
1. Войдите в клиент **Syncplicity** (например, по адресу *https://<название_компании>.Syncplicity.com*).
2. Щелкните **Администрирование** и выберите **Учетные записи пользователей**.
3. Нажмите кнопку **Добавить пользователя**.
   
   ![Управление пользователями](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Manage Users")
4. Введите **адрес электронной почты** учетной записи AAD, которую необходимо подготовить, задайте значение **Пользователь** в поле **Роль**, а затем нажмите кнопку **Далее**.
   
   ![Сведения об учетной записи](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Account Information")
   
   > [!NOTE]
   > Владелец учетной записи AAD получит электронное сообщение, содержащее ссылку для подтверждения и активации учетной записи.
   > 
   > 
5. Выберите группу в организации, в которую будет входить новый пользователь, и нажмите кнопку **Далее**.
   
   ![Членство в группе](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Group Membership")
   
   > [!NOTE]
   > Если список групп пуст, просто нажмите кнопку **Далее**.
   > 
   > 
6. Выберите папки, которые будут находиться под управлением Syncplicity на компьютере пользователя, и нажмите кнопку **Далее**.
   
   ![Папки Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity Folders")

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Syncplicity или API-интерфейсы, предоставляемые Syncplicity для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-syncplicity-perform-the-following-steps"></a>Чтобы назначить пользователей Syncplicity, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Syncplicity** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


