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
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 400793331aa2d56358a83a51ce64c67f59bbf3b7
ms.openlocfilehash: d6ce5ac0207ac94a4eb8e6dccedd36c7cb446d70
ms.lasthandoff: 02/16/2017


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

![Сценарий](./media/active-directory-saas-kintone-tutorial/IC785859.png "Сценарий")

## <a name="enabling-the-application-integration-for-kintone"></a>Включение интеграции приложений для Kintone
В этом разделе показано, как включить интеграцию приложений для Kintone.

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Kintone, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-kintone-tutorial/IC700994.png "Приложения")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавление приложения](./media/active-directory-saas-kintone-tutorial/IC749321.png "Добавление приложения")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавление приложения из коллекции](./media/active-directory-saas-kintone-tutorial/IC749322.png "Добавление приложения из коллекции")

6. В **поле поиска** введите **Kintone**.
   
    ![Коллекция приложений](./media/active-directory-saas-kintone-tutorial/IC785867.png "Коллекция приложений")

7. В области результатов выберите **Kintone** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Kintone со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Kintone** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785872.png "Настройка единого входа")

2. На странице **Как пользователи должны входить в Kintone?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785873.png "Настройка единого входа")

3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Kintone** введите свой URL-адрес в формате *https://компания.kintone.com*, а затем нажмите кнопку **Далее**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-kintone-tutorial/IC785875.png "Настройка URL-адреса приложения")

4. Для скачивания сертификата на странице **Настройка единого входа в Kintone** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785878.png "Настройка единого входа")

5. В другом окне веб-браузера войдите на веб-сайт **Kintone** компании в качестве администратора.

6. Щелкните **Параметры**.
   
    ![Параметры](./media/active-directory-saas-kintone-tutorial/IC785879.png "Параметры")

7. Щелкните **Users & System Administration** (Администрирование пользователей и системы).
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration") (Администрирование пользователей и системы)

8. Перейдите в раздел **System Administration \> Security** (Системное администрирование > Безопасность) и щелкните **Login** (Вход).
   
    ![Login](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login") (Вход)

9. Установите флажок **Включить проверку подлинности SAML**.
   
    ![Аутентификация SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "Аутентификация SAML")

10. В разделе «Проверка подлинности SAML» выполните следующие действия.
    
    ![Аутентификация SAML](./media/active-directory-saas-kintone-tutorial/IC785883.png "Аутентификация SAML")
    
    1. На странице диалогового окна **Настройка единого входа в Kintone** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login URL** (URL-адрес для входа).
   
    2. На странице диалогового окна **Настройка единого входа в Kintone** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).
    
    3. Чтобы передать скачанный сертификат, нажмите кнопку **Обзор** .
    
    4. Щелкните **Сохранить**.

11. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-kintone-tutorial/IC785884.png "Настройка единого входа")
    
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в Kintone, они должны быть подготовлены для Kintone.  
В случае с Kintone подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход на веб-сайт **Kintone** компании в качестве администратора.

2. Щелкните **Параметр**.
   
    ![Параметры](./media/active-directory-saas-kintone-tutorial/IC785879.png "Параметры")

3. Щелкните **Users & System Administration** (Администрирование пользователей и системы).
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration") (Администрирование пользователей и системы)

4. В разделе **User Administration** (Администрирование пользователей) щелкните **Departments & Users** (Отделы и пользователи).
   
    ![Department & Users](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users") (Отделы и пользователи)

5. Щелкните **Новый пользователь**.
   
    ![Новые пользователи](./media/active-directory-saas-kintone-tutorial/IC785889.png "Новые пользователи")

6. В разделе **New User** (Новый пользователь) выполните следующие действия.
   
    ![Новые пользователи](./media/active-directory-saas-kintone-tutorial/IC785890.png "Новые пользователи")
   
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
   
    ![Назначение пользователей](./media/active-directory-saas-kintone-tutorial/IC785891.png "Назначение пользователей")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-kintone-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


