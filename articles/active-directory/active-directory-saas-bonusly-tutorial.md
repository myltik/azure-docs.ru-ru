---
title: "Руководство по интеграции Azure Active Directory с Bonusly | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Руководство. Интеграция Azure Active Directory с Bonusly

В этом руководстве описано, как интегрировать Bonusly с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Bonusly обеспечивает следующие преимущества:

- c помощью Azure AD вы можете контролировать доступ к приложению Bonusly;
- вы можете включить автоматический вход пользователей в Bonusly (единый вход) с учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Bonusly, вам потребуется:

- подписка Azure AD;
- подписка Bonusly с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Bonusly из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-bonusly-from-the-gallery"></a>Добавление Bonusly из коллекции
Чтобы настроить интеграцию Bonusly с Azure AD, необходимо добавить Bonusly из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Bonusly из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Bonusly**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. На панели результатов выберите **Bonusly** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Bonusly с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Bonusly соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Bonusly.

Для этого следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Bonusly.

Чтобы настроить и проверить единый вход Azure AD в Bonusly, выполните следующие стандартные действия.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Bonusly](#creating-a-bonusly-test-user)** требуется для создания в Bonusly пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Bonusly.

**Чтобы настроить единый вход Azure AD в Bonusly, сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **Bonusly** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Bonusly** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    а. В текстовом поле **Идентификатор** введите значение `bonusly`.
    
    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://bonus.ly/saml/<APP-ID>/consume`.
    
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то в разделе **Домены и URL-адреса приложения Bonusly** сделайте следующее:
    
    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    а. Щелкните параметр **Показать дополнительные параметры URL-адресов**.

    b. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://bonus.ly/saml/<your_subdomain>/consume`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки Bonusly](mailto:sales@easyterritory.com).

5. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. В разделе **Настройка Bonusly** щелкните **Настроить Bonusly**, чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Bonusly в качестве администратора.

12. На панели инструментов в верхней части экрана щелкните **Settings** (Параметры) и выберите **Integrations and apps** (Интеграции и приложения).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. В разделе **Single Sign-On** (Единый вход) выберите **SAML**.

14. На диалоговой странице **SAML** выполните следующие действия.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    а. В текстовом поле **IdP SSO target URL** (Целевой URL-адрес единого входа для IdP) введите значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из окна настройки приложения Azure AD.

    b. В текстовом поле **IdP Login URL** (URL-адрес входа IdP) введите значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из окна настройки приложения Azure AD.

    c. В текстовом поле **IdP Issuer** (Издатель IdP) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML) из окна настройки приложения Azure AD.

    d. Скопируйте значение поля "Отпечаток" из скачанного сертификата и вставьте его в текстовое поле **Cert Fingerprint** (Отпечаток сертификата).

    д. Щелкните **Сохранить**.

    > [!NOTE] 
    > Дополнительные сведения см. в статье [Практическое руководство. Извлечение отпечатка сертификата](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be).



### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-bonusly-test-user"></a>Создание тестового пользователя приложения Bonusly

Чтобы пользователи Azure AD могли выполнять вход в Bonusly, они должны быть подготовлены для Bonusly.
В случае с Bonusly подготовка выполняется вручную.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1. Войдите на корпоративный сайт Bonusly в качестве администратора.

2. Щелкните **Параметры**.

    ![Новый пользователь](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "Новый пользователь")

3. Откройте вкладку **Users and bonuses** (Пользователи и бонусы).

    ![Новый пользователь](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "Новый пользователь")

4. Выберите **Manage Users**(Управление пользователями).

    ![Новый пользователь](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "Новый пользователь")

5. Нажмите кнопку **Add User**(Добавить пользователя).

    ![Новый пользователь](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "Новый пользователь")

6. В разделе **Add User** (Добавление пользователя) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "Новый пользователь")

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Электронная почта** введите адрес электронной почты учетной записи Britta Simon.

    d. Щелкните **Сохранить**.

    > [!NOTE] 
    > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие средства создания учетной записи пользователя Bonus.ly или API, предоставляемые Bonus.ly для подготовки учетных записей пользователя AAD.



### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Bonusly.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Bonusly, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Bonusly**.

    ![Настройка единого входа](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Bonusly на панели доступа, вы автоматически войдете в приложение Bonusly.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png
