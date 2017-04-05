---
title: "Руководство по интеграции Azure Active Directory с RolePoint | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и RolePoint."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ea9ddb361d013e58d55401112c98a72b487d92d3
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Руководство по интеграции Azure Active Directory с RolePoint

В этом руководстве описано, как интегрировать RolePoint с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением RolePoint дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к RolePoint.
- Вы можете включить автоматический вход пользователей в RolePoint (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением RolePoint, вам потребуется:

- подписка Azure AD;
- подписка RolePoint с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление RolePoint из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-rolepoint-from-the-gallery"></a>Добавление RolePoint из коллекции
Чтобы настроить интеграцию RolePoint с Azure AD, необходимо добавить RolePoint из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RolePoint из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **RolePoint**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_001.png)

7. В области результатов выберите **RolePoint** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в RolePoint с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в RolePoint соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RolePoint.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в RolePoint.

Чтобы настроить и проверить единый вход Azure AD в RolePoint, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RolePoint](#creating-a-rolepoint-test-user)** требуется для создания в RolePoint пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение RolePoint.

Для приложения RolePoint требуется утверждение SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке "**Атрибут**" приложения. На следующем снимке экрана приведен пример. 

![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_01.png)

**Чтобы настроить единый вход Azure AD в RolePoint, сделайте следующее:**

1. На странице интеграции с приложением **RolePoint** классического портала Azure в верхней части страницы меню щелкните **Атрибуты**.

    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_02.png)

2. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:    

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
 
  1. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.

    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_03.png)
  2. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
  3. В списке **Значение атрибута** введите значение атрибута, отображаемое для этой строки.
  4. Нажмите **Завершено**.

3. В меню вверху щелкните **Быстрый запуск**.

    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_04.png) 

4. На странице **Как пользователи должны входить в RolePoint?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 
    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_05.png)

5. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_06.png)
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.rolepoint.com/login`.
  2. Нажмите кнопку **Далее**.
  
    >[!NOTE] 
    >Обратите внимание, что это значение используется только в качестве примера. Вместо него необходимо указать фактический URL-адрес для входа. Чтобы получить это значение, обратитесь в [службу поддержки RolePoint](emaiLto:info@rolepoint.com).
    >

6. На странице **Настройка единого входа в RolePoint** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на своем компьютере.

    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_07.png) 

7. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки RolePoint](emaiLto:info@rolepoint.com) и предоставьте скачанный **файл метаданных**.

8. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

    ![единого входа Azure AD][10]

9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
  
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_05.png) 
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
 3. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_06.png) 
 1. В текстовом поле **Имя** введите **Britta**. 
 2. В текстовом поле **Фамилия** введите **Simon**.
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
 4. В списке **Роль** выберите **Пользователь**.
 5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_08.png) 
 1. Запишите значение поля **Новый пароль**.
 2. Нажмите **Завершено**.   

### <a name="create-a-rolepoint-test-user"></a>Создание тестового пользователя RolePoint

В этом разделе описано, как создать пользователя Britta Simon в приложении RolePoint. Обратитесь к [службу поддержки RolePoint](emaiLto:info@rolepoint.com), чтобы добавить пользователей на платформу RolePoint.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к RolePoint.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в RolePoint, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **RolePoint**.

    ![Настройка единого входа](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203] 

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент RolePoint на панели доступа, вы автоматически войдете в приложение RolePoint.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_205.png

