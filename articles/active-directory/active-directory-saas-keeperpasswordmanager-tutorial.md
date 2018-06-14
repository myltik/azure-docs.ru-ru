---
title: Руководство по интеграции Azure Active Directory с Keeper Password Manager & Digital Vault | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Keeper Password Manager & Digital Vault.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 2c76edea4498e32363a3a4952056e69c012bd468
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34339025"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Руководство по интеграции Azure Active Directory с Keeper Password Manager & Digital Vault

В этом учебнике описано, как интегрировать Keeper Password Manager & Digital Vault с Azure Active Directory (Azure AD).

Интеграция Keeper Password Manager & Digital Vault с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Keeper Password Manager & Digital Vault.
- Вы можете включить автоматический вход пользователей в Keeper Password Manager & Digital Vault (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Keeper Password Manager & Digital Vault, вам потребуется:

- подписка Azure AD;
- подписка на Keeper Password Manager & Digital Vault с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Keeper Password Manager & Digital Vault из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Добавление Keeper Password Manager & Digital Vault из коллекции
Чтобы настроить интеграцию Keeper Password Manager & Digital Vault с Azure AD, необходимо добавить Keeper Password Manager & Digital Vault из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Keeper Password Manager & Digital Vault из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Keeper Password Manager & Digital Vault**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_search.png)

5. На панели результатов выберите **Keeper Password Manager & Digital Vault** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Keeper Password Manager & Digital Vault с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Keeper Password Manager & Digital Vault соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Keeper Password Manager & Digital Vault.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Keeper Password Manager & Digital Vault.

Чтобы настроить и проверить единый вход Azure AD в Keeper Password Manager & Digital Vault, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Keeper Password Manager & Digital Vault](#creating-a-keeperpasswordmanager-test-user)** требуется для создания в Keeper Password Manager & Digital Vault пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Keeper Password Manager & Digital Vault.

**Чтобы настроить единый вход Azure AD в Keeper Password Manager & Digital Vault, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Keeper Password Manager & Digital Vault** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_samlbase.png)

3. В разделе **Домен и URL-адреса Keeper Password Manager & Digital Vault** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`.

    c. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://{SSO CONNECT SERVER}/sso-connect`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html). 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация Keeper Password Manager & Digital Vault** щелкните **Настроить Keeper Password Manager & Digital Vault**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_configure.png) 

7. Чтобы настроить единый вход на стороне **Keeper Password Manager & Digital Vault**, выполните инструкции в [Руководстве по поддержке Keeper](https://keepersecurity.com/assets/pdf/SettingupAzurewithKeeperSSOConnect.pdf).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keeperpasswordmanager-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keeperpasswordmanager-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keeperpasswordmanager-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keeperpasswordmanager-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-keeper-password-manager--digital-vault-test-user"></a>Создание тестового пользователя Keeper Password Manager & Digital Vault

Чтобы пользователи Azure AD могли выполнять вход в Keeper Password Manager & Digital Vault, они должны быть подготовлены в Keeper Password Manager & Digital Vault. Приложение поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. Если вы хотите настроить пользователей вручную, обратитесь в [службу поддержки Keeper](https://keepersecurity.com/contact.html).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Keeper Password Manager & Digital Vault.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Keeper Password Manager & Digital Vault, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Keeper Password Manager & Digital Vault**.

    ![Настройка единого входа](./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_keeper_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии элемента "Keeper Password Manager & Digital Vault" на панели доступа должна появиться страница входа в приложение Keeper Password Manager & Digital Vault. После успешной проверки подлинности вы должны войти в приложение. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keeperpasswordmanager-tutorial/tutorial_general_203.png

