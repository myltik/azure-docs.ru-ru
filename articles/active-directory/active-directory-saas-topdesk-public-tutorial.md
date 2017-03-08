---
title: "Руководство. Интеграция Azure Active Directory с TOPdesk - Public | Документация Майкрософт"
description: "Узнайте, как использовать TOPdesk - Public с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: ee413f5044d541a7a127690fab0115341c7619f3


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Учебник. Интеграция Azure Directory с TOPdesk — Public
Цель данного учебника — показать интеграцию Azure и TOPdesk — Public.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка TOPdesk — Public с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные в TOPdesk — Public, смогут выполнять единый вход в приложение на веб-сайте TOPdesk — Public вашей компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для TOPdesk — Public
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>Включение интеграции приложений для TOPdesk — Public
В этом разделе показано, как включить интеграцию приложений для TOPdesk — Public.

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для TOPdesk — Public, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Add an application from gallerry")

6. В **поле поиска** введите **TOPdesk - Public**.
   
    ![Коллекция приложений](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Application Gallery")

7. В области результатов выберите **TOPdesk - Public** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в TOPdesk — Public с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
Для настройки единого входа для TOPdesk — Public вам необходимо отправить файл значка с логотипом. Для получения файла значка обратитесь в службу поддержки TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. Выполните вход на веб-сайт **TOPdesk — Public** компании в качестве администратора.

2. В меню **TOPdesk** выберите **Settings** (Параметры).
   
    ![Параметры](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")

3. Щелкните **Параметры входа**.
   
    ![Параметры входа](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

4. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).
   
    ![Общие сведения](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5. В разделе **Public** (Общедоступные) для конфигурации **SAML login** (Вход SAML) выполните следующие действия.
   
    ![Технические параметры](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")
   
    а. Нажмите кнопку **Скачать** , чтобы скачать общедоступный файл метаданных, а затем сохраните его локально на компьютере.
   
    b. Откройте файл метаданных и определите местоположение узла **AssertionConsumerService** .
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   
    c. Скопируйте значение **AssertionConsumerService** .  
      
    > [!NOTE]
    > Это значение потребуется в разделе **Настройка URL-адреса приложения** далее в этом руководстве.
    > 
    > 

6. В другом окне веб-браузера войдите на **классический портал Azure** в качестве администратора.

7. На странице интеграции с приложением **TOPdesk - Public** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configure Single Sign-On")

8. На странице **Как пользователи должны входить в TOPdesk - Public** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configure Single Sign-On")

9. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configure App URL")
   
    а. В текстовом поле **URL-адрес для входа в TOPdesk - Public** введите URL-адрес, используемый пользователями для входа в приложение TOPdesk - Public (например, *https://qssolutions.topdesk.net*).
   
    b. В текстовое поле **URL-адрес ответа TOPdesk - Public** вставьте значение **TOPdesk - Public AssertionConsumerService URL** (URL-адрес AssertionConsumerService для TOPdesk - Public) (например, *https://qssolutions.topdesk.net/tas/public/login/saml*).
   
    c. Нажмите кнопку **Далее**.

10. На странице **Настройка единого входа в TOPdesk - Public** нажмите кнопку **Скачать метаданные**, чтобы скачать метаданные, а затем сохраните файл данных на локальный компьютер.
    
    ![Настройка единого входа](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configure Single Sign-On")

11. Чтобы создать файл сертификата, выполните следующие действия:
    
    ![Сертификат](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")
    
    1. Откройте скачанный файл метаданных.
    2. Разверните узел **RoleDescriptor**, содержащий **xsi:type** со значением **fed:ApplicationServiceType**.
    3. Скопируйте значение узла **X509Certificate** .
    4. Сохраните скопированное значение узла **X509Certificate** локально в файл на компьютере.
12. На веб-сайте TOPdesk - Public компании в меню **TOPdesk** выберите **Параметры**.
    
    ![Параметры](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")
13. Щелкните **Параметры входа**.
    
    ![Параметры входа](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
14. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).
    
    ![Общие сведения](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
15. В разделе **Public** (Общедоступные) щелкните **Add** (Добавить).
    
    ![Вход SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML Login")
16. На странице диалогового окна **Помощник настройки SAML** сделайте следующее:
    
    ![Помощник настройки SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")
    
    а. Чтобы отправить скачанный файл метаданных, напротив пункта **Federation Metadata** (Метаданные федерации) нажмите кнопку **Browse** (Обзор).

    b. Чтобы отправить файл сертификата, напротив пункта **Certificate (RSA)** (Сертификат (RSA)) нажмите кнопку **Browse** (Обзор).

    c. Чтобы отправить файл с логотипом, полученный от службы поддержки TOPdesk, напротив пункта **Logo icon** (Значок логотипа) нажмите кнопку **Browse** (Обзор).

    d. В текстовом поле **User name attribute** (Атрибут имени пользователя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    д. В текстовом поле **Отображаемое имя** введите имя конфигурации.

    Е. Щелкните **Сохранить**.

17. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в систему TOPdesk — Public, они должны быть подготовлены для нее.  
В случае TOPdesk — Public подготовка пользователей осуществляется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт **TOPdesk — Public** своей компании в качестве администратора.

2. В меню в верхней части страницы щелкните **TOPdesk \> New \> Support Files \> Person** ("TOPdesk" > "Создать" > "Файлы поддержки" > "Пользователь").
   
    ![Person](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3. В диалоговом окне "Новый пользователь" выполните следующие действия:
   
    ![Новый пользователь](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")
   
    а. Перейдите на вкладку "Общее".

    b. В текстовом поле "Фамилия" введите фамилию пользователя действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
 
    c. Выберите **Веб-сайт** для этой учетной записи.
 
    d. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей TOPdesk — Public или API-интерфейсы, предоставляемые TOPdesk — Public для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Чтобы назначить пользователей TOPdesk — Public, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **TOPdesk - Public** щелкните **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assign Users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


