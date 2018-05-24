---
title: Руководство по интеграции Azure Active Directory с Soonr Workplace | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Soonr Workplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.openlocfilehash: 1725ebd39a5d976fee061ed92ddcc686fa7a1acd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Учебник. Интеграция Azure Active Directory с Soonr Workplace

В этом руководстве описано, как интегрировать Soonr Workplace с Azure Active Directory (AAD).

Интеграция Soonr Workplace с Azure AD дает приведенные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению Soonr Workplace.
- Вы можете включить автоматический вход пользователей в Soonr Workplace (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Soonr Workplace, вам потребуется:

- подписка Azure AD;
- подписка Soonr Workplace с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Soonr Workplace из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-soonr-workplace-from-the-gallery"></a>Добавление Soonr Workplace из коллекции.
Чтобы настроить интеграцию Soonr Workplace с Azure AD, необходимо добавить Soonr Workplace из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Soonr Workplace из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Soonr Workplace**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_search.png)

5. В области результатов выберите **Soonr Workplace** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа AAD в приложение Soonr Workplace с использованием данных тестового пользователя Britta Simon.

Чтобы настроить единый вход AAD необходимо знать, какой пользователь в Soonr Workplace соответствует пользователю в AAD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Soonr Workplace.

Чтобы установить эту связь, назначьте **имя пользователя** в AAD в качестве значения **имени пользователя** в Soonr Workplace.

Чтобы настроить и проверить единый вход Azure AD в Soonr Workplace, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Soonr Workplace](#creating-a-soonr-workplace-test-user)** требуется для создания в Soonr Workplace соответствующего пользователя Britta Simon, связанного с соответствующим представлением в AAD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход AAD на портале Azure и настроить его в приложении Soonr Workplace.

**Чтобы настроить единый вход Azure AD в Soonr Workplace, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением  **Workplace** выберите пункт **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Soonr Workplace** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<servername>.soonr.com/singlesignon/saml/SSO`.

4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то в разделе **Домены и URL-адреса приложения Soonr Workplace**:
    
    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_url1.png)

    a. Установите флажок **Показать дополнительные параметры URL-адресов**.

    Б. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<servername>.soonr.com/singlesignon/saml/SSO`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса входа и ответа. Чтобы получить это значение, обратитесь в [службу поддержки Soonr Workplace](https://awp.autotask.net/help/).
 
5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_general_400.png)

7. В разделе **Настройка Soonr Workplace** щелкните **Настроить Soonr Workplace**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_configure.png) 

8. Чтобы настроить единый вход на стороне **Soonr Workplace**, нужно отправить скачанный **XML-файл метаданных**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки Soonr Workplace](https://awp.autotask.net/help/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

    >[!Note]
    >Если вам требуется помощь по настройке Autotask Workplace и учетной записи Workplace, см. [эту страницу](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Создание тестового пользователя Soonr Workplace

Цель этого раздела — создать в приложении Soonr Workplace пользователя с именем Britta Simon. Чтобы создать пользователя на этой платформе, свяжитесь со [службой поддержки Soonr Workplace](https://awp.autotask.net/help/). Создать запрос в службу поддержки Soonr можно <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">здесь</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Soonr Workplace.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Soonr Workplace, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Soonr Workplace**.

    ![Настройка единого входа](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент Soonr Workplace на панели доступа, вы автоматически войдете в приложение Soonr Workplace.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png

