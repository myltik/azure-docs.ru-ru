---
title: "Руководство по интеграции Azure Active Directory с Tableau Online | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Tableau Online."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: de8292e6d3642776fc684ded731f48845de846d4
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Руководство. Интеграция Azure Active Directory с Tableau Online
В этом руководстве описано, как интегрировать Tableau Online с Azure Active Directory (Azure AD).

Интеграция Tableau Online с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Tableau Online.
* Вы можете включить автоматический вход пользователей в Tableau Online (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением Tableau Online, вам потребуется следующее:

* подписка Azure AD;
* подписка на **Tableau Online** с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Tableau Online из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-tableau-online-from-the-gallery"></a>Добавление Tableau Online из коллекции
Чтобы настроить интеграцию Tableau Online с Azure AD, необходимо добавить Tableau Online из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Tableau Online из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Tableau Online**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)
7. В области результатов выберите **Tableau Online** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Tableau Online с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Tableau Online соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Tableau Online.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Tableau Online.

Чтобы настроить и проверить единый вход Azure AD в Tableau Online, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Tableau Online](#creating-a-Tableau-Online-test-user)** требуется для создания в Tableau Online пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Tableau Online.

**Чтобы настроить единый вход Azure AD в Tableau Online, сделайте следующее:**

1. В меню вверху щелкните **Быстрый запуск**.
   
    ![Настройка единого входа][6]
2. На странице интеграции с приложением **Tableau Online** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7] 
3. На странице **Как пользователи должны входить в Tableau Online** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)
4. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. 
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)
  1. В текстовом поле "URL-адрес для входа" введите URL-адрес в следующем формате: `https://sso.online.tableau.com`.
  2. Нажмите кнопку **Далее**.
5. На странице **Настройка единого входа в Tableau Online** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на свой компьютер.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)
6. Выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]
8. В другом окне браузера войдите в приложение Tableau Online. Выберите **Параметры**, а затем щелкните **Проверка подлинности**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
9. В разделе **Authentication Types** (Типы проверки подлинности) установите флажок **Single sign-on with SAML** (Единый вход с помощью SAML), чтобы включить SAML.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)
10. Прокрутите вниз до раздела **Import metadata file into Tableau Online** (Импорт файла метаданных в Tableau Online).  Нажмите кнопку "Обзор" и импортируйте файл метаданных, скачанный из Azure AD. Нажмите кнопку **Применить**.
   
   ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)
11. В разделе **Match assertions** (Сопоставление утверждений) вставьте соответствующее имя утверждения поставщика удостоверений для электронной почты, имени и фамилии. Чтобы получить эти сведения из Azure AD, сделайте следующее: 
  1. Вернитесь к Azure AD. Перейдите на страницу интеграции с приложением **Tableau Online** классического портала Azure.
  2. В верхней области меню щелкните **Атрибуты**. 
  3. Скопируйте имена значений: userprincipalname, givenname и surname.
   
     ![Единый вход в Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
  4. Перейдите к приложению Tableau Online, а затем задайте в разделе **Tableau Online Attributes** (Атрибуты Tableau Online) следующее:
     * электронный адрес: **mail** или **userprincipalname**;
     * имя: **givenname**
     * фамилия: **surname**
   
   ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**. 
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-tableau-online-test-user"></a>Создание тестового пользователя Tableau Online
В этом разделе описано, как создать пользователя Britta Simon в приложении Tableau Online.

1. В приложении **Tableau Online** щелкните **Settings** (Параметры), а затем выберите раздел **Authentication** (Проверка подлинности). Прокрутите вниз до раздела **Выбор пользователей** . Щелкните **Add Users** (Добавить пользователей), а затем — **Enter Email Addresses** (Введите адреса электронной почты).
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Установите переключатель **Add users for single sign-on (SSO) authentication**(Добавить пользователей для проверки подлинности единого входа). В текстовое поле **Enter Email Addresses** (Введите адреса электронной почты) добавьте britta.simon@contoso.com.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Щелкните **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Tableau Online.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Tableau Online, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Tableau Online**.
   
    ![Настройка единого входа](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке "Все пользователи" выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Tableau Online на панели доступа, вы автоматически войдете в приложение Tableau Online.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png

