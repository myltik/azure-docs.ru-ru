---
title: "Руководство по интеграции Azure Active Directory и StatusPage | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и StatusPage."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 3b901bcb08a194f72c3dc75e33f2b94fffea370e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Руководство. Интеграция Azure Active Directory и StatusPage
Цель этого руководства — показать, как интегрировать приложение StatusPage с Azure Active Directory (Azure AD).

Интеграция приложения StatusPage с Azure AD обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к приложению StatusPage. 
* Вы можете включить автоматический вход пользователей в StatusPage (единый вход) с их учетными записями Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением StatusPage, вам потребуются следующие компоненты:

* подписка Azure AD;
* подписка StatusPage с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

* Добавление StatusPage из коллекции. 
* Настройка и проверка единого входа Azure AD.

## <a name="add-statuspage-from-the-gallery"></a>Добавление StatusPage из коллекции
Чтобы настроить интеграцию приложения StatusPage с Azure AD, вам нужно добавить StatusPage из коллекции в список управляемых приложений SaaS.

**Чтобы добавить StatusPage из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **StatusPage**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)
7. В области результатов выберите **StatusPage** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложении StatusPage с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в StatusPage соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в StatusPage.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в StatusPage.

Чтобы настроить и проверить единый вход Azure AD в приложении StatusPage, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя в приложении StatusPage](#creating-a-statuspage-test-user)** требуется для создания в StatusPage соответствующего пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение StatusPage. 

**Чтобы настроить единый вход Azure AD в StatusPage, выполните следующее.**

1. На классическом портале Azure на странице интеграции с приложением **StatusPage** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в StatusPage?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 
   
   >[!NOTE]
   >Обратитесь в службу поддержки StatusPage по адресу [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io), чтобы запросить метаданные, необходимые для настройки единого входа. 
   > 
  1. В метаданных скопируйте значение издателя и вставьте его в текстовое поле **Идентификатор** .
  2. В полученных метаданных скопируйте значение URL-адреса ответа и вставьте его в текстовое поле **URL-адрес ответа** .
  3. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в StatusPage** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png)   
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. В другом окне браузера войдите на корпоративный сайт StatusPage с правами администратора.
6. На главной панели инструментов щелкните **Manage Account**(Управление учетной записью).
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 
7. Выберите вкладку **Single Sign-on** (Единый вход). 
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 
8. На странице настройки единого входа выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png)  
  1. На классическом портале Azure на странице диалогового окна **Настройка единого входа в StatusPage** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **SSO Target URL** (Целевой URL-адрес единого входа). 
  2. Откройте загруженный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат** . 
  3. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![единого входа Azure AD][10]
10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png)  
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.  
  2. Нажмите **Завершено**.   

### <a name="create-a-statuspage-test-user"></a>Создание тестового пользователя StatusPage
Цель этого раздела — создать в приложении StatusPage пользователя с именем Britta Simon.

Приложение StatusPage поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

**Чтобы создать в приложении StatusPage пользователя с именем Britta Simon, выполните следующие действия.**

1. Войдите на корпоративный сайт StatusPage с правами администратора.
2. В меню вверху щелкните **Manage Account**(Управление учетной записью).
3. Перейдите на вкладку Team Members (Участники команды). 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 
4. Щелкните **Add Team Member**(Добавить участника команды). 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 
5. Введите в текстовые поля **Email Address** (Электронный адрес), **First Name** (Имя) и **Sur Name** (Фамилия) соответствующие данные действительного пользователя, которого вы хотите подготовить. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 
6. Для параметра **Role** (Роль) выберите значение **Client Administrator** (Администратор клиента).
7. Щелкните **Create Account**(Создать учетную запись).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к StatusPage, чтобы этот пользователь мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению StatusPage, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **StatusPage**.
   
    ![Настройка единого входа](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент StatusPage на панели доступа, вы автоматически войдете в приложение StatusPage.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png







