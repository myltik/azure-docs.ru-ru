---
title: Руководство по интеграции Azure Active Directory с Tableau Online | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 4747c2146c6590e30da2ba8985ecdfbd6a75ae96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Руководство. Интеграция Azure Active Directory с Tableau Online

В этом руководстве описано, как интегрировать Tableau Online с Azure Active Directory (Azure AD).

Интеграция Tableau Online с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Tableau Online.
- Вы можете включить автоматический вход пользователей в Tableau Online (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Tableau Online, вам потребуется следующее:

- подписка Azure AD;
- подписка на Tableau Online с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Tableau Online из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-tableau-online-from-the-gallery"></a>Добавление Tableau Online из коллекции.
Чтобы настроить интеграцию Tableau Online с Azure AD, необходимо добавить Tableau Online из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Tableau Online из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Tableau Online**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. На панели результатов выберите **Tableau Online** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Tableau Online с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Tableau Online соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Tableau Online.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Tableau Online.

Чтобы настроить и проверить единый вход Azure AD в Tableau Online, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Tableau Online](#creating-a-tableau-online-test-user)** требуется для создания в Tableau Online пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как на портале Azure включить единый вход Azure AD и настроить его в приложении Tableau Online.

**Чтобы настроить единый вход Azure AD в Tableau Online, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Tableau Online** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Tableau Online** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://sso.online.tableau.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `.

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. В другом окне браузера войдите в приложение Tableau Online. Выберите **Параметры**, а затем щелкните **Проверка подлинности**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Чтобы включить SAML, в разделе **Типы проверки подлинности** установите флажок **Single sign-on with SAML** (Единый вход с помощью SAML).
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Прокрутите вниз до раздела **Import metadata file into Tableau Online** (Импорт файла метаданных в Tableau Online).  Нажмите кнопку "Обзор" и импортируйте файл метаданных, скачанный из Azure AD. Нажмите кнопку **Применить**.
   
   ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. В разделе **Match assertions** (Сопоставление утверждений) вставьте соответствующее имя утверждения поставщика удостоверений для **электронной почты**, **имени** и **фамилии**. Чтобы получить эти сведения из Azure AD, сделайте следующее: 
  
    a. Перейдите на страницу интеграции с приложением **Tableau Online** портала Azure.
    
    Б. В разделе атрибутов установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и задайте эти атрибуты. 
    
   ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Скопируйте значение пространства имен для этих атрибутов: имя, электронная почта и фамилия, выполнив следующие действия:

   ![единого входа Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Щелкните значение **user.givenname**. 
    
    д. Скопируйте значение из текстового поля **Пространство имен**.

   ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Чтобы скопировать значения пространства имен для электронной почты и фамилии, выполните описанные выше шаги.

    ж. Перейдите к приложению Tableau Online, а затем задайте в разделе **Tableau Online Attributes** (Атрибуты Tableau Online) следующее:
     * электронный адрес: **mail** или **userprincipalname**;
     * имя: **givenname**
     * фамилия: **surname**
   
   ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-tableau-online-test-user"></a>Создание тестового пользователя Tableau Online

В этом разделе описано, как создать пользователя Britta Simon в приложении Tableau Online.

1. В приложении **Tableau Online** щелкните **Settings** (Параметры), а затем выберите раздел **Authentication** (Проверка подлинности). Прокрутите вниз до раздела **Выбор пользователей** . Щелкните **Add Users** (Добавить пользователей), а затем — **Enter Email Addresses** (Введите адреса электронной почты).
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Установите переключатель **Add users for single sign-on (SSO) authentication**(Добавить пользователей для проверки подлинности единого входа). В текстовое поле **Enter Email Addresses** (Введите адреса электронной почты) добавьте britta.simon@contoso.com.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Нажмите кнопку **Создать**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Tableau Online.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Tableau Online, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Tableau Online**.

    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Tableau Online на панели доступа, вы автоматически войдете в приложение Tableau Online.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
