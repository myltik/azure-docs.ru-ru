---
title: Руководство по интеграции Azure Active Directory с O.C. Tanner — AppreciateHub | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и O.C. Tanner — AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 57cdd0d8a58fd88ab1dda63d32397e726c7a2eb2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350259"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Руководство по интеграции Azure Active Directory с O.C. Tanner — AppreciateHub

В этом руководстве вы узнаете, как интегрировать O.C. Tanner — AppreciateHub с Azure Active Directory (Azure AD).

Интеграция O.C. Tanner — AppreciateHub с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к O.C. Tanner — AppreciateHub
- Вы можете включить автоматический вход пользователей в O.C. Tanner — AppreciateHub (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с O.C. Tanner — AppreciateHub, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа O.C. подписка Tanner — AppreciateHub с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление O.C. Tanner — AppreciateHub из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Добавление O.C. Tanner — AppreciateHub из коллекции
Чтобы настроить интеграцию O.C. Tanner — AppreciateHub с Azure AD, необходимо добавить O.C. Tanner — AppreciateHub из коллекции в список управляемых приложений SaaS.

**Чтобы добавить O.C. Tanner — AppreciateHub из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **O.C. Tanner — AppreciateHub**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. На панели результатов выберите **O.C. Tanner — AppreciateHub** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение O.C. Tanner — AppreciateHub с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в O.C. Tanner — AppreciateHub соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в O.C. Необходимо установить связь Tanner — AppreciateHub.

В O.C. Tanner —AppreciateHub назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя**, чтобы установить эту связь.

Чтобы настроить и проверить единый вход Azure AD в O.C. Tanner — AppreciateHub, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя O.C. Tanner — AppreciateHub](#creating-a-oc-tanner---appreciatehub-test-user)** требуется для создания пользователя Britta Simon в O.C. Tanner — AppreciateHub, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в O.C. Tanner — AppreciateHub.

**Чтобы настроить единый вход Azure AD в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **O.C. Tanner — AppreciateHub** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. В разделе **Домены и URL-адреса приложения O.C. Tanner — AppreciateHub** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.octanner.net/sp/ACS.saml2`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него нужно указать фактический URL-адрес ответа. Обратитесь в [службу поддержки O.C. Tanner — AppreciateHub](mailto:sso@octanner.com), чтобы получить это значение.

    Б. Откройте файл метаданных, используя следующую ссылку: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).
   
    c. Найдите узел **md:AssertionConsumerService** . 
   
    d. Скопируйте значение атрибута **Location**. 
   
    ![Настройка параметров приложения][12]
   
    д. Вставьте значение, полученное на предыдущем шаге, в текстовом поле **URL-адрес для входа**.

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход на стороне приложения **O.C. Tanner — AppreciateHub**, необходимо отправить скачанный **XML-файл метаданных** [ службе поддержки О.С. Tanner — AppreciateHub](mailto:sso@octanner.com).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Создание тестового пользователя O.C. Tanner — AppreciateHub

Цель этого раздела — создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub.

**Чтобы создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

Попросите [службу поддержки O.C. Tanner — AppreciateHub](mailto:sso@octanner.com) создать пользователя, у которого значение атрибута nameID совпадает с именем пользователя Britta Simon в Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к O.C. Tanner — AppreciateHub.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **O.C. Tanner — AppreciateHub**.

    ![Настройка единого входа](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку O.C. Tanner — AppreciateHub на панели доступа, вы автоматически войдете в приложение O.C. Tanner — AppreciateHub.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png

