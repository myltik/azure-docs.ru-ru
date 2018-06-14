---
title: Учебник. Интеграция Azure Active Directory с Kontiki | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: f44ac9069ad92e9643d1a01a16e0435fde5fc6b6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343656"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Руководство. Интеграция Azure Active Directory с Kontiki

В этом руководстве описано, как интегрировать Kontiki с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Kontiki предоставляет следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Kontiki.
- Вы можете включить автоматический вход пользователей в Kontiki (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Kontiki, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа Kontiki

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Kontiki из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-kontiki-from-the-gallery"></a>Добавление Kontiki из коллекции
Чтобы настроить интеграцию Kontiki с Azure AD, необходимо добавить Kontiki из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kontiki из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Kontiki**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kontiki-tutorial/tutorial_kontiki_search.png)

5. На панели результатов выберите **Kontiki** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kontiki-tutorial/tutorial_kontiki_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Kontiki для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Kontiki соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kontiki.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Kontiki.

Чтобы настроить и проверить единый вход Azure AD в Kontiki, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Kontiki](#creating-a-kontiki-test-user)** требуется для создания в Kontiki пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Kontiki.

**Чтобы настроить единый вход Azure AD в Kontiki, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Kontiki** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-kontiki-tutorial/tutorial_kontiki_samlbase.png)

3. В разделе **Домены и URL-адреса Kontiki** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-kontiki-tutorial/tutorial_kontiki_url.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.mc.eval.kontiki.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес для входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Kontiki](http://customersupport.kontiki.com/enterprise/contactsupport.html). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-kontiki-tutorial/tutorial_kontiki_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-kontiki-tutorial/tutorial_general_400.png) 

6. Чтобы настроить единый вход на стороне **Kontiki**, отправьте в [службу поддержки Kontiki](http://customersupport.kontiki.com/enterprise/contactsupport.html) скачанный **XML-файл метаданных**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kontiki-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kontiki-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kontiki-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kontiki-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-kontiki-test-user"></a>Создание тестового пользователя Kontiki

Элемент действия для настройки подготовки пользователей в Kontiki отсутствует. Когда назначенный пользователь пытается войти в Kontiki с помощью панели доступа, Kontiki проверяет, существует ли данный пользователь.  

>[!NOTE]
>Если учетная запись пользователя отсутствует, Kontiki автоматически создает ее.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon, предоставив этому пользователю доступ к Kontiki.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon для приложения Kontiki, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Kontiki**.

    ![Настройка единого входа](./media/active-directory-saas-kontiki-tutorial/tutorial_kontiki_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Kontiki на панели доступа, вы автоматически войдете в приложение Kontiki.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kontiki-tutorial/tutorial_general_203.png

