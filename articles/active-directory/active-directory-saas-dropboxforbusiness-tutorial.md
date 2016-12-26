---
title: "Руководство по интеграции Azure Active Directory с Dropbox for Business | Документация Майкрософт"
description: "Узнайте, как использовать Dropbox for Business вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: e328f8ecfad5f481462a7b9c0c4a16e966e33217


---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Руководство. Интеграция Azure Active Directory с Dropbox for Business
Цель данного руководства — показать интеграцию Azure и Dropbox for Business.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Тестовый клиент в Dropbox for Business

После выполнения действий, описанных в этом руководстве, пользователи Azure AD, которых вы прикрепите к Dropbox for Business, смогут использовать единый вход в приложение на веб-сайте Dropbox for Business вашей организации (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Dropbox for Business
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")

## <a name="enabling-the-application-integration-for-dropbox-for-business"></a>Включение интеграции приложений для Dropbox for Business
В этом разделе показано, как включить интеграцию приложений для Dropbox for Business.

### <a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Dropbox for Business, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Dropbox for Business**.
   
    ![Коллекция приложений](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application gallery")
7. В области результатов выберите **Dropbox for Business** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить аутентификацию в Dropbox for Business со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

В рамках этой процедуры вам потребуется загрузить сертификат в кодировке Base-64 в свой клиент Dropbox for Business. Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Dropbox for Business** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")
2. На странице ** Как пользователи должны входить в Dropbox for Business?** выберите **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")
3. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
    а. Войдите в клиент Dropbox for Business. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")
   
    b. На панели навигации слева щелкните **Консоль администрирования**. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")
   
    c. В **консоли администрирования** выберите пункт **Проверка подлинности** в левой области навигации. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")
   
    d. В разделе **Единый вход** установите флажок **Включить единый вход** и нажмите кнопку **Дополнительно**, чтобы развернуть этот раздел.  
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")
   
    д. Скопируйте URL-адрес рядом с пунктом **Users can sign in by entering their email address or they can go directly to**(Пользователи могут входить, указывая адрес электронной почты, или напрямую переходить по адресу). 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")
   
    Е. На классическом портале Azure вставьте URL-адрес в текстовое поле **Dropbox for business sign in** (URL-адрес для входа в Dropbox for Business). 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  
4. На странице **Настройка единого входа в Dropbox for Business** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на компьютере.  
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")
5. В клиенте Dropbox for Business в разделе **Единый вход** на странице **Проверка подлинности** выполните следующие действия: 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")
   
    а. Установите флажок **Обязательно**.
   
    b. На странице диалогового окна **Настройка единого входа в Dropbox for Business** классического портала Azure скопируйте значение поля **URL-адрес страницы входа** и вставьте его в текстовом поле **URL-адрес входа**.

    c. Создайте файл **в кодировке Base-64** из скачанного сертификата. 

    > [!TIP] 
    > Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    d. Щелкните **Выбрать сертификат** и выберите **файл сертификата в кодировке Base-64**.

    д. Щелкните **Сохранить изменения**, чтобы завершить настройку клиента Dropbox for Business.

1. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для Dropbox for Business.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. На странице интеграции с приложением **Dropbox for Business** на классическом портале Azure щелкните **Настроить подготовку учетных записей пользователей**, чтобы открыть диалоговое окно **Configure User Provisioning**(Настройка подготовки учетных записей пользователей).
2. На странице "Включить подготовку учетных записей пользователей для: Dropbox for Business" нажмите кнопку "Включить подготовку пользователей", чтобы открыть диалоговое окно Sign in to Dropbox to link with Windows Azure AD (Вход в Dropbox для установки связи с Microsoft Azure AD).  
   
    ![Подготовка пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "User provisioning")
3. В диалоговом окне **Sign in to Dropbox to link with Windows Azure AD** (Вход в Dropbox для установки связи с Microsoft Azure AD) войдите в клиент Dropbox for Business. 
   
    ![Подготовка пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "User provisioning")
4. Нажмите кнопку **Разрешить** , чтобы предоставить Azure AD доступ к Dropbox. 
   
    ![Подготовка пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "User provisioning")
5. Для завершения настройки нажмите кнопку **Завершить** .  
   
    ![Подготовка пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "User provisioning")

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Чтобы назначить пользователей Dropbox for Business, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Dropbox for Business** нажмите кнопку **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")

Подождите 10 минут и убедитесь, что учетная запись синхронизирована с Dropbox for Business.

Сначала проверьте состояние подготовки, щелкнув **Панель мониторинга** на странице интеграции с приложением **Dropbox for Business** на классическом портале Azure.

![Назначить пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")

Об успешном завершении цикла подготовки пользователя говорит соответствующий статус.

![Назначить пользователей](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")

Если вы хотите проверить параметры единого входа, откройте панель доступа.
Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


