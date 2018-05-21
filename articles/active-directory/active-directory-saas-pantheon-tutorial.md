---
title: Руководство по интеграции Azure Active Directory с Pantheon | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Pantheon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 9e464c24128ab0b55ac23e28e7c9a2d3a18e96aa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Руководство по интеграции Azure Active Directory с Pantheon

В этом руководстве описано, как интегрировать Pantheon с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Pantheon обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Pantheon.
- Вы можете включить автоматический вход пользователей в Pantheon (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Pantheon, вам потребуется:

- подписка Azure AD;
- подписка Pantheon с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Pantheon из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-pantheon-from-the-gallery"></a>Добавление Pantheon из коллекции
Чтобы настроить интеграцию Pantheon с Azure AD, необходимо добавить Pantheon из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pantheon из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Pantheon**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_search.png)

5. На панели результатов выберите **Pantheon** и щелкните **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Pantheon с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Pantheon соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pantheon.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pantheon.

Чтобы настроить и проверить единый вход Azure AD в Pantheon, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pantheon](#creating-a-pantheon-test-user)** требуется для создания в Pantheon пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Pantheon.

**Чтобы настроить единый вход Azure AD в Pantheon, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Pantheon** выберите **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Pantheon** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `urn:auth0:pantheon:<orgname>-SSO`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки Pantheon](https://pantheon.io/docs/getting-support/).

4. Приложение Pantheon ожидает получить утверждение SAML в определенном формате. Для этого формата потребуется указать адрес электронной почты пользователя в качестве значения атрибута UserIdentifier. По умолчанию Azure AD использует для этого атрибута значение UserPrincipalName. Для успешной интеграции это значение следует изменить так, чтобы оно совпадало с адресом электронной почты пользователя. Интеграция будет работать только после выполнения правильного сопоставления.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_attribute.png)  


5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_general_400.png)

7. В разделе **Настройка Pantheon** выберите **Настроить Pantheon**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_configure.png) 

8. Чтобы настроить единый вход на стороне **Pantheon**, нужно отправить скачанный **сертификат** и **URL-адрес службы единого входа SAML** в [службу поддержки Pantheon](https://pantheon.io/docs/getting-support/).

     > [!Note]
     > Чтобы включить эту связь, необходимо также предоставить сведения о доменах электронной почты и о формате даты и времени. Дополнительные сведения см. [здесь](https://pantheon.io/docs/sso-organizations/).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-pantheon-test-user"></a>Создание тестового пользователя Pantheon

В этом разделе описано, как создать пользователя Britta Simon в приложении Pantheon. Выполните следующие ниже действия, чтобы добавить пользователя в Pantheon. 

>[!NOTE] 
>Чтобы единый вход начал работать, необходимо сначала создать пользователя в Pantheon.

1. Войдите в Pantheon с учетными данными администратора.

2. Перейдите на страницу **Organization** (Организация) панели мониторинга.
 
3. Выберите параметр **Пользователи**.

4. Нажмите кнопку **Добавить пользователя**.

5. Введите адрес электронной почты пользователя.

6. Выберите роль пользователя.

7. Нажмите кнопку **Добавить пользователя**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Pantheon, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pantheon, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pantheon**.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Pantheon на панели доступа, вы автоматически войдете в приложение Pantheon.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_203.png

