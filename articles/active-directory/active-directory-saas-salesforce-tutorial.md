---
title: "Руководство по интеграции Azure Active Directory с Salesforce | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Руководство по интеграции Azure Active Directory с Salesforce

В этом руководстве описано, как интегрировать Salesforce с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Salesforce обеспечивает следующие преимущества.

- С помощью Azure AD можно управлять доступом к Salesforce.
- Вы можете включить автоматический вход пользователей в Salesforce (единый вход) с их учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Salesforce, вам потребуется:

- подписка Azure AD;
- подписка Salesforce с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Salesforce из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-salesforce-from-the-gallery"></a>Добавление Salesforce из коллекции
Чтобы настроить интеграцию приложения Salesforce с Azure AD, вам нужно добавить Salesforce из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Salesforce**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. На панели результатов выберите **Salesforce** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Salesforce с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Salesforce соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Salesforce.

Чтобы настроить и проверить единый вход Azure AD в Salesforce, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Salesforce](#creating-a-salesforce-test-user)** требуется для создания в Salesforce пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Salesforce.

**Чтобы настроить единый вход Azure AD в Salesforce, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Salesforce** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. В разделе **Домен и URL-адреса Salesforce** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    В текстовом поле **URL-адрес для входа** введите значение в следующем формате: 
   * Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`
   * Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа. Чтобы получить их, обратитесь в [службу поддержки клиентов Salesforce](https://help.salesforce.com/support). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Salesforce** щелкните **Настроить Salesforce**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**. 

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce.

8.  В области навигации **Administrator** (Администратор) щелкните **Security Controls** (Управление безопасностью), чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).
    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Если не удается включить параметры единого входа для своей учетной записи Salesforce, возможно, вам придется обратиться за помощью в [службу поддержки Salesforce](https://help.salesforce.com/support). 

10. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

      ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Чтобы настроить параметры единого входа SAML, щелкните **Создать**.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. На странице **Изменение параметров единого входа на основе SAML** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    а. В поле **Имя** введите понятное имя конфигурации. При вводе значения в поле **Name** (Имя) текстовое поле **API Name** (Имя API) заполняется автоматически.

    b. Вставьте значение **идентификатора сущности SMAL** в поле **Издатель** в Salesforce.

    c. Введите в текстовое поле **Идентификатор сущности**имя домена Salesforce в следующем формате.
      
      * Учетная запись предприятия: `https://<subdomain>.my.salesforce.com`
      * Учетная запись разработчика: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    г) Нажмите кнопку **Browse** (Обзор) или **Choose File** (Выбрать файл), чтобы открыть диалоговое окно **Choose File to Upload** (Выбор файла для передачи). Выберите сертификат Salesforce и нажмите кнопку **Open** (Открыть), чтобы передать его.

    д. В поле **SAML Identity Type** (Тип удостоверения SAML) выберите значение **Assertion contains User's salesforce.com username** (Утверждение содержит имя пользователя salesforce.com).

    Е. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).

    g. Вставьте **URL-адрес службы единого входа** в поле **URL-адрес входа поставщика удостоверений** в Salesforce.
    
    h. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициируемых поставщиком услуг) выберите значение **HTTP Redirect** (Перенаправление HTTP).
    
    i. Наконец, нажмите кнопку **Сохранить** , чтобы применить параметры единого входа SAML.

13. В левой области навигации в Salesforce щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, и нажмите кнопку **My Domain** (Мой домен).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Прокрутите страницу вниз до раздела **Authentication Configuration** (Конфигурация аутентификации) и нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. В разделе **Authentication Service** (Служба аутентификации) выберите понятное имя конфигурации единого входа SAML и нажмите кнопку **Save** (Сохранить).

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Если выбрано несколько служб проверки подлинности, то при попытке инициировать единый вход в среду Salesforce пользователям будет предложено уточнить, с помощью какой службы проверки подлинности нужно выполнить вход. Чтобы этого избежать, **снимите флажки для всех других служб проверки подлинности**.
<CE>    
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-salesforce-test-user"></a>Создание тестового пользователя Salesforce

В этом разделе вы создадите в Salesforce пользователя с именем Britta Simon. Приложение Salesforce поддерживает JIT-подготовку. Эта функция включена по умолчанию.
В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce еще не существует, он создается при попытке доступа к приложению Salesforce.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Salesforce.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Salesforce, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Salesforce**.

    ![Настройка единого входа](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com/), выполните вход с тестовой учетной записью и щелкните **Salesforce**.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-salesforce-provisioning-tutorial.md)

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

