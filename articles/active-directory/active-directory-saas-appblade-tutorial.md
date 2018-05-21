---
title: Руководство. Интеграция Azure Active Directory с AppBlade | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении AppBlade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 348246dba007c0b38942e7fa44e875f53ec46b6e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Руководство. Интеграция Azure Active Directory с AppBlade

В этом руководстве описано, как интегрировать AppBlade с Azure Active Directory (Azure AD).

Интеграция AppBlade с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению AppBlade.
- Вы можете включить автоматический вход пользователей в AppBlade (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с AppBlade, вам потребуется:

- подписка Azure AD;
- подписка AppBlade с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление AppBlade из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-appblade-from-the-gallery"></a>Добавление AppBlade из коллекции
Чтобы настроить интеграцию AppBlade с Azure AD, необходимо добавить AppBlade из коллекции в список управляемых приложений SaaS.

**Чтобы добавить AppBlade из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **AppBlade**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_search.png)

5. На панели результатов выберите **AppBlade** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение AppBlade с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в AppBlade соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AppBlade.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в AppBlade.

Чтобы настроить и проверить единый вход Azure AD в AppBlade, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя AppBlade](#creating-an-appblade-test-user)** нужно для того, чтобы в AppBlade также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении AppBlade.

**Чтобы настроить единый вход Azure AD в AppBlade, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **AppBlade** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_samlbase.png)

3. В разделе **Домены и URL-адреса приложения AppBlade** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки AppBlade](mailto:support@appblade.com). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход на стороне **AppBlade**, отправьте [группе поддержки AppBlade](mailto:support@appblade.com) скачанный **XML-файл метаданных**. Кроме того, попросите их настроить для параметра **SSO Issuer URL** (URL-адрес единого входа издателя) значение `https://appblade.com/saml`. Без этого параметра единый вход работать не будет.


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
 
### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-appblade-test-user"></a>Создание тестового пользователя AppBlade

Цель этого раздела — создать в приложении AppBlade пользователя с именем Britta Simon. Приложение AppBlade поддерживает JIT-подготовку. Эта функция включена по умолчанию. **Убедитесь, что ваше доменное имя настроено в AppBlade для подготовки пользователей. После этого будет работать только JIT-подготовка пользователей.**

Если у пользователя есть адрес электронной почты в домене, настроенном приложением AppBlade для вашей учетной записи, такой пользователь автоматически станет участником этой учетной записи и получит указанный вами уровень разрешений: "Базовый" (базовый пользователь, который может только устанавливать приложения), "Участник команды" (пользователь, который может скачивать новые версии приложений и управлять проектами) или "Администратор" (все права администратора учетной записи). Обычно выбирается базовый уровень, для повышения которого пользователю предоставляется имя входа администратора (в AppBlade имя входа администратора можно настроить заранее на основе адреса электронной почты или повысить уровень пользователя от имени клиента после того, как он выполнит вход).

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к AppBlade (если он еще не создан). 

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться к [группе поддержки AppBlade](mailto:support@appblade.com).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к AppBlade.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в AppBlade, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **AppBlade**.

    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент AppBlade на панели доступа, вы автоматически войдете в приложение AppBlade. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_203.png

