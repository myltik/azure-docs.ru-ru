---
title: "Учебник. Интеграция Azure Active Directory с InsideView | Документация Майкрософт"
description: "Узнайте, как использовать InsideView вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0da7c8f20fb6bbe78294aab2fb69c24a2dddd877


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Руководство. Интеграция Azure Active Directory с InsideView
Цель данного руководства — показать интеграцию Azure и InsideView.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент InsideView

По завершении работы с этим руководством пользователи Azure AD, назначенные в InsideView, смогут выполнять единый вход в приложение на веб-сайте InsideView компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для InsideView
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")

## <a name="enabling-the-application-integration-for-insideview"></a>Включение интеграции приложений для InsideView
В этом разделе показано, как включить интеграцию приложений для InsideView.

### <a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для InsideView, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **InsideView**.
   
   ![Коллекция приложений](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")
7. В области результатов выберите **InsideView** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в InsideView со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **InsideView** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")
2. На странице **Как пользователи должны входить в InsideView?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес ответа InsideView** введите свой URL-адрес единого входа InsideView (например, `https://my.insideview.com/iv/<STS Name>/login.iv`) и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")
4. Для скачивания сертификата на странице **Настройка единого входа в InsideView** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт InsideView в качестве администратора.
6. На панели инструментов в верхней части экрана щелкните **Admin** (Администрирование), **SingleSignOn Settings** (Параметры единого входа) и выберите **Add SAML** (Добавить SAML).
   
   ![Параметры единого входа SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. В разделе **Добавить SAML** сделайте следующее:
   
   ![Добавить SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. В текстовом поле **Имя службы токенов безопасности** введите имя конфигурации.
   2. На странице диалогового окна **Настройка единого входа в InsideView** классического портала Azure скопируйте значение поля **Конечная точка, инициированная поставщиком услуг** и вставьте его в текстовое поле **SamlP/WS-Fed Unsolicated EndPoint** (Незапрошенная конечная точка SamlP/WS-Fed).
   3. Создайте файл **в кодировке Base-64** из скачанного сертификата.
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат службы токенов безопасности** .
   5. В текстовом поле **Crm User Id Mapping** (Сопоставление идентификаторов пользователей CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   6. В текстовом поле **Crm Email Mapping** (Сопоставление адресов электронной почты CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   7. В текстовом поле **Crm First Name Mapping** (Сопоставление имен CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   8. В текстовом поле **Crm lastName Mapping** (Сопоставление фамилий CRM) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Щелкните **Сохранить**.
8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в InsideView, они должны быть подготовлены для InsideView.  
В случае с InsideView подготовка выполняется вручную.

Для получения пользователей и контактов, созданных в InsideView, обратитесь к своему менеджеру по работе с клиентами или отправьте сообщение электронной почты по адресу **support@insideview.com**

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя InsideView или API, предоставляемые InsideView для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Чтобы назначить пользователей InsideView, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **InsideView** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


