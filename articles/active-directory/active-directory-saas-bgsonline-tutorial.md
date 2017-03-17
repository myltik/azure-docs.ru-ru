---
title: "Руководство по интеграции Azure Active Directory с BGS Online | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и BGS Online."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4fd6b29b-1b46-4fd1-9f5e-16b1c9d892cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 6af7b7777a0ea0b0eba3f499b2e34f30d5bc1ea0
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bgs-online"></a>Учебник. Интеграция Azure Active Directory с BGS Online
В этом учебнике описано, как интегрировать BGS Online с Azure Active Directory (Azure AD).

Интеграция BGS Online с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к BGS Online.
* Вы можете включить автоматический вход пользователей в BGS Online (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с BGS Online, вам потребуется:

* подписка Azure AD;
* подписка на BGS Online с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление BGS Online из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-bgs-online-from-the-gallery"></a>Добавление BGS Online из коллекции
Чтобы настроить интеграцию BGS Online с Azure AD, необходимо добавить BGS Online из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BGS Online из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **BGS Online**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_01.png)
7. В области результатов выберите **BGS Online** и нажмите кнопку **Завершить**, чтобы добавить это приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в BGS Online с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь BGS Online соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BGS Online.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в BGS Online.

Чтобы настроить и проверить единый вход Azure AD в BGS Online, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BGS Online](#creating-a-bgsonline-test-user)** нужно для того, чтобы в BGS Online также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В этом разделе описано, как на классическом портале включить единый вход Azure AD и настроить его в приложении BGS Online.

**Чтобы настроить единый вход Azure AD в BGS Online, сделайте следующее:**

1. На странице интеграции с приложением **BGS Online** на классическом портале щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в BGS Online?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_04.png) 
  1. В текстовом поле **Идентификатор** введите URL-адрес, используемый пользователями для входа в приложение BGS Online. Для рабочей среды используйте шаблон `https://<company name>.millwardbrown.report`, а для тестовой среды — `https://millwardbrown.marketingtracker.nl/mt5/`.
  2. В текстовом поле **URL-адрес ответа** для рабочей среды используйте шаблон `https://<company name>.millwardbrown.report/sso/saml/AssertionConsumerService.aspx`, а для тестовой среды — `https://millwardbrown.marketingtracker.nl/mt5/sso/saml/AssertionConsumerService.aspx`.
  3. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в BGS Online** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_05.png)
  * Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
5. Чтобы настроить единый вход для своего приложения, обратитесь к [группе поддержки](mailTo:bgsdashboardteam@millwardbrown.com) BGS Online и предоставьте следующие сведения:
  * Скачанный файл **метаданных**.
  * **URL-адрес единого входа SAML**
6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Единый вход в Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_06.png)   
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bgsonline-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-an-bgs-online-test-user"></a>Создание тестового пользователя BGS Online
В этом разделе описано, как создать пользователя Britta Simon в BGS Online. Обратитесь к [группе поддержки](mailTo:bgsdashboardteam@millwardbrown.com) BGS Online, чтобы добавить пользователей в платформе BGS Online.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к BGS Online.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BGS Online, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **BGS Online**.
   
    ![Настройка единого входа](./media/active-directory-saas-bgsonline-tutorial/tutorial_bgsonline_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "BGS Online" на панели доступа, вы автоматически войдете в приложение BGS Online.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bgsonline-tutorial/tutorial_general_205.png

