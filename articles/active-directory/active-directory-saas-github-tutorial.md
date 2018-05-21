---
title: Руководство. Интеграция Azure Active Directory с GitHub | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ef9d0bb112738e9abea5894fb0f321368b7fc26e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Руководство. Интеграция Azure Active Directory с GitHub

В этом руководстве описано, как интегрировать GitHub с Azure Active Directory (Azure AD).

Интеграция Azure AD с GitHub обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к GitHub.
- Вы можете включить автоматический вход пользователей в GitHub (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с GitHub, вам потребуется:

- подписка Azure AD;
- подписка GitHub с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление GitHub из коллекции.
2. настройка и проверка единого входа в Azure AD.


## <a name="adding-github-from-the-gallery"></a>Добавление GitHub из коллекции
Чтобы настроить интеграцию GitHub с Azure AD, необходимо добавить GitHub из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение GitHub из коллекции, сделайте следующее.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Github.com**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. На панели результатов выберите **GitHub** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описано, как настроить и проверить единый вход Azure AD в GitHub с использованием тестового пользователя Britta Simon.

Для настройки единого входа в Azure AD необходимо знать, какой пользователь в GitHub соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в GitHub.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в GitHub.

Чтобы настроить и проверить единый вход Azure AD в GitHub, потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя GitHub](#creating-a-GitHub-test-user)** требуется для создания в GitHub пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении GitHub.

**Чтобы настроить единый вход Azure AD в GitHub, сделайте следующее.**

1. На портале управления Azure на странице интеграции с приложением **GitHub** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. В разделе **Домены и URL-адреса приложения GitHub** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. В текстовом поле **URL-адрес для входа** введите значение `https://github.com/orgs/<entity-id>/sso`.

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Мы рекомендуем использовать уникальное значение строки идентификатора. Перейдите в раздел администрирования GitHub, чтобы получить эти значения. 

4. В разделе **Атрибуты пользователя** выберите значение user.mail в качестве параметра **Идентификатор пользователя**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. В разделе **Настройка GitHub** щелкните **Настроить GitHub**, чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. В другом окне веб-браузера войдите на свой корпоративный сайт GitHub в качестве администратора.

12. Перейдите к разделу **Параметры** и щелкните **Безопасность**.

    ![Параметры](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Установите флажок **Enable SAML authentication** (Включить проверку подлинности SAML). После этого появятся поля конфигурации единого входа. Затем с помощью значения URL-адреса единого входа обновите URL-адрес единого входа в конфигурации Azure AD.

    ![Параметры](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Задайте значения в следующих полях:

    a. **URL-адрес для входа.** Введите **URL-адрес службы единого входа в SAML** из раздела **Настройка GitHub** в Azure AD.

    Б. **Издатель.** Введите **идентификатор сущности SAML** из раздела **Настройка GitHub** в Azure AD.

    c. **Public Certificate** (Общий сертификат). Откройте скачанный сертификат из Azure AD в блокноте и скопируйте его содержимое, включая строки BEGIN CERTIFICATE и END CERTIFICATE.

    ![Параметры](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Щелкните **Test SAML configuration** (Проверка конфигурации SAML), чтобы убедиться в отсутствии сбоев проверки при выполнении единого входа.

    ![Параметры](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Нажмите кнопку **Сохранить**

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 


### <a name="creating-a-github-test-user"></a>Создание тестового пользователя GitHub

Чтобы пользователи Azure AD могли выполнять вход в GitHub, они должны быть подготовлены для GitHub.  
В случае с GitHub подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Выполните вход на свой корпоративный сайт Github в качестве администратора.

2. Выберите параметр **Пользователи**.

    ![Люди](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "Люди")

3. Нажмите **Invite member** (Пригласить участника).

    ![Приглашение пользователей](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "приглашение пользователей")

4. На странице диалогового окна **Invite member** (Приглашение участников) сделайте следующее.

    a. В текстовом поле **Электронная почта** введите адрес электронной почты учетной записи Britta Simon.

    ![Приглашение участников](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "приглашение участников")
    
    Б. Щелкните **Send Invitation** (Отправить приглашение).

    ![Приглашение участников](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "приглашение участников")

    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к GitHub.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении GitHub, сделайте следующее.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **GitHub.com**.

    ![Настройка единого входа](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "GitHub" на панели доступа, вы автоматически войдете в приложение GitHub. Будет выполнен вход с использованием учетной записи организации, но затем нужно будет войти с помощью личной учетной записи.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
