---
title: "Руководство. Интеграция Azure Active Directory с Oneteam | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Oneteam."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2e94916c-64ae-4e1a-a8b5-bc6ef7d28c29
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3327faa5864b721fff5d3a34389d214b0ae29a9
ms.openlocfilehash: dab95c2b63e18c2cd21ffb33370ce16404237762


---
# <a name="tutorial-azure-active-directory-integration-with-oneteam"></a>Руководство. Интеграция Azure Active Directory с Oneteam

В этом руководстве описано, как интегрировать Oneteam с Azure Active Directory (Azure AD).

Интеграция Oneteam с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Oneteam.
- Вы можете включить автоматический вход пользователей в Oneteam (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Oneteam, вам потребуется следующее:

- подписка Azure AD;
- подписка Oneteam с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Oneteam из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-oneteam-from-the-gallery"></a>Добавление Oneteam из коллекции
Чтобы настроить интеграцию Oneteam с Azure AD, необходимо добавить Oneteam из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Oneteam из коллекции, выполните следующее.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Oneteam**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_001.png)

7. В области результатов выберите **Oneteam** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Oneteam с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Oneteam соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Oneteam.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Oneteam.

Чтобы настроить и проверить единый вход в Azure AD в Oneteam, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Oneteam](#creating-a-oneteam-test-user)** требуется для создания в Oneteam пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Oneteam.


**Чтобы настроить единый вход Azure AD в Oneteam, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **Oneteam** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Oneteam?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 
    ![Настройка единого входа](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_02.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_03.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://api.one-team.io/teams/<team name>/auth/saml/issuer`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://api.one-team.io/teams/<team name>/auth/saml/callback`.

    c. Нажмите кнопку **Далее**.

4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_04.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<team name>.one-team.io/`.

    b. Нажмите кнопку **Далее**.

    > [!NOTE]
    > Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, <a href="https://support.one-team.com/hc/en-us/requests/new">на этой странице</a> вы можете отправить запрос группе поддержки Oneteam.

5. На странице **Настройка единого входа в Oneteam** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на свой компьютер.

    ![Настройка единого входа](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_05.png) 

6. Чтобы настроить единый вход для своего приложения, <a href="https://support.one-team.com/hc/en-us/requests/new">на этой странице</a> вы можете отправить запрос группе поддержки Oneteam, добавив в него скачанный файл **метаданных**.

7. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

    ![единого входа Azure AD][10]

8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
  
    ![единого входа Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oneteam-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-a-oneteam-test-user"></a>Создание тестового пользователя Oneteam

Цель этого раздела — создать пользователя Britta Simon в Oneteam. Приложение Oneteam поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Oneteam (если он еще не создан).

> [!NOTE]
> Если необходимо создать пользователя вручную, на <a href="https://support.one-team.com/hc/en-us/requests/new">этой странице</a> можно отправить запрос группе поддержки Oneteam.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Oneteam.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Oneteam, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Oneteam**.

    ![Настройка единого входа](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203] 

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
    ![Назначение пользователя][205]



### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Oneteam на панели доступа, вы автоматически войдете в приложение Oneteam.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO3-->


