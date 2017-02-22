---
title: "Учебник. Интеграция Azure Active Directory с Fuse | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Fuse."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ef34f58-863a-4b37-875c-e8efa3e18bb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cb7a8a505122f0b6d2e5744c31a48018c2c05351
ms.openlocfilehash: 10a51c8b272a8e8460079f8beb8a23dc40d3701d


---
# <a name="tutorial-azure-active-directory-integration-with-fuse"></a>Руководство. Интеграция Azure Active Directory с Fuse

В этом руководстве описано, как интегрировать Fuse с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Fuse обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Fuse.
- Вы можете включить автоматический вход пользователей в Fuse (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Fuse, вам потребуется:

- подписка Azure AD;
- подписка на Fuse с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Fuse из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-fuse-from-the-gallery"></a>Добавление Fuse из коллекции
Чтобы настроить интеграцию Fuse с Azure AD, необходимо добавить Fuse из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Fuse из коллекции, выполните следующие действия.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** вверху диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Fuse**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_001.png)

5. На панели результатов выберите **Fuse** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Fuse с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Fuse соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Fuse.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Fuse.

Чтобы настроить и проверить единый вход Azure AD в Fuse, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Fuse](#creating-a-fuse-test-user)** требуется для создания пользователя Britta Simon в Fuse, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Fuse.

**Чтобы настроить единый вход Azure AD в Fuse, выполните следующие действия.**

1. На портале управления Azure на странице интеграции с приложением **Fuse** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_01.png)

3. В разделе **Домены и URL-адреса приложения Fuse** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_02.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant name>.fusion-universal.com/`.

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<tenant name>.fusion-universal.com`

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки Fuse](mailto:support@fusion-universal.com). 

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_03.png)   

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_general_300.png)

6. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_04.png)

7. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_general_400.png)

8. В разделе **Сертификат подписи SAML** щелкните **Certificate (Raw)** (Сертификат (необработанный)) и сохраните файл сертификата на своем компьютере.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_05.png) 

9. В разделе **Конфигурация Fuse** щелкните **Настроить Fuse**, чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_06.png) 

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_07.png)

10. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки Fuse](mailto:support@fusion-universal.com) и предоставьте следующие сведения: 

    • скачанный **файл сертификата**;

    • **URL-адрес службы единого входа SAML**;

    • **идентификатор сущности SAML**;

    • **URL-адрес выхода**.
  

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в левой области навигации нажмите значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fuse-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fuse-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fuse-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия:
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-fuse-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **Britta Simon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-fuse-test-user"></a>Создание тестового пользователя приложения Fuse

В этом разделе описано, как создать пользователя Britta Simon в приложении Fuse. Обратитесь в [службу поддержки Fuse](mailto:support@fusion-universal.com), чтобы добавить пользователей на платформу Fuse.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Fuse.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Fuse, выполните следующие действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Fuse**.

    ![Настройка единого входа](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Fuse на панели доступа, вы автоматически войдете в приложение Fuse.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO4-->


