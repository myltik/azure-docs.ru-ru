---
title: "Руководство по интеграции Azure Active Directory с Pantheon | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Pantheon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 52a42d6705d0d2cfb86865027f8315411d7aece0
ms.openlocfilehash: b14872daab1bddf18d68b1f0fb23fc74c12078ed


---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Руководство по интеграции Azure Active Directory с Pantheon

В этом руководстве описано, как интегрировать Pantheon с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Pantheon обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Pantheon.
- Вы можете включить автоматический вход пользователей в Pantheon (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Pantheon, вам потребуется:

- подписка Azure AD;
- подписка Pantheon с поддержкой единого входа.


>[!NOTE] 
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Pantheon из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-pantheon-from-the-gallery"></a>Добавление Pantheon из коллекции
Чтобы настроить интеграцию Pantheon с Azure AD, необходимо добавить Pantheon из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pantheon из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Pantheon**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_01.png)

7. В области результатов выберите **Pantheon** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Pantheon с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Pantheon соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pantheon.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pantheon.

Чтобы настроить и проверить единый вход Azure AD в Pantheon, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pantheon](#creating-a-pantheon-test-user)** требуется для создания в Pantheon пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Pantheon.


**Чтобы настроить единый вход Azure AD в Pantheon, выполните следующие действия:**

Приложение Pantheon ожидает получить утверждение SAML в определенном формате. Для этого формата потребуется указать адрес электронной почты пользователя в качестве значения атрибута NameIdentifier. По умолчанию Azure AD использует для атрибута NameIdentifier значение UserPrincipalName. Для успешной интеграции это значение следует изменить так, чтобы оно совпадало с адресом электронной почты пользователя.
Интеграция будет работать только после выполнения правильного сопоставления.

![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_06.png)
    
1. На классическом портале на странице интеграции с приложением **Pantheon** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Pantheon?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_04.png) 

    а. В текстовом поле **Идентификатор** введите URN в следующем формате: `urn:auth0:pantheon:<orgname>-SSO`.
    
    b. В текстовое поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`. 

    В. click **Далее**
 
4. На странице **Настройка единого входа в Pantheon** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_05.png)

    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Для настройки единого входа в приложении обратитесь в [службу поддержки Pantheon](https://pantheon.io/docs/getting-support) и предоставьте следующие сведения:

    • скачанный **сертификат**

    • **URL-адрес службы единого входа**

    >[!NOTE] 
    >Чтобы включить эту связь, необходимо также предоставить сведения о доменах электронной почты и о формате даты и времени. Дополнительные сведения см. [здесь](https://pantheon.io/docs/sso-organizations/).

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
 
    ![единого входа Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pantheon-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-an-pantheon-test-user"></a>Создание тестового пользователя Pantheon

В этом разделе описано, как создать пользователя Britta Simon в приложении Pantheon. Выполните следующие ниже действия, чтобы добавить пользователя в Pantheon. 

>[!NOTE] 
>Чтобы единый вход начал работать, необходимо сначала создать пользователя в Pantheon.

1. Войдите в Pantheon с учетными данными администратора.

2. Перейдите на страницу **Organization** (Организация) панели мониторинга.
 
3. Выберите параметр **Пользователи**.

4. Нажмите кнопку **Добавить пользователя**.

5. Введите адрес электронной почты пользователя.

6. Выберите роль пользователя.

7. Нажмите кнопку **Добавить пользователя**.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Pantheon.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pantheon, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pantheon**.

    ![Настройка единого входа](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Pantheon на панели доступа, вы автоматически войдете в приложение Pantheon.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


