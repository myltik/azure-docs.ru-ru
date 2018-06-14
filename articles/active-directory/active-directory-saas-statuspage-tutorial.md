---
title: Руководство по интеграции Azure Active Directory и StatusPage | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 4b28e9c90da60aa9fb2113c5a179095156d16e02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352826"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Руководство. Интеграция Azure Active Directory и StatusPage

В этом руководстве описано, как интегрировать StatusPage с Azure Active Directory (Azure AD).

Интеграция приложения StatusPage с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению StatusPage.
- Вы можете включить автоматический вход пользователей в StatusPage (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением StatusPage, вам потребуются следующие компоненты:

- подписка Azure AD;
- подписка StatusPage с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление StatusPage из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-statuspage-from-the-gallery"></a>Добавление StatusPage из коллекции.
Чтобы настроить интеграцию приложения StatusPage с Azure AD, вам нужно добавить StatusPage из коллекции в список управляемых приложений SaaS.

**Чтобы добавить StatusPage из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **StatusPage**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_search.png)

5. На панели результатов выберите **StatusPage** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в StatusPage с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в StatusPage соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в StatusPage.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в StatusPage.

Чтобы настроить и проверить единый вход Azure AD в приложении StatusPage, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя StatusPage](#creating-a-statuspage-test-user)** требуется для того, чтобы в StatusPage существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении StatusPage.

**Чтобы настроить единый вход Azure AD в StatusPage, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **StatusPage** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. В разделе **Домены и URL-адреса приложения StatusPage** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Обратитесь в службу поддержки StatusPage по адресу [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io), чтобы запросить метаданные, необходимые для настройки единого входа. 
    >
    >a. В метаданных скопируйте значение издателя и вставьте его в текстовое поле **Идентификатор** .
    >
    >Б. В полученных метаданных скопируйте значение URL-адреса ответа и вставьте его в текстовое поле **URL-адрес ответа** .

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация StatusPage** щелкните **Настроить StatusPage**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_configure.png) 

7. В другом окне браузера войдите на корпоративный сайт StatusPage с правами администратора.

8. На главной панели инструментов щелкните **Manage Account**(Управление учетной записью).
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 

10. Выберите вкладку **Single Sign-on** (Единый вход). 
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 

11. На странице настройки единого входа выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. В текстовое поле **SSO Target URL** (Целевой URL-адрес единого входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    Б. Откройте загруженный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат** . 

    c. Щелкните **SAVE CONFIGURATION** (Сохранить конфигурацию).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-statuspage-test-user"></a>Создание тестового пользователя в приложении StatusPage

Цель этого раздела — создать в приложении StatusPage пользователя с именем Britta Simon.

Приложение StatusPage поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа Azure AD](#configuring-azure-ad-single-sign-on).

**Чтобы создать в приложении StatusPage пользователя с именем Britta Simon, выполните следующие действия.**

1. Войдите на корпоративный сайт StatusPage с правами администратора.

2. В меню вверху щелкните **Manage Account**(Управление учетной записью).

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png)

3. Перейдите на вкладку **Team Members** (Участники команды). 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 

4. Щелкните **ADD TEAM MEMBER** (Добавить участника команды). 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 

5. Введите в текстовые поля **Email Address** (Электронный адрес), **First Name** (Имя) и **Sur Name** (Фамилия) соответствующие данные действительного пользователя, которого вы хотите подготовить. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 

6. Для параметра **Role** (Роль) выберите значение **Client Administrator** (Администратор клиента).

7. Щелкните **CREATE ACCOUNT** (Создать учетную запись).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к StatusPage.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению StatusPage, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **StatusPage**.

    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент StatusPage на панели доступа, вы автоматически войдете в приложение StatusPage.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png

