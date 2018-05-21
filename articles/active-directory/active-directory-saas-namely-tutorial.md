---
title: Руководство по интеграции Azure Active Directory с Namely | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Namely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: a7f3fac69143cc4863b65604c6aac314157ba826
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Руководство по интеграции Azure Active Directory с Namely

В этом руководстве описано, как интегрировать Namely с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Namely обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Namely.
- Вы можете включить автоматический вход пользователей в Namely (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Namely, вам потребуется:

- подписка Azure AD;
- подписка Namely с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Namely из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-namely-from-the-gallery"></a>Добавление Namely из коллекции
Чтобы настроить интеграцию Namely с Azure AD, необходимо добавить Namely из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Namely из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Namely**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_search.png)

5. На панели результатов выберите **Namely** и щелкните **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описаны настройка и проверка единого входа Azure AD в приложение Namely с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Namely соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Namely.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Namely.

Чтобы настроить и проверить единый вход Azure AD в Namely, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Namely](#creating-a-namely-test-user)** требуется для создания в Namely пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Namely.

**Чтобы настроить единый вход Azure AD в Namely, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Namely** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Namely** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.namely.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов Namely](https://www.namely.com/contact/). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Namely** щелкните **Настроить Namely**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_configure.png) 

7. В другом окне браузера войдите на корпоративный сайт Namely с правами администратора.

8. На панели инструментов в верхней части экрана щелкните **Компания**.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

9. Перейдите на вкладку **Параметры** .
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 

10. Нажмите кнопку **SAML**.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 

11. На странице **Параметры SAML** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png)
 
    a. Выберите команду **Enable SAML**(Включить SAML). 

    Б. В текстовое поле **Identity Provider SSO URL** (URL-адрес единого входа поставщика удостоверений) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.
    
    c. Откройте сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Identity provider certificate** (Сертификат поставщика удостоверений).
     
    d. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-namely-test-user"></a>Создание тестового пользователя Namely

Цель этого раздела — создать пользователя с именем Britta Simon в Namely.

**Чтобы создать пользователя с именем Britta Simon в Namely, выполните следующие действия.**

1. Войдите на корпоративный сайт Namely с правами администратора.

2. На панели инструментов в верхней части экрана щелкните **People**(Пользователи).
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

3. Щелкните вкладку **Directory** (Каталог).
   
    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

4. Щелкните **Add New Person**(Добавить нового пользователя).

    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_12.png)

5. В диалоговом окне **Add New Person** (Добавление пользователя) выполните следующие действия.

    a. В текстовое поле **Имя** введите **Britta**.

    Б. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Email** (Адрес электронной почты) введите **адрес электронной почты** пользователя BrittaSimon.

    d. Выберите команду **Сохранить**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Namely.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Namely, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Namely**.

    ![Настройка единого входа](./media/active-directory-saas-namely-tutorial/tutorial_namely_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Namely на панели доступа, вы автоматически войдете в приложение Namely.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-namely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png

