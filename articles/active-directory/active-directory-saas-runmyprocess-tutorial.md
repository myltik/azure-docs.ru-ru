<properties 
    pageTitle="Учебник. Интеграция Azure Active Directory с RunMyProcess | Microsoft Azure" 
    description="Узнайте, как использовать RunMyProcess с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-runmyprocess"></a>Учебник. Интеграция Azure Active Directory с RunMyProcess
  
Цель данного учебника — показать интеграцию Azure и RunMyProcess.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   действующая подписка Azure;
-   Клиент RunMyProcess.
  
По завершении работы с этим руководством пользователи Azure AD, назначенные в RunMyProcess, смогут выполнять единый вход в приложение на веб-сайте RunMyProcess компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для RunMyProcess
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Scenario")
##<a name="enabling-the-application-integration-for-runmyprocess"></a>Включение интеграции приложений для RunMyProcess
  
В этом разделе показано, как включить интеграцию приложений для RunMyProcess.

###<a name="to-enable-the-application-integration-for-runmyprocess,-perform-the-following-steps:"></a>Чтобы включить интеграцию приложений для RunMyProcess, выполните следующие действия:

1.  На классическом портале Azure в области навигации слева щелкните **Active Directory**.

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Applications")

4.  В нижней части страницы нажмите кнопку **Добавить** .

    ![Добавить приложение](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Add application")

5.  В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Add an application from gallerry")

6.  В **поле поиска** введите **RunMyProcess**.

    ![Коллекция приложений](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Application Gallery")

7.  В области результатов выберите **RunMyProcess** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##<a name="configuring-single-sign-on"></a>Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить аутентификацию в RunMyProcess со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Чтобы настроить единый вход, выполните следующие действия.

1.  На классическом портале Azure на странице интеграции с приложением **RunMyProcess** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configure Single Sign-On")

2.  На странице **Как пользователи должны входить в RunMyProcess?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configure Single Sign-On")

3.  На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в RunMyProcess** введите свой URL-адрес, используя шаблон *http://company.runmyprocess.com*, а затем нажмите кнопку **Далее**.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configure App URL")

4.  На странице **Настройка единого входа в RunMyProcess** щелкните **Скачать сертификат**, а затем сохраните файл сертификата на своем компьютере.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configure Single Sign-On")

5.  В другом окне веб-браузера войдите на сайт RunMyProcess вашей компании в качестве администратора.

6.  Выберите **Account \> Configuration** ("Учетная запись" > "Конфигурация").

    ![Учетная запись](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Account")

7.  Щелкните вкладку **Метод проверки подлинности** .

8.  В разделе **Метод аутентификации** выполните следующие действия:

    ![Единый вход](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  Для параметра **Method** (Метод) выберите значение **SSO with Samlv2** (Единый вход с помощью SAML версии 2).
    2.  На странице диалогового окна **Настройка единого входа в RunMyProcess** классического портала Azure скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **SSO redirect** (Перенаправление для единого входа).
    3.  На странице диалогового окна **Настройка единого входа в RunMyProcess** классического портала Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **Logout redirect** (Перенаправление для выхода).
    4.  В текстовом поле **Name id Format** (Формат идентификатора имени) введите **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
    5.  Создайте файл в кодировке **Base-64** из загруженного сертификата.  

        >[AZURE.TIP] Дополнительные сведения можно узнать в видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

    6.  Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат** .
    7.  Щелкните **Сохранить**.

9.  На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configure Single Sign-On")
##<a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
  
Чтобы пользователи Azure AD могли выполнять вход в RunMyProcess, они должны быть подготовлены для RunMyProcess.  
В случае с RunMyProcess подготовка выполняется вручную.

###<a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:

1.  Войдите на веб-сайт **RunMyProcess** компании в качестве администратора.

2.  Выберите **Account \> Users** ("Учетная запись" > "Пользователи") и щелкните **New User** (Создать пользователя).

    ![Новый пользователь](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "New User")

3.  В разделе **Параметры пользователя** сделайте следующее:

    ![Профиль](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Profile")

    1.  Заполните текстовые поля **Name** (Имя) и **E-mail** (Электронный адрес) данными действующей учетной записи AAD, которую необходимо подготовить.
    2.  Выберите значения параметров **IDE language** (Язык интегрированной среды разработки), **Language** (Язык) и **Profile** (Профиль).
    3.  Установите флажок **Отправить сообщение о создании учетной записи**.
    4.  Щелкните **Сохранить**.

>[AZURE.NOTE]Вы можете использовать любые другие инструменты создания учетных записей пользователя RunMyProcess или API, предоставляемые RunMyProcess для подготовки учетных записей пользователя AAD.

##<a name="assigning-users"></a>Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###<a name="to-assign-users-to-runmyprocess,-perform-the-following-steps:"></a>Чтобы назначить пользователей RunMyProcess, выполните следующие действия:

1.  На классическом портале Azure создайте тестовую учетную запись.

2.  На странице интеграции с приложением **RunMyProcess** щелкните **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Assign Users")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Yes")
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


