---
title: "Руководство по интеграции Azure Active Directory с Veracode | Документация Майкрософт"
description: "Узнайте, как использовать Veracode вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 731a0fd05b6ed0c49c0c06836e33cd99f7e3bce2


---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Руководство. Интеграция Azure Active Directory с Veracode
Цель данного учебника — показать интеграцию Azure и Veracode. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка Veracode с поддержкой единого входа.

После завершения этого руководства пользователи Azure AD, назначенные Veracode, будут иметь возможность единого входа в приложение с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Veracode 
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

## <a name="enabling-the-application-integration-for-veracode"></a>Включение интеграции приложений для Veracode 
В этом разделе показано, как включить интеграцию приложений для Veracode.

### <a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Veracode, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Veracode**.
   
   ![Коллекция приложений](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")
7. В области результатов выберите **Veracode** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Veracode с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
Приложение Veracode ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML** .  
На следующем снимке экрана приведен пример.

![Атрибуты](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Veracode** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")
2. На странице **Как пользователи будут входить в Veracode?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")
3. На странице **Настроить параметры приложения** нажмите кнопку **Далее**.
   
   ![Настройка параметров приложения](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")
4. На странице **Настройка единого входа в Veracode** щелкните **Скачать сертификат**, а затем сохраните файл сертификата локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на веб-сайт Veracode вашей компании в качестве администратора.
6. В верхнем меню нажмите **Settings** (Параметры), а затем выберите **Admin** (Администратор).
   
   ![Администрирование](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")
7. Щелкните вкладку **SAML** .
8. В разделе **Параметры SAML для организации** выполните следующие действия.
   
   ![Администрирование](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")
   
   1. На странице **Настройка единого входа в Veracode** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Издатель**.
   2. Чтобы передать загруженный сертификат, нажмите кнопку **Выбрать файл**.
   3. Выберите параметр **Включить саморегистрацию**.
9. В разделе **Self Registration Settings** (Параметры саморегистрации) выполните следующие действия, а затем нажмите кнопку **Сохранить**.
   
   ![Администрирование](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")
   
   1. Для параметра **New User Activation** (Активация нового пользователя) выберите значение **No Activation Required** (Активация не требуется).
   2. Для параметра **User Data Updates** (Обновления пользовательских данных) выберите значение **Preference Veracode User Data** (Предпочтение пользовательских данных Veracode).
   3. Для параметра **Сведения об атрибутах SAML**выберите следующие значения:
      * **роли пользователей;**
      * **администратор политики;**
      * **рецензент;**
      * **руководитель безопасности;**
      * **руководитель;**
      * **отправитель;**
      * **создатель;**
      * **все типы проверки;**
      * **участие в группе;**
      * **группа по умолчанию.**
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")
11. В меню в верхней части экрана выберите пункт **Атрибуты** to open the **SAML Token Атрибуты** .
    
    ![Атрибуты](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")
12. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.
    
    ![Атрибуты](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")
    
    | Имя атрибута | Значение атрибута |
    |:--- |:--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    1. Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
    2. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
    3. В текстовом поле **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
    4. Нажмите **Завершено**.
13. Нажмите кнопку **Применить изменения**.

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в Veracode, они должны быть подготовлены для Veracode.  
В случае Veracode подготовка выполняется автоматически.  
С вашей стороны никакие действия не требуются.

В случае необходимости пользователи создаются автоматически при первой попытке входа в систему.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Veracode или API-интерфейсы, предоставляемые Veracode для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Чтобы назначить пользователей Veracode, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Veracode** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


