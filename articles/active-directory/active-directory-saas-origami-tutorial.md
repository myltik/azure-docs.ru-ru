---
title: Руководство по интеграции Azure Active Directory с Origami | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: c8943d6cdda21970167846d9301556d20750614d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Руководство. Интеграция Azure Active Directory с Origami

В этом учебнике описано, как интегрировать Origami с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Origami обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Origami.
- Вы можете включить автоматический вход пользователей в Origami (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Origami, вам потребуется:

- подписка Azure AD;
- подписка Origami с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Origami из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-origami-from-the-gallery"></a>Добавление Origami из коллекции
Чтобы настроить интеграцию Origami с Azure AD, необходимо добавить Origami из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Origami из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Origami**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_search.png)

5. На панели результатов выберите **Origami** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Origami с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Origami соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Origami.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Origami.

Чтобы настроить и проверить единый вход Azure AD в Origami, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Origami](#creating-an-origami-test-user)** требуется для создания в Origami пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Origami.

**Чтобы настроить единый вход Azure AD в Origami, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Origami** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Origami** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Origami](https://wordpress.org/support/theme/origami). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Origami** щелкните **Настроить Origami**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**

    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_configure.png) 

7. Войдите в систему, используя учетную запись Origami с правами администратора.

8. В верхнем меню щелкните **Администратор**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

9. На странице диалогового окна "Настройка единого входа" сделайте следующее.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_531.png)

    a. Выберите пункт **Включить единый вход**.

    Б. В текстовое поле **Identity Provider's Sign-in Page URL** (URL-адрес страницы входа поставщика удостоверений) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    c. В текстовом поле **Identity Provider's Sign-out Page URL** (URL-адрес страницы выхода поставщика удостоверений) вставьте значение **URL-адрес выхода**, которое вы скопировали на портале Azure.

    d. Нажмите кнопку **Browse** (Обзор), чтобы отправить файл метаданных, скачанный с портала Azure.

    д. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-origami-test-user"></a>Создание тестового пользователя Origami

В этом разделе описано, как создать пользователя Britta Simon в приложении Origami. 

1. Войдите в систему, используя учетную запись Origami с правами администратора.

2. В верхнем меню щелкните **Администратор**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. В диалоговом окне **Users and Security** (Пользователи и безопасность) нажмите кнопку **Users** (Пользователи).
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Нажмите кнопку **Add New User**(Добавить нового пользователя).
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. В диалоговом окне добавления нового пользователя выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    a. В текстовом поле **User Name** (Имя пользователя) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    Б. В текстовом поле **Пароль** введите пароль.

    c. В текстовом поле **подтверждения пароля** введите пароль еще раз.

    d. В текстовом поле **First Name** (Имя) введите имя, допустим, **Britta**.

    д. В текстовом поле **Last Name** (Фамилия) введите фамилию, предположим, **Simon**.

    f. Выберите команду **Сохранить**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

6. Назначьте **роли пользователя** и уровень **клиентского доступа** для пользователя. 
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Origami.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Origami, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Origami**.

    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Origami на панели доступа, вы автоматически войдете в приложение Origami.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-origami-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png

