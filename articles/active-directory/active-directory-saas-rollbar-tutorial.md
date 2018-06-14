---
title: Руководство по интеграции Azure Active Directory с Rollbar | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: 277dc8e378e5e0c5f649da4f129d3daca96ef8be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350888"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Руководство по интеграции Azure Active Directory с Rollbar

В этом руководстве описано, как интегрировать Rollbar с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Rollbar обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Rollbar.
- Вы можете включить автоматический вход пользователей в Rollbar (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Rollbar, вам потребуется:

- подписка Azure AD;
- подписка Rollbar с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Rollbar из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-rollbar-from-the-gallery"></a>Добавление Rollbar из коллекции
Чтобы настроить интеграцию Rollbar с Azure AD, необходимо добавить Rollbar из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Rollbar из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Rollbar**, выберите **Rollbar** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Rollbar в списке результатов](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Rollbar с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Rollbar соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Rollbar.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Rollbar.

Чтобы настроить и проверить единый вход Azure AD в Rollbar, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Rollbar](#create-a-rollbar-test-user)** требуется для того, чтобы в Rollbar существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Rollbar.

**Чтобы настроить единый вход Azure AD в Rollbar, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Rollbar** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Rollbar** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://saml.rollbar.com`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://rollbar.com/<accountname>/saml/sso/azure/`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Rollbar](mailto:support@rollbar.com). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. В другом окне веб-браузера войдите на свой корпоративный сайт Rollbar в качестве администратора.

8. Щелкните **Profile Settings** (Параметры профиля) в правом верхнем углу и выберите **Account Name settings** (Параметры имени учетной записи).
    
    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Щелкните **Identity Provider** (Поставщик удостоверений) в разделе "SECURITY" (Безопасность).

    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. В разделе **SAML Identity Provider** (Поставщик удостоверений SAML) выполните следующие действия.
    
    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Выберите **AZURE** из раскрывающегося списка **SAML Identity Provider** (Поставщик удостоверений SAML).

    Б. Откройте файл метаданных в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте в текстовое поле **SAML Metadata** (Метаданные SAML).

    c. Выберите команду **Сохранить**.

11. После нажатия кнопки сохранения экран будет выглядеть так:
    
    ![Параметр Configuration](./media/active-directory-saas-rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Чтобы выполнить следующее действие, необходимо добавить себя в качестве пользователя в приложение Rollbar в Azure.
    a. Если нужно, чтобы все пользователи проходили аутентификацию с помощью Azure, щелкните **log in via your identity provider** (вход с использованием поставщика удостоверений), чтобы повторить аутентификацию через Azure.  

    Б.  После возвращения на экран установите флажок **Require login via SAML Identity Provider** (Требовать вход с использованием поставщика удостоверений SAML).

    Б. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-rollbar-test-user"></a>Создание тестового пользователя Rollbar

Чтобы пользователи Azure AD могли выполнять вход в Rollbar, они должны быть подготовлены в Rollbar. В случае с Rollbar подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт Rollbar вашей компании в качестве администратора.

2. Щелкните **Profile Settings** (Параметры профиля) в правом верхнем углу и выберите **Account Name settings** (Параметры имени учетной записи).

    ![Пользователь](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Выберите раздел **Пользователи**.
    
    ![Добавление сотрудника](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Щелкните **Invite Team Members** (Пригласить участников команды).

    ![Приглашение пользователей](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. В текстовом поле введите имя пользователя, например **brittasimon@contoso.com**, и щелкните **Add/Invite** (Добавить или пригласить).

    ![Приглашение пользователей](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. Пользователь получает приглашение, после принятия которого в системе создается соответствующая учетная запись.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Rollbar.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Rollbar, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Rollbar**.

    ![Ссылка на Rollbar в списке "Приложения"](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Rollbar" на панели доступа, вы автоматически войдете в приложение Rollbar.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

