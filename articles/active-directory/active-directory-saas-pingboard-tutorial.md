---
title: Руководство по интеграции Azure Active Directory с Pingboard | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: 46d00fdcc535b85754eb2f805d2844e1526f1f86
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Руководство по интеграции Azure Active Directory с Pingboard

В этом руководстве описано, как интегрировать приложение Pingboard с Azure Active Directory (Azure AD).

Интеграция Azure AD с Pingboard обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Pingboard.
- Вы можете включить автоматический вход пользователей в Pingboard (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Pingboard, вам потребуется:

- подписка Azure AD;
- подписка Pingboard с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Pingboard из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-pingboard-from-the-gallery"></a>Добавление Pingboard из коллекции
Чтобы настроить интеграцию Pingboard с Azure AD, необходимо добавить Pingboard из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pingboard из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Пункт "Корпоративные приложения"][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Pingboard**, выберите **Pingboard** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Pingboard в списке результатов](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Pingboard с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Pingboard соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pingboard.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pingboard.

Чтобы настроить и проверить единый вход Azure AD в Pingboard, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pingboard](#create-a-pingboard-test-user)** требуется для того, чтобы в Pingboard существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Pingboard.

**Чтобы настроить единый вход Azure AD в Pingboard, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Pingboard** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2.  В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Pingboard** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Pingboard для IdP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. В текстовом поле **Идентификатор** введите значение `http://app.pingboard.com/sp`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<entity-id>.pingboard.com/auth/saml/consume`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если вы хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Сведения о домене и URL-адресах единого входа для приложения Pingboard для поставщика услуг](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<sub-domain>.pingboard.com/sign_in`.

    > [!NOTE] 
    > Обратите внимание на то, что эти значения используются в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Pingboard](https://support.pingboard.com/).

5. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![XML-файл метаданных Pingboard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне Pingboard, откройте новое окно браузера и войдите в учетную запись Pingboard. Для настройки единого входа нужны права администратора Pingboard.

8. В верхнем меню выберите **Apps > Integrations** ("Приложения" > "Интеграция").

    ![Настройка единого входа](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  На странице **Integrations** (Интеграция) найдите элемент **Azure Active Directory** и щелкните его.

    ![Интеграция для единого входа в Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. В появившемся модальном окне щелкните **Configure** (Настройка).

    ![Кнопка настройки в Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. На следующей странице вы увидите сообщение "Azure SSO Integration is enabled" (Интеграция для единого входа Azure включена). Откройте скачанный XML-файл метаданных в Блокноте и вставьте его содержимое в поле **IDP Metadata** (Метаданные IdP).

    ![Экран настройки единого входа в Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. Файл будет проверен, и если он пройдет проверку, то единый вход будет включен.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-pingboard-test-user"></a>Создание тестового пользователя Pingboard

Чтобы пользователи Azure AD могли входить в Pingboard, их необходимо подготовить в Pingboard. Для Pingboard подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Pingboard в качестве администратора.

2. Нажмите кнопку **Add Employee** (Добавить сотрудника) на странице **Directory** (Каталог).

    ![Добавление сотрудника](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. На диалоговой странице **Add Employee** (Добавление сотрудника) выполните следующие действия.

    ![Приглашение пользователей](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например **Britta Simon**.

    Б. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    c. В текстовом поле **Job Title** (Должность) введите должность пользователя Britta Simon.

    d. Из раскрывающегося списка **Location** (Расположение) выберите расположение пользователя Britta Simon.
    
    д. Щелкните **Добавить**.   

4. Откроется окно подтверждения добавления пользователя.
    
    ![Подтверждение](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Pingboard.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pingboard, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pingboard**.

    ![Ссылка на Pingboard в списке "Приложения"](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Щелкнув элемент Pingboard на панели доступа, вы автоматически войдете в приложение Pingboard.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png
