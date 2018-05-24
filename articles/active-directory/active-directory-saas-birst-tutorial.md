---
title: Руководство по интеграции Azure Active Directory с Birst Agile Business Analytics | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Birst Agile Business Analytics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e6ff23d76710cf1f63e7b0439e27760eed333c1b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Руководство. Интеграция Azure Active Directory с Birst Agile Business Analytics

В этом руководстве описано, как интегрировать Birst Agile Business Analytics с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Birst Agile Business Analytics обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Birst Agile Business Analytics.
- Вы можете включить автоматический вход пользователей в Birst Agile Business Analytics (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Birst Agile Business Analytics, вам потребуется:

- подписка Azure AD;
- подписка на Birst Agile Business Analytics с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Birst Agile Business Analytics из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Добавление Birst Agile Business Analytics из коллекции
Чтобы настроить интеграцию Birst Agile Business Analytics с Azure AD, необходимо добавить Birst Agile Business Analytics из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Birst Agile Business Analytics из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Birst Agile Business Analytics**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-birst-tutorial/tutorial_birst_search.png)

5. В области результатов выберите **Birst Agile Business Analytics** и нажмите кнопку **Добавить**, чтобы добавить эту программу.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Birst Agile Business Analytics с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Birst Agile Business Analytics соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Birst Agile Business Analytics.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Birst Agile Business Analytics.

Чтобы настроить и проверить единый вход Azure AD в Birst Agile Business Analytics, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Birst Agile Business Analytics](#creating-a-birst-agile-business-analytics-test-user)** нужно для того, чтобы в Birst Agile Business Analytics также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на новом Azure и настроить его в приложении Birst Agile Business Analytics.

**Чтобы настроить единый вход Azure AD в Birst Agile Business Analytics, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Birst Agile Business Analytics** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-birst-tutorial/tutorial_birst_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Birst Agile Business Analytics** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-birst-tutorial/tutorial_birst_url.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     URL-адрес зависит от центра обработки данных, в котором расположена учетная запись Birst. 

     * Для центра обработки данных в США используйте шаблон `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`. 

     * Для центра обработки данных в Европе используйте шаблон `https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь к [группе поддержки Birst Agile Business Analytics](mailto:info@birst.com) для получения этого значения. 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-birst-tutorial/tutorial_birst_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-birst-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Birst Agile Business Analytics** щелкните **Настроить Birst Agile Business Analytics**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-birst-tutorial/tutorial_birst_configure.png) 

7. Чтобы настроить единый вход на стороне **Birst Agile Business Analytics**, нужно отправить скачанный **сертификат (Base64)**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [группе поддержки Birst Agile Business Analytics](mailto:info@birst.com). 

    > [!NOTE]
    > Укажите в письме к группе поддержки Birst, что для интеграции необходимо использовать алгоритм SHA256 (SHA1 не будет поддерживаться), чтобы специалисты по поддержке настроили единый вход на соответствующем сервере, например **app2101**.
  

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-birst-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Создание тестового пользователя Birst Agile Business Analytics

В этом разделе описано, как создать пользователя с именем Britta Simon в Birst Agile Business Analytics. Для добавления пользователей в учетную запись Birst следует обратиться к [группе поддержки Birst Agile Business Analytics](mailto:info@birst.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Birst Agile Business Analytics.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Birst Agile Business Analytics, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Birst Agile Business Analytics**.

    ![Настройка единого входа](./media/active-directory-saas-birst-tutorial/tutorial_birst_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Birst Agile Business Analytics на панели доступа, вы автоматически войдете в приложение Birst Agile Business Analytics. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-birst-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-birst-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-birst-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-birst-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-birst-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-birst-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-birst-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-birst-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-birst-tutorial/tutorial_general_203.png

