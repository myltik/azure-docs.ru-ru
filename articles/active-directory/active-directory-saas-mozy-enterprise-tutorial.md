---
title: "Руководство по интеграции Azure Active Directory с Mozy Enterprise | Документация Майкрософт"
description: "Узнайте, как использовать Mozy Enterprise вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6034434c289936089afbb0fa5bd803363cde882


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Учебник. Интеграция Azure Active Directory с Mozy Enterprise
Цель данного учебника — показать интеграцию Azure и Mozy Enterprise.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Mozy Enterprise.

По завершении работы с этим руководством пользователи Azure AD, назначенные в Mozy Enterprise, смогут выполнять единый вход в приложение на веб-сайте Mozy Enterprise компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Mozy Enterprise
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>Включение интеграции приложений для Mozy Enterprise
В этом разделе показано, как включить интеграцию приложений для Mozy Enterprise.

### <a name="to-enable-the-application-integration-for-mozy-enterprise-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Mozy Enterprise, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **mozy enterprise**.
   
   ![Коллекция приложений](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Application Gallery")
7. В области результатов выберите **Mozy Enterprise** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![mozy enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в Mozy Enterprise со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется передать в клиент Mozy Enterprise сертификат в кодировке Base-64.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Mozy Enterprise** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Mozy Enterprise** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configure single sign-on")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес входа в Mozy Enterprise** введите свой URL-адрес, используя следующий формат *https://\<имя_клиента\>.Mozyenterprise.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configure app URL")
4. Чтобы скачать сертификат, на странице **Настройка единого входа в Mozy Enterprise** щелкните **Загрузить сертификат** и сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configure single sign-on")
5. В другом окне веб-браузера войдите на сайт Mozy Enterprise компании в качестве администратора.
6. В разделе **Конфигурация** щелкните **Политика проверки подлинности**.
   
   ![Политика проверки подлинности](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentication policy")
7. В разделе **Политика проверки подлинности** сделайте следующее:
   
   ![Политика проверки подлинности](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentication policy")
   
   1. Для параметра **Поставщик** выберите значение **Служба каталогов**.
   2. Выберите **Использовать LDAP для отправки**.
   3. Щелкните вкладку **Проверка подлинности SAML** .
   4. На диалоговой странице **Настройка единого входа в Mozy Enterprise** классического портала Azure скопируйте значение поля **URL-адрес запроса проверки подлинности** и вставьте его в текстовое поле **Authentication URL** (URL-адрес проверки подлинности).
   5. На диалоговой странице **Настройка единого входа в Mozy Enterprise** классического портала Azure скопируйте значение поля **Идентификатор поставщика удостоверений** и вставьте его в текстовое поле **SAML Endpoint** (Конечная точка SAML).
   6. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена и вставьте весь сертификат в текстовое поле **Сертификат SAML** .
   8. Установите флажок **Включить единый вход для администраторов для входа с учетными данными сети**.
   9. Нажмите кнопку **Сохранить изменения**.
8. На диалоговой странице **Настройка единого входа в Mozy Enterprise** классического портала Azure выберите подтверждение настройки единого входа и щелкните **Завершить**.
   
   ![Настройка единого входа](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы дать возможность пользователям Azure AD входить в Mozy Enterprise, необходимо в Mozy Enterprise подготовить для них учетные записи.  
В случае с Mozy Enterprise подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход в клиент **Mozy Enterprise** .
2. Щелкните **Users** (Пользователи), а затем — **Add New User** (Добавить нового пользователя).
   
   ![Пользователи](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Users")
   
   > [!NOTE]
   > Параметр **Add New User** (Добавить нового пользователя) отображается только в том случае, если в качестве поставщика в списке **Authentication policy** (Политика проверки подлинности) выбрать **Mozy**. Если настроена аутентификация SAML, то пользователи добавляются автоматически при первом входе посредством единого входа.
   > 
   > 
3. В диалоговом окне создания нового пользователя выполните следующие действия:
   
   ![Добавление пользователей](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Add Users")
   
   1. В списке **Выберите группу** выберите группу.
   2. В списке **Тип пользователя** выберите тип.
   3. В текстовом поле **Имя пользователя** введите имя пользователя Azure AD.
   4. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя Azure AD.
   5. Выберите **Отправить пользователю электронное сообщение с указаниями**.
   6. Щелкните **Добавить пользователей**.
   
   > [!NOTE]
   > После создания учетной записи соответствующему пользователю Azure AD будет отправлено электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей Mozy Enterprise или API, предоставляемые Mozy Enterprise для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить конфигурацию, необходимо предоставить тем пользователям Azure AD, которым требуется разрешить использовать приложение, доступ к нему, назначив их.

### <a name="to-assign-users-to-mozy-enterprise-perform-the-following-steps"></a>Чтобы назначить пользователей Mozy Enterprise, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Mozy Enterprise** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


