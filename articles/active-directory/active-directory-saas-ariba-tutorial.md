---
title: Руководство по интеграции Azure Active Directory с Ariba | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Ariba.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 45a8364c-55d1-4dc7-b079-9eb2a701842d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: aedb8f761a16cc438a1ad6473728e29b270c3919
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346519"
---
# <a name="tutorial-azure-active-directory-integration-with-ariba"></a>Руководство. Интеграция Azure Active Directory с Ariba

В этом руководстве описано, как интегрировать Ariba с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Ariba обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Ariba.
- Вы можете включить автоматический вход пользователей в Ariba (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Ariba, вам потребуется:

- подписка Azure AD;
- подписка Ariba с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Ariba из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ariba-from-the-gallery"></a>Добавление Ariba из коллекции
Чтобы настроить интеграцию Ariba с Azure AD, необходимо добавить Ariba из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ariba из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Ariba**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_search.png)

5. На панели результатов выберите **Ariba** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Ariba с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Ariba соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Ariba.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Ariba.

Чтобы настроить и проверить единый вход Azure AD в Ariba, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Ariba](#creating-an-ariba-test-user)** нужно для того, чтобы в Ariba также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Ariba.

**Чтобы настроить единый вход Azure AD в Ariba, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Ariba** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Ariba** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.sourcing.ariba.com` или `https://<subdomain>.supplier.ariba.com`.

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `http://<subdomain>.procurement-2.ariba.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь к группе поддержки Ariba по номеру **1-866-218-2155**. 
 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-ariba-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход для приложения, позвоните в группу поддержки Ariba по номеру **1-866-218-2155**. Вы получите дальнейшие указания по тому, как предоставить скачанный файл **сертификата (Base64)**.  
 
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-ariba-test-user"></a>Создание тестового пользователя Ariba

Цель этого раздела — создать пользователя с именем Britta Simon в приложении Ariba. Обратитесь к группе поддержки Ariba по номеру **1-866-218-2155**, чтобы добавить пользователей в систему Ariba. 

 >[!NOTE]
 >Чтобы создать пользователя вручную, необходимо обратиться к группе поддержки Ariba по номеру **1-866-218-2155**.
 >  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Ariba.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Ariba, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Ariba**.

    ![Настройка единого входа](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Ariba на панели доступа, вы автоматически войдете в приложение Ariba. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_203.png

