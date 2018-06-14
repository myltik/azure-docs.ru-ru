---
title: Руководство по интеграции Azure Active Directory с FM:Systems | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и FM:Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 726b9c8001cb7b87c577bea6813ebe95e903a04d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344217"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Руководство по интеграции Azure Active Directory с FM:Systems

В этом учебнике описано, как интегрировать FM:Systems с Azure Active Directory (Azure AD).

Интеграция FM:Systems с Azure AD обеспечивает следующие преимущества:

- Вы сможете контролировать доступ к FM:Systems с помощью Azure AD
- Вы можете включить автоматический вход пользователей в FM:Systems (единый вход) с учетной записью Azure AD
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с FM:Systems, вам потребуется:

- подписка Azure AD;
- подписка FM:Systems с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление FM:Systems из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-fmsystems-from-the-gallery"></a>Добавление FM:Systems из коллекции
Чтобы настроить интеграцию FM:Systems с Azure AD, необходимо добавить FM:Systems из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FM:Systems из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **FM:Systems**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_search.png)

5. На панели результатов выберите **FM:Systems** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение FM:Systems для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в FM:Systems соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FM:Systems.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в FM:Systems.

Чтобы настроить и проверить единый вход Azure AD в FM:Systems, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя FM:Systems](#creating-an-fmsystems-test-user)** требуется для создания пользователя Britta Simon в FM:Systems, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении FM:Systems.

**Чтобы настроить единый вход Azure AD в FM:Systems, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **FM:Systems** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_samlbase.png)

3. В разделе **Домены и URL-адреса FM:Systems** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_url.png)

    В текстовом поле **URL-адрес ответа** введите **URL-адрес ответа** FM:Systems в следующем формате: `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь в [службу поддержки FM:Systems](https://fmsystems.com/ask-us/).
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход на стороне **FM:Systems**, отправьте в [службу поддержки FM:Systems](https://fmsystems.com/ask-us/) скачанный **XML-файл метаданных**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах подключения. Как только единый вход для вашей подписки будет включен, вы получите уведомление.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fm-systems-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-fmsystems-test-user"></a>Создание тестового пользователя FM:Systems

1. В другом окне веб-браузера войдите на ваш корпоративный веб-сайт FM: Systems в качестве администратора.

2. Последовательно выберите **System Administration \> Manage Security \> Users \> User list** (Системное администрирование > Управление безопасностью > Пользователи > Список пользователей).
   
    ![Администрирование системы](./media/active-directory-saas-fm-systems-tutorial/ic795905.png "Администрирование системы")

3. Нажмите **Создать нового пользователя**.
   
    ![Создание пользователя](./media/active-directory-saas-fm-systems-tutorial/ic795906.png "Создание пользователя")

4. В разделе **Создание пользователя** выполните следующие действия.
   
    ![Создание пользователя](./media/active-directory-saas-fm-systems-tutorial/ic795907.png "Создание пользователя")
   
    a. В текстовых полях **Имя пользователя**, **Пароль**, **Подтверждение пароля**, **Адрес электронной почты** и **Идентификатор сотрудника** введите соответствующие данные для действующей учетной записи Azure Active Directory, которую хотите подготовить.
   
    Б. Нажмите кнопку **Далее**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon, предоставив этому пользователю доступ к FM:Systems.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в FM:Systems, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **FM:Systems**.

    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/tutorial_fmsystems_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент FM:Systems на панели доступа, вы автоматически войдете в приложение FM:Systems.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fm-systems-tutorial/tutorial_general_203.png

