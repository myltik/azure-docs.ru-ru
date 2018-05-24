---
title: Руководство по интеграции Azure Active Directory с Box | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-box"></a>Интеграция службы Azure Active Directory с приложением Box

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Box.

Интеграция Azure AD с приложением Box обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Box.
- Вы можете включить для пользователей автоматический вход в Box (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Чтобы узнать об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Box, вам потребуется:

- подписка Azure AD;
- подписка Box с поддержкой единого входа.

> [!NOTE]
> Мы *не рекомендуем* использовать рабочую среду для тестирования действий, описанных в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление Box из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="add-box-from-the-gallery"></a>Добавление Box из коллекции
Чтобы настроить интеграцию Azure AD с Box, добавьте Box из коллекции в список управляемых приложений SaaS, выполнив следующие действия.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Окно "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Box** и выберите **Box** в списке результатов, затем щелкните **Добавить**.

    ![Box в списке результатов](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Box с помощью тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо определить, какой пользователь Box соответствует пользователю в Azure Active Directory. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Box.

Для этого назначьте *имя пользователя* Azure AD в качестве значения *имени пользователя* в Box.

Чтобы настроить и проверить единый вход Azure AD в Box, выполните действия в следующих пяти разделах.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure и настроить его в приложении Box.

1. На портале Azure в окне интеграции с приложением **Box** щелкните **Единый вход**.

    ![Ссылка "Единый вход"][4]

2. В окне **Единый вход** в поле **Режим единого входа** выберите **Вход на основе SAML**.
 
    ![Окно "Единый вход"](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Box** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Box](./media/active-directory-saas-box-tutorial/url3.png)

    a. В поле **URL-адрес входа** введите URL-адрес в следующем формате: *https://\<поддомен>.box.com*.

    Б. В текстовом поле **Идентификатор** введите адрес **box.net**.
     
    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Замените их фактическими значениями URL-адреса входа и идентификатора. Чтобы получить эти значения, обратитесь к [службе поддержки Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Чтобы настроить единый вход для приложения, выполните процедуру из раздела [о самостоятельной настройке единого входа](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Если вам не удастся включить параметры единого входа для учетной записи Box, попробуйте обратиться за помощью в [службу поддержки Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire), предоставив им скачанный XML-файл.

> [!TIP]
> Настроив приложение, вы можете прочитать краткую версию предыдущих инструкций на [портале Azure](https://portal.azure.com). Добавив приложение из раздела **Active Directory** > **Корпоративные приложения**, выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** внизу. На [этой странице]( https://go.microsoft.com/fwlink/?linkid=845985) вы можете получить дополнительную информацию о встроенной документации Azure AD.
>

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon, выполнив следующие действия.

![Создание тестового пользователя Azure AD][100]

1. На портале Azure в области слева щелкните **Azure Active Directory**.

    ![Ссылка на Azure Active Directory](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Чтобы отобразить список активных пользователей, выберите **Пользователи и группы** > **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. В верхней части окна **Все пользователи** выберите **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    Откроется окно **Пользователь**.

4. В окне **Пользователь** сделайте следующее.

    ![Окно пользователя](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-box-test-user"></a>Создание тестового пользователя Box

В этом разделе вы создадите тестового пользователя Britta Simon в Box. Приложение Box поддерживает JIT-подготовку. Эта функция включена по умолчанию. Если такой пользователь еще не существует, он создается при попытке доступа к приложению Box. Дополнительные действия по созданию пользователя не требуются.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Box. Для этого выполните следующее.

![Назначение роли пользователя][200]

1. На портале Azure откройте представление **Приложения**, перейдите к представлению **Каталог** и выберите **Корпоративные приложения** > **Все приложения**.

    ![Ссылки "Корпоративные приложения" и "Все приложения"][201] 

2. В **списке приложений** выберите **Box**.

    ![Ссылка на Box](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. Нажмите кнопку **Select** (Выбрать).

7. В окне **Добавление назначения** выберите **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Выбрав плитку **Box**  на панели доступа, вы откроете страницу входа в приложение Box.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список руководств, посвященных интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Настройка подготовки пользователей](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

