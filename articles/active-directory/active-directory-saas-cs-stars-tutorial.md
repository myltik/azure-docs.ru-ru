---
title: "Руководство по интеграции Azure Active Directory с приложением CS Stars | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и CS Stars."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5704d151-afb8-40a4-b286-8bacd4f279ee
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 015ca80c952110d3289888ed82d7a543be29950c
ms.openlocfilehash: 3519fcb7621aec61ab35c80558bc0da0e3b2c41e


---
# <a name="tutorial-azure-active-directory-integration-with-cs-stars"></a>Руководство. Интеграция Azure Active Directory с CS Stars
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением CS Stars.  
Интеграция Azure AD с приложением CS Stars обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к CS Stars.
* Вы можете включить автоматический вход пользователей в CS Stars (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с CS Stars, вам потребуется:

* подписка Azure AD;
* подписка CS Stars с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>
>

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление CS Stars из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-cs-stars-from-the-gallery"></a>Добавление CS Stars из коллекции
Чтобы настроить интеграцию CS Stars с Azure AD, необходимо добавить CS Stars из коллекции в список управляемых приложений SaaS.

**Чтобы добавить CS Stars из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]
6. В поле поиска введите **CS Stars**.

    ![Приложения][5]
7. В области результатов выберите **CS Stars** и щелкните **Завершить**, чтобы добавить приложение.

    ![Приложения][400]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в CS Stars с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в CS Stars соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в CS Stars.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в CS Stars.

Чтобы настроить и проверить единый вход Azure AD в CS Stars, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя CS Stars](#creating-a-cs-stars-test-user)** требуется для создания пользователя Britta Simon в CS Stars, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение CS Stars.

**Чтобы настроить единый вход Azure AD в CS Stars, выполните следующие действия:**

1. На странице интеграции с приложением **CS Stars** классического портала Azure щелкните **Настройка единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в CS Stars?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![единого входа Azure AD][7]
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка параметров приложения][8]

    А. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение CS Stars (например, `https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2`).

    > [!NOTE]
    > Если вы не знаете, какое значение использовать, обратитесь к представителю Marsh ClearSight.

    b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в CS Stars** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере.

    ![Что такое Azure AD Connect?][9]
5. Чтобы включить единый вход для CS Stars, обратитесь к представителю Marsh ClearSight и передайте ему файл метаданных.
6. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

    ![Что такое Azure AD Connect?][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  

    ![Что такое Azure AD Connect?][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   

### <a name="creating-a-cs-stars-test-user"></a>Создание тестового пользователя CS Stars
Цель этого раздела — создать пользователя с именем Britta Simon в CS Stars.

Чтобы создать пользователя в CS Stars, обратитесь к представителю Marsh ClearSight.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к CS Stars.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в CS Stars, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

    ![Назначение пользователя][201]
2. В списке приложений выберите **CS Stars**.

    ![Назначение пользователя][202]
3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент CS Stars на панели доступа, вы автоматически войдете в приложение CS Stars.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png



<!--HONumber=Dec16_HO1-->


