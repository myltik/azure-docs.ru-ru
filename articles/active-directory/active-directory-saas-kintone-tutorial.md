---
title: "Учебник. Интеграция Azure Active Directory с Kintone | Документация Майкрософт"
description: "Узнайте, как использовать Kintone вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a25e030e8ade95db2ac9cd58fe4d94bbb7775ba


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Руководство. Интеграция Azure Active Directory с Kintone
Цель данного руководства — показать интеграцию Azure и Kintone.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Kintone

По завершении работы с этим руководством пользователи Azure AD, назначенные в Kintone, смогут выполнять единый вход в приложение на веб-сайте Kintone компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Kintone
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")

## <a name="enabling-the-application-integration-for-kintone"></a>Включение интеграции приложений для Kintone
В этом разделе показано, как включить интеграцию приложений для Kintone.

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Kintone, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6. В **поле поиска** введите **Kintone**.
   
    ![Коллекция приложений](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7. В области результатов выберите **Kintone** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Kintone со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Kintone** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2. На странице **Как пользователи должны входить в Kintone?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Kintone** введите свой URL-адрес в формате *https://компания.kintone.com*, а затем нажмите кнопку **Далее**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4. Для скачивания сертификата на странице **Настройка единого входа в Kintone** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5. В другом окне веб-браузера войдите на веб-сайт **Kintone** компании в качестве администратора.

6. Щелкните **Параметры**.
   
    ![Параметры](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7. Щелкните **Users & System Administration** (Администрирование пользователей и системы).
   
    ![Администрирование пользователей и системы](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8. Перейдите в раздел **System Administration \> Security** (Системное администрирование > Безопасность) и щелкните **Login** (Вход).
   
    ![Вход](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9. Установите флажок **Включить проверку подлинности SAML**.
   
    ![Проверка подлинности SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. В разделе «Проверка подлинности SAML» выполните следующие действия.
    
    ![Проверка подлинности SAML](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")
    
    1. На странице диалогового окна **Настройка единого входа в Kintone** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login URL** (URL-адрес для входа).
   
    2. На странице диалогового окна **Настройка единого входа в Kintone** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).
    
    3. Чтобы передать скачанный сертификат, нажмите кнопку **Обзор** .
    
    4. Щелкните **Сохранить**.

11. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в Kintone, они должны быть подготовлены для Kintone.  
В случае с Kintone подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход на веб-сайт **Kintone** компании в качестве администратора.

2. Щелкните **Параметр**.
   
    ![Параметры](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3. Щелкните **Users & System Administration** (Администрирование пользователей и системы).
   
    ![Администрирование пользователей и системы](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4. В разделе **User Administration** (Администрирование пользователей) щелкните **Departments & Users** (Отделы и пользователи).
   
    ![Отделы и пользователи](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5. Щелкните **Новый пользователь**.
   
    ![Новые пользователи](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6. В разделе **New User** (Новый пользователь) выполните следующие действия.
   
    ![Новые пользователи](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")
   
    1. В соответствующие текстовые поля введите атрибуты **Display Name** (Отображаемое имя), **Login Name** (Имя входа), **New Password** (Новый пароль), **Confirm Password** (Подтверждение пароля), **E-mail Address** (Адрес электронной почты) и другие данные действующей учетной записи AAD, которую вы хотите подготовить.
 
    2. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Kintone или API, предоставляемые Kintone для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Чтобы назначить пользователей Kintone, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **Kintone** нажмите кнопку **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


