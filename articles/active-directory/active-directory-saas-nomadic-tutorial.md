---
title: "Руководство. Интеграция Azure Active Directory с Nomadic | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Nomadic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13d02b1c-d98a-40b1-824f-afa45a2deb6a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ee8b12207ecb21bccc2670122565ba154aaa9ff8
ms.openlocfilehash: ce07a93977930852b49d4eaad4ad18254425a773


---
# <a name="tutorial-azure-active-directory-integration-with-nomadic"></a>Руководство. Интеграция Azure Active Directory с Nomadic

В этом руководстве описано, как интегрировать Nomadic с Azure Active Directory (Azure AD).

Интеграция Azure AD с Nomadic обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Nomadic.
- Вы можете включить автоматический вход пользователей в Nomadic (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Nomadic, вам потребуются:

- подписка Azure AD;
- подписка Nomadic с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц, используя [эту ссылку](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Nomadic из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-nomadic-from-the-gallery"></a>Добавление Nomadic из коллекции
Чтобы настроить интеграцию Nomadic с Azure AD, необходимо добавить Nomadic из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Nomadic из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Nomadic**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_001.png)

5. На панели результатов выберите **Nomadic** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Nomadic с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход Azure AD, необходимо знать, какой пользователь в Nomadic соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Nomadic.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD как **Имя пользователя** в Nomadic.

Чтобы настроить и проверить единый вход Azure AD в Nomadic, потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Nomadic](#creating-a-nomadic-test-user)** требуется для создания в Nomadic пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Nomadic.

**Чтобы настроить единый вход Azure AD в Nomadic, сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **Nomadic** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. На странице диалогового окна **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_01.png)

3. В разделе **Домены и URL-адреса приложения Nomadic** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_02.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.nomadic.fm/signin`.
    
    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.nomadic.fm/auth/saml2/sp`

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки Nomadic](mailto:help@nomadic.fm).

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_03.png)     

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_general_300.png)

6. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_04.png)

7. Во всплывающем окне **Rollover certificate** (Сертификат возобновления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_general_400.png)

8. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_05.png) 

9. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки Nomadic](mailto:help@nomadic.fm) и предоставьте скачанный **файл метаданных**.
  

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadic-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadic-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadic-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** сделайте следующее:
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nomadic-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-nomadic-test-user"></a>Создание тестового пользователя Nomadic

В этом разделе описано, как создать пользователя Britta Simon в приложении Nomadic. Обратитесь в [службу поддержки Nomadic](mailto:help@nomadic.fm), чтобы добавить пользователей на платформу Nomadic.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Nomadic.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Nomadic, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Nomadic**.

    ![Настройка единого входа](./media/active-directory-saas-nomadic-tutorial/tutorial_nomadic_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. Нажмите кнопку **Выбрать** в диалоговом окне **Пользователи и группы**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Nomadic на панели доступа, вы автоматически войдете в приложение Nomadic.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-nomadic-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


