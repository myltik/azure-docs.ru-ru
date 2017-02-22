---
title: "Руководство по интеграции Azure Active Directory с Citrix GoToMeeting | Документация Майкрософт"
description: "Узнайте, как использовать Citrix GoToMeeting вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5b775b3cb53086daea6258cccc15cd77fd0e68d9
ms.openlocfilehash: c4f33f3f856c5017c5b156e2b27b3b82a275afa6


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Учебник. Интеграция Azure Active Directory с Citrix GoToMeeting

Цель данного учебника — показать интеграцию Azure и Citrix GoToMeeting. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Citrix GoToMeeting

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Citrix GoToMeeting
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Конфигурация](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")

## <a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Включение интеграции приложений для Citrix GoToMeeting
В этом разделе показано, как включить интеграцию приложений для Citrix GoToMeeting.

### <a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Citrix GoToMeeting, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6. В **поле поиска** введите **Citrix GoToMeeting**.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7. В области результатов выберите **Citrix GoToMeeting** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Citrix GoToMeeting со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется передать в клиент Citrix GoToMeeting сертификат в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Citrix GoToMeeting** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Включить единый вход](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Enable single sign-on")

2. На странице **Как пользователи должны входить в Citrix GoToMeeting?** выберите **Единый вход Microsoft Azure AD**.
   
    ![Настройка единого входа](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")

3. На странице **Настроить параметры приложения** нажмите кнопку **Далее**. 
   
    ![Включить единый вход](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Enable single sign-on")

4. На странице **Настройка единого входа в Citrix GoToMeeting** щелкните **Скачать сертификат**, а затем сохраните файл сертификата на компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5. В другом окне браузера войдите в [центр организации Citrix](https://account.citrixonline.com/organization/administration/).

6. Щелкните вкладку **Конфигурация поставщика удостоверений** и выполните следующие действия.  
   
    ![Настройка SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")
   
    а. Выберите **Вручную**

    b. На диалоговой странице **Настройка единого входа в Citrix GoToMeeting** классического портала Azure скопируйте значение поля **URL-адрес страницы входа** и вставьте его в текстовое поле **URL-адрес страницы входа**. 

    c. На диалоговой странице **Настройка единого входа в Citrix GoToMeeting** классического портала Azure скопируйте значение поля **URL-адрес страницы выхода** и вставьте его в текстовое поле **URL-адрес страницы выхода**.

    d. На диалоговой странице **Настройка единого входа в Citrix GoToMeeting** классического портала Azure скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений).

    д. Для передачи загруженного сертификата нажмите кнопку **Отправить**.

    Е. Щелкните **Сохранить**.


1. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")

2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Настройка SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для Citrix GoToMeeting.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. На странице интеграции приложения **Citrix GoToMeeting** классического портала Azure щелкните **Настроить подготовку учетных записей пользователей**, чтобы открыть диалоговое окно **Configure user provisioning** (Настройка подготовки учетных записей пользователей).
   
    ![Настроить подготовку учетных записей пользователей](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2. На странице **Параметры и учетные данные администратора** выполните следующие действия.
   
    ![Настройка подготовки учетных записей пользователей](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")
   
    а. В текстовом поле **Имя пользователя администратора Citrix GoToMeeting** введите имя пользователя администратора.

    b. В текстовом поле **Пароль администратора Citrix GoToMeeting** введите пароль администратора.

    c. Нажмите кнопку **Далее**.

1. На странице **Подтверждение** установите флажок, чтобы сохранить конфигурацию.
2. Нажмите кнопку **Проверить** , чтобы проверить конфигурацию.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Чтобы назначить пользователей Citrix GoToMeeting, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции приложения **Citrix GoToMeeting** щелкните **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

Подождите 10 минут и убедитесь, что учетная запись синхронизирована с Dropbox for Business.

В качестве первого шага проверки можно проверить состояние подготовки, щелкнув "Панель мониторинга" в разделе D на странице интеграции приложения **Citrix GoToMeeting** на классическом портале Azure.

![Панель мониторинга](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Об успешном завершении цикла подготовки пользователя говорит соответствующий статус:

![Состояние интеграции](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

Если вы хотите проверить параметры единого входа, откройте панель доступа.

Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).




<!--HONumber=Nov16_HO3-->


