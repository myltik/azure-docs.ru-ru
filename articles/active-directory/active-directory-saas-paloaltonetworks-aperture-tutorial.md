---
title: Руководство по интеграции Azure Active Directory с Palo Alto Networks - Aperture | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks - Aperture.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 29b37b34271680cdd4d1f660ea6eb6172df8b06d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347199"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Руководство по интеграции Azure Active Directory с Palo Alto Networks - Aperture

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Palo Alto Networks - Aperture.

Интеграция Azure AD с Palo Alto Networks - Aperture обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks - Aperture.
- Вы можете включить автоматический вход пользователей в Palo Alto Networks - Aperture (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Palo Alto Networks - Aperture, вам потребуется:

- подписка Azure AD;
- подписка Palo Alto Networks - Aperture с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Palo Alto Networks - Aperture из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Добавление Palo Alto Networks - Aperture из коллекции
Чтобы настроить интеграцию Palo Alto Networks - Aperture с Azure AD, необходимо добавить Palo Alto Networks - Aperture из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Palo Alto Networks - Aperture из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Palo Alto Networks - Aperture** и выберите **Palo Alto Networks - Aperture** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Palo Alto Networks - Aperture в списке результатов](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks - Aperture с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Palo Alto Networks - Aperture соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Palo Alto Networks - Aperture.

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks - Aperture, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Palo Alto Networks - Aperture](#create-a-palo-alto-networks---aperture-test-user)** требуется для того, чтобы в Palo Alto Networks - Aperture существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Palo Alto Networks - Aperture.

**Чтобы настроить единый вход Azure AD в Palo Alto Networks - Aperture, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Palo Alto Networks - Aperture** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Palo Alto Networks - Aperture** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks - Aperture](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks - Aperture](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support). 

5. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. В разделе **Конфигурация Palo Alto Networks - Aperture** щелкните **Настройка Palo Alto Networks - Aperture**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Ссылка "Настроить"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. В другом окне веб-браузера войдите на веб-сайт Palo Alto Networks - Aperture в качестве администратора.

9. В верхней строке меню щелкните **SETTINGS** (Параметры).

    ![Вкладка "SETTINGS" (Параметры)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Перейдите в раздел **APPLICATION** (Приложение) и щелкните форму **Authentication** (Аутентификация) в левой части меню.

    ![Вкладка "Authentication" (Аутентификация)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. На странице **Authentication** (Аутентификация) выполните следующие действия.
    
    ![Вкладка "Authentication" (Аутентификация)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Установите флажок **Enable Single Sign-On (Supported SSP Providers are Okta, Onelogin)** (Включить единый вход (поддерживаемые поставщики SSP Providers: Okta, Onelogin)) в поле **Single Sign-On** (Единый вход).

    Б. В текстовое поле **Identity Provider ID** (Идентификатор поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    c. Щелкните **Choose File** (Выбор файла), чтобы передать сертификат, скачанный из Azure AD, и указать его в поле **Identity Provider Certificate** (Сертификат поставщика удостоверений).

    d. В текстовое поле **Identity Provider SSO URL** (URL-адрес единого входа поставщика удостоверений) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    д. Просмотрите сведения о поставщике удостоверений в разделе **Aperture Info** (Сведения об Aperture) и скачайте сертификат из поля **Aperture Key** (Ключ Aperture).

    f. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Создание тестового пользователя в Palo Alto Networks - Aperture

В этом разделе описано, как создать пользователя Britta Simon в приложении Palo Alto Networks - Aperture. Обратитесь к [группе поддержки клиентов Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) для добавления пользователей на платформу Palo Alto Networks - Aperture. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks - Aperture, чтобы этот пользователь мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Palo Alto Networks - Aperture, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Palo Alto Networks - Aperture**.

    ![Ссылка на Palo Alto Networks - Aperture в списке "Приложения"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Palo Alto Networks - Aperture" на панели доступа, вы автоматически войдете в приложение Palo Alto Networks - Aperture.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_203.png

