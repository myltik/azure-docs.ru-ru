---
title: Руководство. Интеграция Azure Active Directory с Workday | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 5f3c3bbb23b9d4439188286342d80eb1cc1d0833
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Руководство. Интеграция Azure Active Directory с Workday

В этом руководстве описано, как интегрировать Workday с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Workday обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Workday.
- Вы можете включить автоматический вход пользователей в Workday (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Workday, вам потребуется:

- подписка Azure AD;
- подписка Workday с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Workday из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-workday-from-the-gallery"></a>Добавление Workday из коллекции
Чтобы настроить интеграцию Workday с Azure AD, необходимо добавить Workday из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workday из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Workday**, выберите **Workday** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Workday в списке результатов](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Workday с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Workday соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workday.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workday.

Чтобы настроить и проверить единый вход Azure AD в Workday, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Workday](#create-a-workday-test-user)** требуется для того, чтобы в Workday существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Workday.

**Чтобы настроить единый вход Azure AD в Workday, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Workday** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Workday** сделайте следующее.

    ![Сведения о домене и URL-адресах для единого входа для приложения Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    Б. В текстовом поле **Идентификатор** введите URL-адрес: `http://www.workday.com`

4. Установите флажок **Показывать дополнительные параметры URL-адреса** и выполните следующие действия.

    ![Сведения о домене и URL-адресах для единого входа для приложения Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url1.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://impl.workday.com/<tenant>/login-saml.htmld`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями URL-адреса входа и URL-адреса ответа. URL-адрес ответа должен содержать поддомен (например, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Например, значение "*http://www.myworkday.com*" допустимо, а значение "*http://myworkday.com*" — нет. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html).  

5. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)
    
7. В разделе **Конфигурация Workday** щелкните **Настроить Workday**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 

8. В другом окне веб-браузера войдите на свой корпоративный сайт Workday в качестве администратора.

9. В **поле поиска** выполните поиск **Edit Tenant Setup – Security** в верхней левой части домашней страницы.
   
    ![Изменение параметров безопасности клиента](./media/active-directory-saas-workday-tutorial/IC782925.png "Изменение параметров безопасности клиента")

10. В разделе **URL-адреса перенаправления** выполните следующие действия.
   
    ![URL-адреса перенаправления](./media/active-directory-saas-workday-tutorial/IC7829581.png "URL-адреса перенаправления")
   
    a. Нажмите кнопку **Добавить строку**.
   
    Б. В текстовых полях **Login Redirect URL** (URL-адрес перенаправления для входа) и **Mobile Redirect URL** (URL-адрес перенаправления для мобильных устройств) введите **URL-адрес выхода**, который был введен в разделе **Домены и URL-адреса приложения Workday** на портале Azure.
   
    c. На портале Azure в окне **Настройка единого входа** скопируйте значение **URL-адрес выхода** и вставьте его в текстовое поле **Logout Redirect URL** (URL-адрес перенаправления для выхода).

    d. В текстовом поле **Used for Environments** (Используется для сред) выберите имя среды.  

    >[!NOTE]
    > Значение атрибута "Среда" привязано к значению URL-адреса клиента.  
    >Если URL-адрес имени домена клиента Workday начинается с impl (например: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), атрибуту **Environment** нужно присвоить значение Implementation.  
    >Если имя домена начинается с другого значения, следует обратиться к [группе поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html), чтобы получить соответствующее значение атрибута **Environment**.

11. В разделе **Настройка SAML** выполните следующие действия.
   
    ![Настройка SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Настройка SAML")
   
    a.  Установите флажок **Включить проверку подлинности SAML**.
   
    Б.  Нажмите кнопку **Добавить строку**.

12. В разделе **SAML Identity Providers** (Поставщики удостоверений SAML) выполните следующие действия.
   
    ![Поставщики удостоверений SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Поставщики удостоверений SAML")
   
    a. В текстовом поле **Имя поставщика удостоверений** введите имя поставщика (например, *SPInitiatedSSO*).
   
    Б. На портале Azure в окне **Настройка единого входа** скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) и вставьте его в текстовое поле **Issuer** (Издатель).

    ![Поставщики удостоверений SAML](./media/active-directory-saas-workday-tutorial/IC7829272.png "Поставщики удостоверений SAML")
   
    c. На портале Azure в окне **Настройка единого входа** скопируйте значение **URL-адрес выхода** и вставьте его в текстовое поле **Logout Response URL** (URL-адрес ответа выхода).

    d. На портале Azure в окне **Настройка единого входа** скопируйте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) и вставьте его в текстовое поле **IdP SSO Service URL** (URL-адрес службы единого входа IdP).

    д. В текстовом поле **Used for Environments** (Используется для сред) выберите имя среды.

    f. Щелкните **Identity Provider Public Key Certificate** (Сертификат открытого ключа поставщика удостоверений), а затем нажмите кнопку **Создать**. 

    ![Создание](./media/active-directory-saas-workday-tutorial/IC782928.png "Создание")

    ж. Щелкните **Create x509 Public Key**(Создать открытый ключ x509). 

    ![Создание](./media/active-directory-saas-workday-tutorial/IC782929.png "Создание")

13. В разделе **View x509 Public Key** (Просмотр открытого ключа x509) сделайте следующее. 
   
    ![Просмотр открытого ключа x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Просмотр открытого ключа x509") 
   
    a. В текстовом поле **Name** (Имя) введите имя сертификата (например, *PPE\_SP*).
   
    Б. В текстовом поле **Действителен с** введите начальную дату действия сертификата.
   
    c.  В текстовом поле **Действителен до** введите конечную дату действия сертификата.
   
    > [!NOTE]
    > Чтобы получить начальную и конечную даты, дважды щелкните скачанный сертификат.  Даты указаны на вкладке **Подробности** .
    > 
    >
   
    d.  Откройте сертификат в кодировке Base-64 в блокноте и скопируйте его содержимое.
   
    д.  Вставьте содержимое буфера обмена в текстовое поле **Сертификат** .
   
    f.  Последовательно выберите **ОК**.

14. Выполните следующие действия: 
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "Настройка единого входа")
   
    a.  В текстовом поле **Service Provider ID** (Идентификатор поставщика службы) введите **http://www.workday.com**.
   
    Б. Выберите параметр **Не отклонять запрос проверки подлинности, инициированный поставщиком услуг**.
   
    c. Для параметра **Authentication Request Signature Method** (Метод подписи запроса аутентификации) выберите значение **SHA256**. 
   
    ![Метод подписи запроса аутентификации](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Метод подписи запроса аутентификации") 
   
    d. Последовательно выберите **ОК**. 
   
    ![ОК](./media/active-directory-saas-workday-tutorial/IC782933.png "ОК")

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-workday-test-user"></a>Создание тестового пользователя Workday

В этом разделе описано, как создать пользователя Britta Simon в приложении Workday. Обратитесь к [группе поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html), чтобы добавить пользователей на платформу Workday. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Workday.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Workday, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Workday**.

    ![Ссылка на Workday в списке "Приложения"](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Workday" на панели доступа, вы автоматически войдете в приложение Workday.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png
