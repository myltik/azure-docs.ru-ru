---
title: Руководство по интеграции Azure Active Directory с myPolicies | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении myPolicies.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bf79e858-1dfb-4ab3-a6df-74b2d5a878d2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: jeedes
ms.openlocfilehash: 22bc26190971ef8ceed409a70d45be5e049d7401
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mypolicies"></a>Руководство по интеграции Azure Active Directory с myPolicies

В этом руководстве описано, как интегрировать приложение myPolicies с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением myPolicies обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к myPolicies.
- Вы можете включить автоматический вход пользователей в myPolicies (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с myPolicies, вам потребуется:

- подписка Azure AD;
- подписка myPolicies с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление myPolicies из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mypolicies-from-the-gallery"></a>Добавление myPolicies из коллекции
Чтобы настроить интеграцию myPolicies с Azure AD, необходимо добавить myPolicies из коллекции в список управляемых приложений SaaS.

**Чтобы добавить myPolicies из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **myPolicies**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_search.png)

5. На панели результатов выберите **myPolicies** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в myPolicies с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в myPolicies соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в myPolicies.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в myPolicies.

Чтобы настроить и проверить единый вход Azure AD в myPolicies, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя myPolicies](#creating-a-mypolicies-test-user)** требуется для того, чтобы в myPolicies существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении myPolicies.

**Чтобы настроить единый вход Azure AD в myPolicies, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **myPolicies** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_samlbase.png)

3. В разделе **Домены и URL-адреса приложения myPolicies** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<tenantname>.mypolicies.com/`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<tenantname>.mypolicies.com/users/auth/saml/callback`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки myPolicies](mailto:support@mypolicies.com).

4. Приложение myPolicies ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример. 

    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_attribute.png)

5. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей**, чтобы развернуть атрибуты. Выполните следующие действия для каждого отображаемого атрибута.

    | Имя атрибута | Значение атрибута |
    | ------------------- | ---------- |
    | givenname | user.givenname |
    | surname | user.surname |
    | emailaddress | user.mail |
    | name | user.userprincipalname |
    
    a. Щелкните атрибут, чтобы открыть диалоговое окно **Изменить атрибут**.
    
    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_attribute_05.png)
    
    Б. Удалите значение URL-адреса из **пространства имен**.
    
    c. Нажмите кнопку **ОК**, чтобы сохранить настройки.
    
6. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_general_400.png)

8. В разделе **Конфигурация myPolicies** щелкните **Настроить myPolicies**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_configure.png) 

9. Чтобы настроить единый вход на стороне **myPolicies**, нужно отправить скачанный **сертификат в кодировке Base64** и **URL-адрес службы единого входа SAML** [группе поддержки myPolicies](mailto:support@mypolicies.com). 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-mypolicies-test-user"></a>Создание тестового пользователя myPolicies

В этом разделе описано, как создать пользователя Britta Simon в myPolicies. Обратитесь к [группе поддержки myPolicies](mailto:support@mypolicies.com), чтобы добавить пользователей на платформу myPolicies. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к myPolicies.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в myPolicies, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **myPolicies**.

    ![Настройка единого входа](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "myPolicies" на панели доступа, вы автоматически войдете в приложение myPolicies.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_203.png

