---
title: "Руководство. Интеграция Azure Active Directory с Workday | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Workday."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Руководство. Интеграция Azure Active Directory с Workday

В этом руководстве описано, как интегрировать Workday с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Workday обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Workday.
- Вы можете включить автоматический вход пользователей в Workday (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Workday, вам потребуется:

- подписка Azure AD;
- подписка Workday с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Workday из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-workday-from-the-gallery"></a>Добавление Workday из коллекции
Чтобы настроить интеграцию Workday с Azure AD, необходимо добавить Workday из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workday из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Workday**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. На панели результатов выберите **Workday** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Workday с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Workday соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workday.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workday.

Чтобы настроить и проверить единый вход Azure AD в Workday, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Workday](#creating-a-workday-test-user)** требуется для того, чтобы в Workday существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Workday.

**Чтобы настроить единый вход Azure AD в Workday, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Workday** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Workday** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    а. В текстовом поле **URL-адрес для входа** введите значение `https://impl.workday.com/<tenant>/login-saml2.htmld`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://impl.workday.com/<tenant>/login-saml.htmld`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями URL-адреса входа и URL-адреса ответа. URL-адрес ответа должен содержать поддомен (например, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Можно указать адрес вида *http://www.myworkday.com*, но формат *http://myworkday.com* не поддерживается. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html). 
 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Workday** щелкните **Настроить Workday**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. В другом окне веб-браузера войдите на свой корпоративный сайт Workday в качестве администратора.

8. Выберите **меню \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

9. Перейдите в раздел **Администрирование учетной записи**.
   
    ![Администрирование учетной записи](./media/active-directory-saas-workday-tutorial/IC782924.png "Администрирование учетной записи")

10. Выберите **Изменение настройки клиента — безопасность**.
   
    ![Изменение параметров безопасности клиента](./media/active-directory-saas-workday-tutorial/IC782925.png "Изменение параметров безопасности клиента")

11. В разделе **URL-адреса перенаправления** выполните следующие действия.
   
    ![URL-адреса перенаправления](./media/active-directory-saas-workday-tutorial/IC7829581.png "URL-адреса перенаправления")
   
    а. Нажмите кнопку **Добавить строку**.
   
    b. В текстовых полях **Login Redirect URL** (URL-адрес перенаправления для входа) и **Mobile Redirect URL** (URL-адрес перенаправления для мобильных устройств) введите **URL-адрес выхода**, который был введен в разделе **Домены и URL-адреса приложения Workday** на портале Azure.
   
    c. На портале Azure в окне **Настройка единого входа** скопируйте значение **URL-адрес выхода** и вставьте его в текстовое поле **Logout Redirect URL** (URL-адрес перенаправления для выхода).
   
    г)  В текстовом поле **Среда** введите имя среды.  

    >[!NOTE]
    > Значение атрибута "Среда" привязано к значению URL-адреса клиента.  
    >Если URL-адрес имени домена клиента Workday начинается с impl (например: *https://impl.workday.com/\<клиент\>/login-saml2.htmld*), атрибуту **Environment** должно быть присвоено значение Implementation.  
    >Если имя домена начинается с другого значения, следует обратиться к [группе поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html), чтобы получить соответствующее значение атрибута **Environment**.

12. В разделе **Настройка SAML** выполните следующие действия.
   
    ![Настройка SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Настройка SAML")
   
    а.  Установите флажок **Включить проверку подлинности SAML**.
   
    b.  Нажмите кнопку **Добавить строку**.

13. В разделе Поставщики удостоверений SAML" выполните следующие действия.
   
    ![Поставщики удостоверений SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Поставщики удостоверений SAML")
   
    а. В текстовое поле "Имя поставщика удостоверений" введите имя поставщика (например, *SPInitiatedSSO*).
   
    b. На портале Azure в окне **Настройка единого входа** скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) и вставьте его в текстовое поле **Issuer** (Издатель).
   
    c. Установите флажок **Enable Workday Initiated Logout** (Включить выход, инициируемый Workday).
   
    г) На портале Azure в окне **Настройка единого входа** скопируйте значение **URL-адрес выхода** и вставьте его в текстовое поле **Logout Request URL** (URL-адрес запроса выхода).

    д. Щелкните **Identity Provider Public Key Certificate** (Сертификат открытого ключа поставщика удостоверений), а затем нажмите кнопку **Создать**. 

    ![Создание](./media/active-directory-saas-workday-tutorial/IC782928.png "Создание")

    Е. Щелкните **Create x509 Public Key**(Создать открытый ключ x509). 

    ![Создание](./media/active-directory-saas-workday-tutorial/IC782929.png "Создание")


14. В разделе **View x509 Public Key** (Просмотр открытого ключа x509) сделайте следующее. 
   
    ![Просмотр открытого ключа x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Просмотр открытого ключа x509") 
   
    а. В текстовом поле **Name** (Имя) введите имя сертификата (например, *PPE\_SP*).
   
    b. В текстовом поле **Действителен с** введите начальную дату действия сертификата.
   
    c.  В текстовом поле **Действителен до** введите конечную дату действия сертификата.
   
    > [!NOTE]
    > Чтобы получить начальную и конечную даты, дважды щелкните скачанный сертификат.  Даты указаны на вкладке **Подробности** .
    > 
    >
   
    г)  Откройте сертификат в кодировке Base-64 в блокноте и скопируйте его содержимое.
   
    д.  Вставьте содержимое буфера обмена в текстовое поле **Сертификат** .
   
    f.  Нажмите кнопку **ОК**.

15. Выполните следующие действия: 
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "Настройка единого входа")
   
    а.  Установите флажок **Пара закрытых ключей x509**.
   
    b.  В текстовом поле **Идентификатор поставщика службы** введите **http://www.workday.com**.
   
    В.  Установите флажок **Включить проверку подлинности SAML, инициированную поставщиком услуг**.
   
    г)  На портале Azure в окне **Настройка единого входа** скопируйте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) и вставьте его в текстовое поле **IdP SSO Service URL** (URL-адрес службы единого входа IdP).
   
    д. Выберите параметр **Не отклонять запрос проверки подлинности, инициированный поставщиком услуг**.
   
    Е. Для параметра **Authentication Request Signature Method** (Метод подписи запроса аутентификации) выберите значение **SHA256**. 
   
    ![Метод подписи запроса аутентификации](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Метод подписи запроса аутентификации") 
   
    g. Нажмите кнопку **ОК**. 
   
    ![ОК](./media/active-directory-saas-workday-tutorial/IC782933.png "ОК")
<CE>

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-workday-test-user"></a>Создание тестового пользователя Workday

Чтобы подготовить тестового пользователя в Workday, обратитесь к [группе поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html).
[Группа поддержки клиентов Workday](https://www.workday.com/en-us/partners-services/services/support.html) создаст пользователя для вас.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Workday.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Workday, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Workday**.

    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Руководство по настройке Workday для автоматической подготовки пользователей с помощью локальной службы Active Directory и Azure Active Directory](active-directory-saas-workday-inbound-tutorial.md)



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


