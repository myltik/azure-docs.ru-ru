---
title: Руководство по интеграции Azure Active Directory с Bonusly | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8133ec95bf0cbf6a9ce4ca1ad224d03ac593c940
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Руководство. Интеграция Azure Active Directory с Bonusly

В этом руководстве описано, как интегрировать Bonusly с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Bonusly обеспечивает следующие преимущества:

- c помощью Azure AD вы можете контролировать доступ к приложению Bonusly;
- вы можете включить автоматический вход пользователей в Bonusly (единый вход) с учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Bonusly, вам потребуется:

- подписка Azure AD;
- подписка Bonusly с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Bonusly из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-bonusly-from-the-gallery"></a>Добавление Bonusly из коллекции
Чтобы настроить интеграцию Bonusly с Azure AD, необходимо добавить Bonusly из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Bonusly из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Bonusly**, выберите **Bonusly** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Bonusly в списке результатов](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Bonusly с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Bonusly соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Bonusly.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Bonusly.

Чтобы настроить и проверить единый вход Azure AD в Bonusly, выполните следующие стандартные действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Bonusly](#create-a-bonusly-test-user)** требуется для того, чтобы в Bonusly существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Bonusly.

**Чтобы настроить единый вход Azure AD в Bonusly, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Bonusly** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Bonusly** выполните следующие действия:

    ![Сведения о домене и URL-адресах для единого входа в приложение Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://Bonus.ly/saml/<tenant-name>`.

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь в [службу поддержки Bonusly](https://Bonusly/contact).
 
4. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток** сертификата.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Bonusly** щелкните **Настроить Bonusly**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Конфигурация Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. В другом окне браузера войдите в клиент **Bonusly**.

8. На панели инструментов в верхней части экрана щелкните **Settings** (Параметры) и выберите **Integrations and apps** (Интеграции и приложения).
   
    ![Социальный раздел Bonusly](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. В разделе **Single Sign-On** (Единый вход) выберите **SAML**.

10. На диалоговой странице **SAML** выполните следующие действия.
   
    ![Диалоговое окно SAML в Bonusly](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. В текстовое поле **IdP SSO target URL** (Целевой URL-адрес единого входа IdP) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    Б. В текстовое поле **IdP Issuer** (Издатель IdP) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure. 

    c. В текстовое поле **IdP Login URL** (URL-адрес входа IdP) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    d. Вставьте значение **Отпечаток** с портала Azure в текстовое поле **Cert Fingerprint** (Отпечаток сертификата).
   
11. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-bonusly-test-user"></a>Создание тестового пользователя приложения Bonusly

Чтобы пользователи Azure AD могли входить в приложение Bonusly, они должны быть подготовлены в этом приложении. В случае с Bonusly подготовка выполняется вручную.

>[!NOTE]
>Для подготовки учетных записей пользователей AAD можно использовать любые другие средства создания учетных записей пользователей Bonusly или API, предоставляемые разработчиками Bonusly.
>  

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. В окне браузера войдите в клиент Bonusly.

2. Щелкните **Параметры**.
 
    ![Параметры](./media/active-directory-saas-bonus-tutorial/ic781041.png "Параметры")

3. Откройте вкладку **Users and bonuses** (Пользователи и бонусы).
   
    ![Пользователи и бонусы](./media/active-directory-saas-bonus-tutorial/ic781042.png "Пользователи и бонусы")

4. Выберите **Manage Users**(Управление пользователями).
   
    ![Управление пользователями](./media/active-directory-saas-bonus-tutorial/ic781043.png "Управление пользователями")

5. Нажмите кнопку **Add User**(Добавить пользователя).
   
    ![Добавление пользователя](./media/active-directory-saas-bonus-tutorial/ic781044.png "Добавление пользователя")

6. На странице **Добавление пользователя** выполните следующие действия.
   
    ![Добавление пользователя](./media/active-directory-saas-bonus-tutorial/ic781045.png "Добавление пользователя")  

    a. В текстовое поле **First name** (Имя) введите имя пользователя, например **Britta**.

    Б. В текстовое поле **Last name** (Фамилия) введите фамилию пользователя, например **Simon**.
 
    c. В текстовое поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    d. Выберите команду **Сохранить**.
   
     >[!NOTE]
     >Владелец учетной записи Azure AD получит сообщение электронной почты со ссылкой для подтверждения учетной записи перед ее активацией.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Bonusly.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Bonusly, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Bonusly**.

    ![Ссылка на Bonusly в списке "Приложения"](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Bonusly на панели доступа, вы автоматически войдете в приложение Bonusly.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png

