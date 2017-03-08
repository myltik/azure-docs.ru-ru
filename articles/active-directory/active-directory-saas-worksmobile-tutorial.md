---
title: "Руководство. Интеграция Azure Active Directory с WORKS MOBILE | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в WORKS MOBILE."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 215d1bf1f0f02e08f4d7bd65354d51bae945716e
ms.openlocfilehash: 4bd654821dfde2a59d9c9fd85166c7e9681791ab


---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Руководство. Интеграция Azure Active Directory с WORKS MOBILE

В этом руководстве описано, как интегрировать WORKS MOBILE с Azure Active Directory (Azure AD).

Интеграция WORKS MOBILE с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к WORKS MOBILE.
- Вы можете настроить автоматический вход пользователей в WORKS MOBILE (единый вход) под учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с WORKS MOBILE, вам потребуются:

- подписка Azure AD;
- подписка WORKS MOBILE с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц, используя [эту ссылку](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление WORKS MOBILE из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-works-mobile-from-the-gallery"></a>Добавление WORKS MOBILE из коллекции
Чтобы настроить интеграцию WORKS MOBILE с Azure AD, необходимо добавить WORKS MOBILE из коллекции в список управляемых приложений SaaS.

**Чтобы добавить WORKS MOBILE из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **WORKS MOBILE**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_001.png)

5. На панели результатов выберите **WORKS MOBILE** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описывается настройка и проверка единого входа Azure AD в WORKS MOBILE с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход, Azure AD необходимо знать, какой пользователь в WORKS MOBILE соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в WORKS MOBILE.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения параметра **Имя пользователя** в WORKS MOBILE.

Чтобы настроить и проверить единый вход Azure AD в WORKS MOBILE, потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя WORKS MOBILE](#creating-a-works-mobile-test-user)** требуется для создания в WORKS MOBILE пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описывается, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении WORKS MOBILE.

**Чтобы настроить единый вход Azure AD в WORKS MOBILE, сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **WORKS MOBILE** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_01.png)

3. В разделе **Домены и URL-адреса приложения WORKS MOBILE** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_02.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your-subdomain>.worksmobile.com/jp/myservice`.

    b. В текстовом поле **Идентификатор** введите значение `worksmobile.com`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Мы рекомендуем использовать уникальное значение строки идентификатора. Обратитесь в [службу поддержки WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com), чтобы получить эти значения. 

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_03.png)     

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_300.png)

6. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_04.png)

7. Во всплывающем окне **Rollover certificate** (Сертификат возобновления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)

8. В разделе **Сертификат подписи SAML** щелкните **Certificate (Raw)** (Сертификат (необработанный)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_05.png) 

9. В разделе **Настройка WORKS MOBILE** щелкните **Настроить WORKS MOBILE**, чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_06.png) 

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_07.png)

10. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) и предоставьте следующие сведения: 

    • скачанный **файл сертификата**;

    • **URL-адрес службы единого входа SAML**;

    • **идентификатор сущности SAML**;

    • **URL-адрес выхода**.
  

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-works-mobile-test-user"></a>Создание тестового пользователя WORKS MOBILE

В этом разделе описано, как создать пользователя Britta Simon в приложении WORKS MOBILE. Обратитесь в [службу поддержки WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com), чтобы добавить пользователей на платформу WORKS MOBILE.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к WORKS MOBILE.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в WORKS MOBILE, выполните следующие действия**.

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **WORKS MOBILE**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент WORKS MOBILE на панели доступа, вы автоматически войдете в приложение WORKS MOBILE.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO5-->


