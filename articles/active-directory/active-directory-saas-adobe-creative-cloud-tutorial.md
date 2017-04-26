---
title: "Руководство по интеграции Azure Active Directory с Adobe Creative Cloud | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Руководство по интеграции Azure Active Directory с Adobe Creative Cloud

В этом учебнике описано, как интегрировать приложение Adobe Creative Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Adobe Creative Cloud обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Adobe Creative Cloud.
- Вы можете включить автоматический вход пользователей в Adobe Creative Cloud (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Adobe Creative Cloud, вам потребуется:

- подписка Azure AD;
- подписка Adobe Creative Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Adobe Creative Cloud из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Добавление Adobe Creative Cloud из коллекции
Чтобы настроить интеграцию Adobe Creative Cloud с Azure AD, необходимо добавить Adobe Creative Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Adobe Creative Cloud из коллекции, выполните следующие действия:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Adobe Creative Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. На панели результатов выберите **Adobe Creative Cloud** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Adobe Creative Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Adobe Creative Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Adobe Creative Cloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Adobe Creative Cloud.

Чтобы настроить и проверить единый вход Azure AD в Adobe Creative Cloud, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Adobe Creative Cloud](#creating-an-adobe-creative-cloud-test-user)** требуется для создания в Adobe Creative Cloud пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении Adobe Creative Cloud.

**Чтобы настроить единый вход Azure AD в Adobe Creative Cloud, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **Adobe Creative Cloud** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Adobe Creative Cloud** выполните следующее.

    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    а. В текстовом поле **Идентификатор** введите значение `https://www.okta.com/saml2/service-provider/<token>`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.okta.com/auth/saml20/accauthlinktest`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо указать фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы создать пользователя вручную, необходимо обратиться к группе поддержки Adobe Creative Cloud.

4. Если вы хотите настроить приложение в **режиме, инициированном провайдером службы**, то в разделе **Домены и URL-адреса приложения Adobe Creative Cloud** выполните следующее.

    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    а. Щелкните параметр **Показать дополнительные параметры URL-адресов**.

    b. В текстовом поле **URL-адрес для входа** введите значение `https://adobe.com`.

5. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. В разделе **Конфигурация Adobe Creative Cloud** щелкните **Настройка Adobe Creative Cloud**, чтобы открыть окно **настройки входа**. Скопируйте **Идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** из раздела "Краткий справочник".

    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. В другом окне веб-браузера войдите в свой клиент Adobe Creative Cloud с правами администратора.

8.  Выберите пункты **Identity** (Идентификация) на панели навигации слева и щелкните свой домен. В разделе **Single Sign On Configuration Required** (Обязательная конфигурация единого входа) выполните следующие действия.

    ![Параметры](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Параметры")

9. Щелкните **Browse** (Просмотр), чтобы найти и передать сертификат, загруженный из Azure AD, в качестве **сертификата поставщика удостоверений**.

10. В текстовое поле **IDP issuer** (Издатель удостоверений) поместите значение **Идентификатор сущности SAML**, который вы скопировали из раздела **Настройка входа** на портале Azure.

11. В текстовое поле **IDP Login URL** (URL-адрес входа поставщика удостоверений) поместите значение **URL-адрес службы единого входа SAML**, который вы скопировали из раздела **Настройка входа** на портале Azure.

12. Для параметра **HTTP — Redirect** (Перенаправление HTTP) выберите вариант **IDP Binding** (Привязка к поставщику удостоверений).

13. Для параметра **Email Address** (Адрес электронной почты) выберите вариант **User Login Setting** (Настройки входа пользователя).
 
14. Нажмите кнопку **Сохранить** .

15. Теперь на панели мониторинга отобразится XML-файл **Download Metadata** (Загрузить метаданные). Он содержит URL-адреса компании Adobe для описания сущности (EntityDescriptor) и URL-адреса службы утверждений (AssertionConsumerService). Откройте этот файл и перенесите настройки в приложение Azure AD.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    а. Используйте значение EntityDescriptor, предоставленное компанией Adobe, в качестве значения для параметра **Identifier** (Идентификатор) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).

    b. Используйте значение AssertionConsumerService, предоставленное компанией Adobe, в качестве значения для параметра **Reply URL** (URL-адрес ответа) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).
 
### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>Создание тестового пользователя Adobe Creative Cloud

Чтобы пользователи Azure AD могли входить в Adobe Creative Cloud, они должны быть подготовлены для Adobe Creative Cloud.  
В случае Adobe Creative Cloud подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Выполните вход на корпоративный сайт Adobe Creative Cloud с правами администратора.

2. Выберите параметр **Пользователи**.

    ![Люди](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "Люди")

3. Нажмите кнопку **Пригласить пользователя**.

    ![Приглашение пользователей](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "приглашение пользователей")

4. На странице диалогового окна **Приглашение пользователей** выполните следующие действия:

    ![Приглашение участников](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "приглашение участников")

    а. В текстовом поле **Электронная почта** введите адрес электронной почты учетной записи Britta Simon.
    
    b. Нажмите кнопку **Пригласить**.

    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Adobe Creative Cloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Adobe Creative Cloud, выполните следующие действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Adobe Creative Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Adobe Creative Cloud на панели доступа, вы автоматически войдете в приложение Adobe Creative Cloud.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
