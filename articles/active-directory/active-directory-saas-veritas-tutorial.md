---
title: "Руководство. Интеграция Azure Active Directory с Veritas Enterprise Vault.cloud SSO | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Veritas Enterprise Vault.cloud SSO."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a8f9ae76cd914224b3715857b6fd37750f13cd7b
ms.openlocfilehash: 2d6dccdcb7eb6c08d707c2182f9af8fe4824e6f5


---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Руководство. Интеграция Azure Active Directory с Veritas Enterprise Vault.cloud SSO

В этом руководстве описано, как интегрировать Veritas Enterprise Vault.cloud SSO с Azure Active Directory (Azure AD).

Интеграция Veritas Enterprise Vault.cloud SSO с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Veritas Enterprise Vault.cloud SSO.
- Вы можете включить автоматический вход пользователей в Veritas Enterprise Vault.cloud SSO (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Veritas Enterprise Vault.cloud SSO, вам потребуется следующее:

- подписка Azure AD;
- подписка Veritas Enterprise Vault.cloud SSO с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Veritas Enterprise Vault.cloud SSO из коллекции.
2. Настройка и проверка единого входа в Azure AD.


## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Добавление Veritas Enterprise Vault.cloud SSO из коллекции
Чтобы настроить интеграцию Veritas Enterprise Vault.cloud SSO c Azure AD, необходимо добавить Veritas Enterprise Vault.cloud SSO из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Veritas Enterprise Vault.cloud SSO из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Veritas Enterprise Vault.cloud SSO**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_01.png)
7. В области результатов выберите **Veritas Enterprise Vault.cloud SSO**, а затем нажмите кнопку **Завершить**, чтобы добавить приложение.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описывается настройка и проверка единого входа Azure AD в Veritas Enterprise Vault.cloud SSO с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в Veritas Enterprise Vault.cloud SSO соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Veritas Enterprise Vault.cloud SSO.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Veritas Enterprise Vault.cloud SSO.

Чтобы настроить и проверить единый вход Azure AD в Veritas Enterprise Vault.cloud SSO, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Veritas Enterprise Vault.cloud SSO](#creating-a-Veritas Enterprise Vault.cloud SSO-test-user)** требуется для создания пользователя Britta Simon в Veritas Enterprise Vault.cloud SSO, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Veritas Enterprise Vault.cloud SSO.


**Чтобы настроить единый вход Azure AD в Veritas Enterprise Vault.cloud SSO, выполните следующие действия.**

1. На странице интеграции с приложением **Veritas Enterprise Vault.cloud SSO** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Veritas Enterprise Vault.cloud SSO** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_04.png) 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, с помощью которого пользователи входят в приложение Veritas Enterprise Vault.cloud SSO, в следующем формате: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`.
    
     
4. На странице **Настройка единого входа в Veritas Enterprise Vault.cloud SSO** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_05.png)

    а. Щелкните **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Veritas Enterprise Vault.cloud SSO на <a href="https://www.veritas.com/content/support/en_US/62696.html ">веб-сайте поддержки</a> и укажите следующие сведения:

    • скачанный файл **метаданных**

    • **URL-адрес единого входа SAML**

6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
 
    ![единого входа Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Создание тестового пользователя Veritas Enterprise Vault.cloud SSO

В этом разделе описано, как создать пользователя Britta Simon в Veritas Enterprise Vault.cloud SSO. Чтобы добавить пользователей в платформу Veritas Enterprise Vault.cloud SSO, обратитесь в службу поддержки этого решения через <a href="https://www.veritas.com/content/support/en_US/62696.html ">веб-сайт поддержки</a>.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Veritas Enterprise Vault.cloud SSO.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Veritas Enterprise Vault.cloud SSO, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Veritas Enterprise Vault.cloud SSO**.

    ![Настройка единого входа](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Veritas Enterprise Vault.cloud SSO на панели доступа, вы автоматически войдете в приложение Veritas Enterprise Vault.cloud SSO.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_205.png




<!--HONumber=Nov16_HO5-->


