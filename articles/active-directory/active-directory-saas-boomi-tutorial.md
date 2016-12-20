---
title: "Руководство по интеграции Azure Active Directory с Boomi | Документация Майкрософт"
description: "Узнайте, как использовать Boomi вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a315916e-8bfd-4390-bad2-ec9029314ab6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51ea1ededc7b850e3dff24c9f735789d34391434


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Руководство. Интеграция Azure Active Directory с Boomi
Цель данного руководства — показать интеграцию Azure и Boomi.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Boomi

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к Boomi, смогут выполнять единый вход в приложение на корпоративном веб-сайте Boomi (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Boomi
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")

## <a name="enabling-the-application-integration-for-boomi"></a>Включение интеграции приложений для Boomi
В этом разделе показано, как включить интеграцию приложений для Boomi.

### <a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Boomi, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Boomi**.
   
   ![Коллекция приложений](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")
7. В области результатов выберите **Boomi** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Boomi со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Boomi** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Boomi?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **Boomi Reply URL** (URL-адрес ответа Boomi) введите **URL-адрес входа в Boomi AtomSphere** (например, *https://platform.boomi.com/sso/AccountName/saml*) и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")
4. На странице **Настройка единого входа в Boomi** щелкните **Скачать сертификат** и сохраните файл сертификата локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Boomi в качестве администратора.
6. На панели инструментов в верхней части экрана щелкните название своей организации и выберите элемент **Setup**(Настроить).
   
   ![Setup](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")
7. Нажмите **Параметры единого входа**.
   
   ![Параметры единого входа](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")
8. В разделе **Single Sign-On Options** (Параметры единого входа) выполните следующие действия.
   
   ![Single Sign-On Options](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")
   
   1. Установите флажок **Enable SAML Single Sign-On**(Разрешить единый вход SAML).
   2. Нажмите кнопку **Import**(Импорт), чтобы передать загруженный сертификат.
   3. На диалоговой странице **Настройка единого входа в Boomi** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес для входа поставщика удостоверений**.
   4. Для параметра **Federation Id Location** (Расположение идентификатора федерации) выберите значение **Federation Id is in NameID element of the Subject** (Идентификатор федерации выступает элементом NameID субъекта).
   5. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Boomi, они должны быть подготовлены для Boomi.  
В случае с Boomi подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт компании **Boomi** в качестве администратора.
2. Последовательно выберите пункты **User Management \> Users** (Управление пользователями > Пользователи).
   
   ![Пользователи](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")
3. Нажмите кнопку **Add User**(Добавить пользователя).
   
   ![Add User](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")
4. На диалоговой странице **Add User Roles** (Добавление ролей пользователей) выполните следующие действия.
   
   ![Добавить пользователя](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")
   
   1. Заполните текстовые поля **First Name** (Имя), **Last Name** (Фамилия) и **Email** (Электронный адрес) данными действующей учетной записи AAD, которую необходимо подготовить.
   2. Нажмите кнопку ОК.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Boomi или API, предоставляемые Boomi для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Чтобы назначить пользователей Boomi, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Boomi** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


