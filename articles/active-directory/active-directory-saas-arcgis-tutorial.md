---
title: "Руководство по интеграции Azure Active Directory с ArcGIS | Документация Майкрософт"
description: "Узнайте, как использовать ArcGIS вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4b6ad270d7aded7e4ea7343e53ffd8fc3a59d1c6


---
# <a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Руководство. Интеграция Azure Active Directory с ArcGIS
Цель данного руководства — показать интеграцию Azure и ArcGIS. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа ArcGIS

После завершения этого руководства пользователи Azure AD, назначенные ArcGIS, будут иметь возможность единого входа в приложение на веб-сайте компании ArcGIS (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для ArcGIS
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")

## <a name="enabling-the-application-integration-for-arcgis"></a>Включение интеграции приложений для ArcGIS
В этом разделе показано, как включить интеграцию приложений для ArcGIS.

### <a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для ArcGIS, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **ArcGIS**.
   
   ![Коллекция приложений](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation Gallery")
7. В области результатов выберите **ArcGIS** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в ArcGIS со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **ArcGIS** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в ArcGIS?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configure Single Sign-On")
3. На странице **Configure App URL** (Настройка URL-адреса приложения) в текстовом поле **URL-адрес входа в ArcGIS** введите URL-адрес, используемый для входа в приложение, в формате *https://company.maps.arcgis.com* и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configure App URL")
4. На странице **Настройка единого входа в ArcGIS** щелкните **Скачать метаданные**, а затем сохраните файл метаданных локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт ArcGIS в качестве администратора.
6. Нажмите **Изменить параметры**.
   
   ![Изменить параметры](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")
7. Выберите пункт **Безопасность**.
   
   ![Безопасность](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")
8. В разделе **Enterprise Logins** (Корпоративные имена входа) установите флажок **Set Identity Provider** (Назначить поставщика удостоверений).
   
   ![Корпоративные имена входа](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")
9. На странице **Назначение поставщика удостоверений** выполните следующие действия.
   
   ![Назначить поставщика удостоверений](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")
   
   1. В текстовое поле «Имя» введите название вашей организации.
   2. Для параметра **Metadata for the Enterprise Identity Provider will be supplied using** (В предоставлении метаданных для корпоративного поставщика удостоверений будет использоваться) выберите значение **Файл**.
   3. Чтобы отправить загруженный файл метаданных, нажмите кнопку **Выбрать файл**.
   4. Нажмите кнопку **Назначить поставщика удостоверений**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в ArcGIS, они должны быть подготовлены для ArcGIS.  
В случае с ArcGIS подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите в клиент **ArcGIS** .
2. Нажмите **Пригласить участников**.
   
   ![Пригласить участников](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")
3. Выберите параметр **Add members automatically without sending an email** (Добавлять участников автоматически без отправки сообщений электронной почты) и нажмите кнопку **Далее**.
   
   ![Добавлять участников автоматически](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")
4. На странице диалогового окна **Участники** выполните следующие действия.
   
   ![Добавить и просмотреть](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")
   
   1. Укажите **имя**, **фамилию** и **адрес электронной почты** для действующей учетной записи AAD, которую вы хотите подготовить.
   2. Нажмите кнопку **Добавить и просмотреть**.
5. Просмотрите введенные данные и нажмите кнопку **Добавить участников**.
   
   ![Добавить участника](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя ArcGIS или API, предоставляемые ArcGIS для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Чтобы назначить пользователей ArcGIS, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **ArcGIS ** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


