---
title: 'Руководство: интеграция Azure Active Directory с ServiceNow | Microsoft Docs'
description: Узнайте, как использовать ServiceNow с Azure Active Directory для реализации единого входа, автоматической подготовки и многого другого.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-servicenow"></a>Руководство: интеграция Azure Active Directory с ServiceNow
Цель данного учебника — показать интеграцию Azure и ServiceNow.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* Клиент в ServiceNow версии Calgary или выше
* В клиенте ServiceNow должен быть включен [подключаемый модуль единого входа для нескольких поставщиков](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) . Это можно сделать, отправив запрос на обслуживание на сайте https://hi.service-now.com/ 

После завершения этого руководства пользователи Azure AD, назначенные ServiceNow, получат возможность единого входа в приложение на корпоративном веб-сайте ServiceNow (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md)

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для ServiceNow
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Scenario")

## <a name="enabling-the-application-integration-for-servicenow"></a>Включение интеграции приложений для ServiceNow
В этом разделе показано, как включить интеграцию приложений для ServiceNow.

### <a name="to-enable-the-application-integration-for-servicenow,-perform-the-following-steps:"></a>Чтобы включить интеграцию приложений для ServiceNow, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **ServiceNow**.
   
   ![Коллекция приложений](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Application gallery")
7. В области результатов выберите **ServiceNow** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в ServiceNow со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

В рамках этой процедуры вам потребуется загрузить сертификат в кодировке Base-64 в свой клиент Dropbox for Business. Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure AD на странице интеграции с приложением **ServiceNow** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в ServiceNow?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")
3. На странице **Настройка параметров приложения** выполните следующие действия.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")
   
   а. В текстовом поле **URL-адрес входа в ServiceNow** введите URL-адрес, используемый пользователями для входа в приложение ServiceNow (например, *https://\<имя_экземпляра\>.service-now.com*).
   
   b. В текстовом поле **URL-адрес издателя** введите URL-адрес, который используется пользователями для входа в приложение ServiceNow (например, *https://\<имя_экземпляра\>.service-now.com*).
   
   c. В нижней части страницы нажмите кнопку **Далее**
4. Чтобы в Azure AD была выполнена автоматическая настройка ServiceNow для аутентификации на основе SAML, введите имя вашего экземпляра ServiceNow, имя пользователя с правами администратора и пароль администратора в форму **Настроить единый вход автоматически** , после чего щелкните *Настроить*. Обратите внимание, что для этого указываемому пользователю с правами администратора должна быть назначена роль **security_admin** в ServiceNow. Вы также можете вручную настроить в ServiceNow использование Azure AD в качестве поставщика удостоверений SAML. Для этого щелкните **Вручную настроить это приложение для единого входа**, затем нажмите кнопку **Далее** и выполните следующие действия.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")
5. На странице **Настройка единого входа в ServiceNow** щелкните **Скачать сертификат**, сохраните файл сертификата на локальном компьютере, затем нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")
6. Войдите в приложение ServiceNow с правами администратора.
7. На панели навигации слева щелкните **Properties**(Свойства).  
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configure app URL")
8. В диалоговом окне **Multiple Provider SSO Properties** (Свойства единого входа для нескольких поставщиков) выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configure app URL")
   
    а. Для параметра **Enable multiple provider SSO** (Включить единый вход для нескольких поставщиков) выберите значение **Yes** (Да).
   
    b. Для параметра **Enable debug logging got the multiple provider SSO integration** (Включить ведение журнала отладки для интеграции нескольких поставщиков единого входа) выберите **Yes** (Да).
   
    c. В текстовом поле **The field on the user table that...** (Поле в пользовательской таблице) введите значение **user_name**.
   
    d. Щелкните **Сохранить**.
9. На панели навигации слева щелкните **x509 Certificates**(Сертификаты x509).
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configure single sign-on")
10. В диалоговом окне **X.509 Certificates** (Сертификаты X.509) нажмите кнопку **New** (Создать).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")
11. В диалоговом окне **X.509 Certificates** (Сертификаты X.509) выполните следующие действия.
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")
    
     а. Нажмите кнопку **Создать**.
    
     b. В текстовом поле **Name** (Имя) введите имя конфигурации (например, **TestSAML2.0**).
    
     c. Установите флажок **Активно**.
    
     d. В поле **Format** (Формат) выберите **PEM**.
    
     д. В поле **Type** (Тип) выберите **Trust Store Cert** (Сертификат хранилища доверия).
    
     Е. На основе загруженного сертификата создайте файл в кодировке Base 64.
    
    > [!NOTE]
    > Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
     ж. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **PEM Certificate** (Сертификат PEM).
    
     h. Нажмите кнопку **Update**(Обновить).
12. На панели навигации слева щелкните **Identity Providers**(Поставщики удостоверений).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configure single sign-on")
13. В диалоговом окне **Identity Providers** (Поставщики удостоверений) нажмите кнопку **New** (Создать).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")
14. В диалоговом окне **Identity Providers** (Поставщики удостоверений) щелкните **SAML2 Update1?**.
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")
15. В диалоговом окне SAML2 Update1 Properties (Свойства SAML2 Update1) выполните следующие действия.
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")

    а. В текстовом поле **Name** (Имя) введите имя конфигурации (например, **SAML 2.0**).

    b. В текстовом поле **User Field** (Поле пользователя) введите **электронный адрес** или значение **user_id**, в зависимости от того, какое поле используется для уникальной идентификации пользователей в развертывании ServiceNow. 

    **Примечание**. Вы можете настроить в Azure AD выдачу идентификатора пользователя Azure AD (имени участника-пользователя) или электронного адреса в качестве уникального идентификатора в токене SAML, перейдя в раздел **ServiceNow > Атрибуты > Единый вход** на классическом портале Azure и сопоставив нужное поле с атрибутом **nameidentifier**. Значение, хранящееся для выбранного атрибута в Azure AD, например имя участника-пользователя, должно соответствовать значению, хранящемуся в ServiceNow для введенного поля (например user_id)

    c. На классическом портале Azure AD скопируйте значение поля **Идентификатор поставщика удостоверений**, а затем вставьте его в текстовое поле **Identity provider URL** (URL-адрес поставщика удостоверений).

    d. На классическом портале Azure AD скопируйте значение поля **URL-адрес запроса проверки подлинности**, а затем вставьте его в текстовое поле **Identity Provider's AuthnRequest** (Запрос аутентификации поставщика удостоверений).

    д. На классическом портале Azure AD скопируйте значение поля **URL-адрес службы единого входа**, а затем вставьте его в текстовое поле **Identity Provider's SingleLogoutRequest** (Запрос на единый выход поставщика удостоверений).

    Е. В текстовое поле **ServiceNow Homepage** (Домашняя страница ServiceNow) введите URL-адрес домашней страницы экземпляра ServiceNow.

    > [AZURE.NOTE] URL-адрес домашней страницы экземпляра ServiceNow состоит из **URL-адреса клиента ServiceNow** и **/navpage.do** (например, *https://.service-now.com/navpage.do*).


    ж. В текстовое поле **Entity ID / Issuer** (Идентификатор сущности или издатель) введите URL-адрес клиента ServiceNow.

    h. В текстовое поле **Audience URL** (URL-адрес аудитории) введите URL-адрес клиента ServiceNow. 

    i. В текстовом поле **Protocol Binding for the IDP's SingleLogoutRequest** (Привязка протокола для запроса на единый выход поставщика удостоверений) введите **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. В текстовое поле NameID Policy (Политика идентификатора имени) введите **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Снимите флажок **Create an AuthnContextClass**(Создать класс контекста проверки подлинности).

    l. В поле **AuthnContextClassRef Method** (Метод AuthnContextClassRef) введите **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. В текстовое поле **Clock Skew** (Разница в показаниях часов) введите **60**.

    n. В поле **Single Sign On Script** (Сценарий единого входа) выберите **MultiSSO_SAML2_Update1**.

    o. В поле **x509 Certificate**(Сертификат x509) выберите сертификат, созданный на предыдущем шаге.

    p. Нажмите кнопку **Submit**(Отправить). 



1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для ServiceNow.

### <a name="to-configure-user-provisioning,-perform-the-following-steps:"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **ServiceNow** щелкните **Настроить подготовку учетных записей пользователей**. 
   
    ![Подготовка пользователей](./media/active-directory-saas-servicenow-tutorial/IC769498.png "User provisioning")
2. На странице **Ввод учетных данных ServiceNow для включения автоматической подготовки пользователей** в разделе настройки подготовки пользователей укажите следующие параметры конфигурации. 
   
     а. В текстовое поле **Имя экземпляра ServiceNow** введите имя экземпляра ServiceNow.
   
     b. В текстовом поле **Имя пользователя администратора ServiceNow** введите имя учетной записи администратора ServiceNow.
   
     c. В текстовое поле **Пароль администратора ServiceNow** введите пароль для этой учетной записи.
   
     d. Нажмите **проверить** для проверки конфигурации.
   
     д. Нажмите кнопку **Далее**, чтобы открыть страницу **Дальнейшие действия**.
   
     Е. Если вы хотите подготовить всех пользователей для этого приложения, установите флажок**Автоматически настраивать все учетные записи пользователей из каталога в этом приложении**. 
   
    ![Дальнейшие действия](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Next Steps")
   
     ж. На странице **Дальнейшие действия** нажмите кнопку **Завершить**, чтобы сохранить конфигурацию.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-servicenow,-perform-the-following-steps:"></a>Чтобы назначить пользователей ServiceNow, выполните следующие действия.
1. На классическом портале Azure AD создайте тестовую учетную запись.
2. На странице интеграции с приложением **ServiceNow** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--HONumber=Oct16_HO2-->


