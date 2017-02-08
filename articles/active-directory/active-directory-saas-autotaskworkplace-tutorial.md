---
title: "Руководство по интеграции Azure Active Directory с Autotask | Документация Майкрософт"
description: "Сведения об использовании Autotask вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d22f107be306ad47426b89adb99f07f9d1f6617c
ms.openlocfilehash: 10292e19216e01034a597368e0f2570d49e71610


---

# <a name="tutorial-azure-active-directory-integration-with-autotask"></a>Руководство по интеграции Azure Active Directory с Autotask

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Autotask.

Интеграция Azure AD с приложением Autotask обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Autotask. 
- Вы можете включить автоматический вход пользователей в Autotask (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Autotask, вам потребуется:

- подписка Azure AD;
- подписка на Autotask с поддержкой единого входа.


> [!NOTE] 
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Autotask из коллекции.
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-autotask-from-the-gallery"></a>Добавление Autotask из коллекции
Чтобы настроить интеграцию Autotask с Azure AD, необходимо добавить Autotask из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Autotask из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
    
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
    
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Autotask**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_01.png)

7. В области результатов выберите **Autotask** и щелкните **Завершить**, чтобы добавить приложение.

    ![Выбор приложения в коллекции](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в Autotask с использованием тестового пользователя Britta Simon.

Для настройки единого входа Azure AD необходимо знать, какой пользователь в Autotask соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Autotask.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Autotask. 

Чтобы настроить и проверить единый вход Azure AD в Autotask, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Autotask](#creating-an-autotask-workplace-test-user)** требуется для создания в Autotask пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Autotask.

**Чтобы настроить единый вход Azure AD в Autotask, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Autotask** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Autotask?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
    
    ![Настройка единого входа](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_03.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_04.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/metadata`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<your-subdomain>.awp.autotask.net/singlesignon/saml/SSO`.

    c. Нажмите кнопку **Далее**.

4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_05.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your-subdomain>.awp.autotask.net/loginsso`.

    b. Нажмите кнопку **Далее**.

    > [!NOTE] 
    > Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы узнать эти адреса, обратитесь к [группе поддержки Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm).

4. На странице **Настройка единого входа в Autotask** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_06.png)

5. Для настройки единого входа в вашем приложении обратитесь в [службу поддержки Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) и предоставьте скачанный **файл метаданных**.

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-an-autotask-test-user"></a>Создание тестового пользователя Autotask

В этом разделе описано, как создать пользователя Britta Simon в приложении Autotask. Обратитесь в [службу поддержки Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm), чтобы добавить пользователей в платформу Autotask.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Цель этого раздела — предоставить пользователю Britta Simon доступ к Autotask, чтобы он мог использовать единый вход Azure.
    
![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Autotask, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
    
    ![Назначение пользователя][201]

2. В списке приложений выберите **Autotask**.
    
    ![Настройка единого входа](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
    
    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку Autotask на панели доступа, вы автоматически войдете в приложение Autotask.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO4-->


