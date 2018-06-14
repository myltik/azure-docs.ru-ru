---
title: Руководство. Интеграция Azure Active Directory с HappyFox | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и HappyFox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jeedes
ms.openlocfilehash: 21515b85812a3cf0b345154d2582d7236ab28d3c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340732"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Руководство. Интеграция Azure Active Directory с HappyFox

В этом руководстве описано, как интегрировать HappyFox с Azure Active Directory (Azure AD).

Интеграция приложения HappyFox с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению HappyFox.
- Вы можете включить автоматический вход пользователей в HappyFox (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с HappyFox, вам потребуется:

- подписка Azure AD;
- подписка HappyFox с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление HappyFox из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-happyfox-from-the-gallery"></a>Добавление HappyFox из коллекции
Чтобы настроить интеграцию приложения HappyFox с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения HappyFox из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **HappyFox**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_search.png)

5. На панели результатов выберите **HappyFox** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в HappyFox с использованием тестового пользователя Britta Simon.

Для работы единого входа службе Azure AD нужно знать, какой пользователь в HappyFox соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в HappyFox.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HappyFox.

Чтобы настроить и проверить единый вход Azure AD в HappyFox, выполните следующие действия:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя HappyFox](#creating-a-happyfox-test-user)** требуется для создания в HappyFox пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении HappyFox.

**Настройка единого входа Azure AD в HappyFox**

1. На портале Azure на странице интеграции с приложением **HappyFox** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_samlbase.png)

3. В разделе **Домены и URL-адреса приложения HappyFox** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.happyfox.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов HappyFox](https://support.happyfox.com/home). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация HappyFox** щелкните **Настроить HappyFox**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_configure.png) 

7. Войдите на портал HappyFox для сотрудников, перейдите к разделу **Manage** (Управление) и щелкните вкладку **Integrations** (Интеграция).

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/header.png) 

8. На вкладке Integrations (Интеграция) щелкните **Configure** (Настройка) в разделе **SAML Integration** (Интеграция SAML), чтобы открыть параметры единого входа.

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/configure.png) 

9. В разделе конфигурации SAML вставьте **URL-адрес службы единого входа SAML**, скопированный на портале Azure, в текстовое поле **SSO Target URL** (Целевой URL-адрес единого входа).

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/targeturl.png)

10. Откройте в Блокноте сертификат, загруженный с портала Azure, и вставьте его содержимое в раздел **IdP Signature** (Подпись поставщика удостоверений).
 
    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/cert.png)

11. Нажмите кнопку **Save Settings** (Сохранить параметры).

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/savesettings.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-happyfox-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-happyfox-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-happyfox-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-happyfox-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-happyfox-test-user"></a>Создание тестового пользователя HappyFox

Приложение поддерживает JIT-подготовку пользователей, поэтому после проверки подлинности пользователи будут созданы в приложении автоматически.
        
### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к HappyFox.

![Назначение пользователя][200] 

**Назначение пользователя Britta Simon приложению HappyFox**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **HappyFox**.

    ![Настройка единого входа](./media/active-directory-saas-happyfox-tutorial/tutorial_happyfox_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

1. Когда вы нажмете плитку HappyFox на панели доступа, должна появиться страница входа в приложение HappyFox. На этой странице входа вы увидите кнопку **SAML**.

    ![Подключаемый модуль](./media/active-directory-saas-happyfox-tutorial/saml.png) 

2. Нажмите кнопку **SAML**, чтобы войти в HappyFox с помощью учетной записи Azure AD.

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-happyfox-tutorial/tutorial_general_203.png

