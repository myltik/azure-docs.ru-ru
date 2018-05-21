---
title: Руководство по интеграции Azure Active Directory с Salesforce | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 2caf788f1c38c701699e0c275cabe3d12eb75270
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Руководство по интеграции Azure Active Directory с Salesforce

В этом руководстве описано, как интегрировать Salesforce с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Salesforce обеспечивает следующие преимущества.

- С помощью Azure AD можно управлять доступом к Salesforce.
- Вы можете включить автоматический вход пользователей в Salesforce (единый вход) с помощью их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Salesforce, вам потребуется:

- подписка Azure AD;
- подписка Salesforce с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Salesforce из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-salesforce-from-the-gallery"></a>Добавление Salesforce из коллекции
Чтобы настроить интеграцию приложения Salesforce с Azure AD, вам нужно добавить Salesforce из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Salesforce**, выберите **Salesforce** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Salesforce в списке результатов](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описано, как настроить и проверить единый вход Azure AD в Salesforce с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Salesforce соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce.

Для этого назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Salesforce.

Чтобы настроить и проверить единый вход Azure AD в Salesforce, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Salesforce](#create-a-salesforce-test-user)** требуется для создания в Salesforce пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Salesforce.

**Чтобы настроить единый вход Azure AD в Salesforce, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Salesforce** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. В разделе **Домен и URL-адреса Salesforce** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. В текстовом поле **URL-адрес для входа** введите значение в следующем формате:
    
    Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`

    Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    Б. В текстовое поле **Идентификатор** введите значение в следующем формате:
    
    Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`

    Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов Salesforce](https://help.salesforce.com/support).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Salesforce** щелкните **Настроить Salesforce**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce.

8. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. В области навигации прокрутите вниз до элемента **Settings** (Параметры) и щелкните **Identity** (Удостоверение), чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Если не удается включить параметры единого входа для своей учетной записи Salesforce, возможно, вам придется обратиться за помощью в [службу поддержки Salesforce](https://help.salesforce.com/support). 

11. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

      ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Чтобы настроить параметры единого входа SAML, щелкните **Создать**.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. На странице **Изменение параметров единого входа на основе SAML** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. В поле **Имя** введите понятное имя конфигурации. При вводе значения в поле **Name** (Имя) текстовое поле **API Name** (Имя API) заполняется автоматически.

    Б. В поле **Issuer** (Издатель) вставьте **идентификатор сущности SAML**, скопированный на портале Azure.

    c. Введите в текстовое поле **Идентификатор сущности**имя домена Salesforce в следующем формате.
      
      * Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`
      * Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Для отправки **сертификата поставщика удостоверений** нажмите кнопку **Choose File** (Выбрать файл), чтобы найти и выбрать файл сертификата, скачанный с портала Azure.

    д. Для **типа удостоверения SAML** выберите один из следующих вариантов:
    
      * Выберите **Assertion contains the User's Salesforce username** (Утверждение содержит имя пользователя Salesforce), если имя пользователя Salesforce передается в утверждении SAML.

      * Выберите **Assertion contains the Federation ID from the User object** (Утверждение содержит идентификатор федерации из объекта-пользователя), если идентификатор федерации из объекта-пользователя передается в утверждении SAML.

      * Выберите **Assertion contains the User ID from the User object** (Утверждение содержит идентификатор пользователя из объекта-пользователя), если идентификатор пользователя из объекта-пользователя передается в утверждении SAML.

    f. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).

    ж. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициируемых поставщиком услуг) выберите значение **HTTP Redirect** (Перенаправление HTTP).

    h. В текстовое поле **Identity Provider Login URL** (URL-адрес входа IdP) вставьте значение **URL-адреса службы единого входа**, скопированное на портале Azure.
    
    i. Наконец, нажмите кнопку **Сохранить** , чтобы применить параметры единого входа SAML.

14. В области навигации слева в Salesforce щелкните **Company Settings** (Параметры компании), чтобы развернуть соответствующий раздел, и выберите пункт **My Domain** (Мой домен).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Прокрутите страницу вниз до раздела **Authentication Configuration** (Конфигурация аутентификации) и нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. В разделе **Authentication Configuration** (Конфигурация аутентификации), установите флажок **Login Page** (Страница входа) для параметра **Authentication Service** (Служба аутентификации) конфигурации единого входа SAML и нажмите кнопку **Save** (Сохранить).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Если выбрано несколько служб проверки подлинности, то при попытке инициировать единый вход в среду Salesforce пользователям будет предложено уточнить, с помощью какой службы проверки подлинности нужно выполнить вход. Чтобы этого избежать, **снимите флажки для всех других служб проверки подлинности**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-salesforce-test-user"></a>Создание тестового пользователя Salesforce

В этом разделе вы создадите в Salesforce пользователя с именем Britta Simon. Приложение Salesforce поддерживает JIT-подготовку. Эта функция включена по умолчанию.
В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce еще не существует, он создается при попытке доступа к приложению Salesforce.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Salesforce.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Salesforce, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Salesforce**.

    ![Ссылка на Salesforce в списке приложений](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Salesforce на панели доступа, вы автоматически войдете в приложение Salesforce.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

