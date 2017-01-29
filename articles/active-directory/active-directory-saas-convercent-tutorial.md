---
title: "Руководство. Интеграция Azure Active Directory с Convercent | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Convercent."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: aec25285cd0fa2f09e21a629648e015dffbeb11d
ms.openlocfilehash: 9ea835787db25adabe9b8cb71551de49b330a6dd


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Руководство. Интеграция Azure Active Directory с Convercent

В этом руководстве описано, как интегрировать Convercent с Azure Active Directory (Azure AD).

Интеграция Convercent с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Convercent.
- Вы можете включить автоматический вход пользователей в Convercent (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Convercent, вам потребуется следующее:

- подписка Azure AD;
- подписка Convercent с поддержкой единого входа.


>[!NOTE] 
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Convercent из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-convercent-from-the-gallery"></a>Добавление Convercent из коллекции
Чтобы настроить интеграцию Convercent с Azure AD, необходимо добавить Convercent из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Convercent из коллекции, выполните следующее.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Convercent**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

7. В области результатов выберите **Convercent** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Convercent с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Convercent соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Convercent.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Convercent.

Чтобы настроить и проверить единый вход в Azure AD в Convercent, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Convercent](#creating-a-convercent-test-user)** требуется для создания в Convercent пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Convercent.


**Чтобы настроить единый вход Azure AD в Convercent, выполните следующее.**

1. На классическом портале на странице интеграции с приложением **Convercent** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Convercent?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png) 

3. На странице диалогового окна **Настроить параметры приложения** нажмите кнопку "Далее". Это позволит настроить приложение в режиме, инициируемом поставщиком удостоверений.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    >[!NOTE] 
    >При настройке этого приложения в режиме, инициируемом поставщиком удостоверений, необходимо настроить RelayState для приложения. В противном случае интеграция единого входа не будет работать. Выполните шаг 5, чтобы настроить RelayState.

4. Если вы хотите настроить приложение в режиме, инициируемом поставщиком услуг, установите флажок **Показать дополнительные настройки** и выполните указанные ниже действия.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png) 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Convercent, используя следующий формат: `https://app.convercent.com/`.
    
    b. click **Далее**
 
5. Если вы настроили приложение в режиме, инициируемом поставщиком удостоверений, то задайте значение RelayState для этого приложения. Чтобы настроить значение RelayState в Azure AD, выполните следующие действия. 
    
    а. Войдите на [портал управления Azure](https://portal.azure.com) с учетной записью администратора.

    b. В области навигации слева щелкните **Больше служб**. 
    
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_07.png)

    c. В текстовое поле **Поиск** введите **Azure Active Directory**, а затем щелкните связанную ссылку.
    
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_08.png)

    d. Щелкните **Корпоративные приложения**.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_09.png)

    д. В разделе **Управление** щелкните **Все приложения**.
    
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_10.png)

    Е. В текстовое поле **Поиск** введите **ADP eTime**, а затем щелкните связанную ссылку. 
    
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_11.png)

    ж. В разделе **Управление** щелкните **Единый вход**.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_12.png)

    h. Выберите **Показать дополнительные параметры URL-адресов**.
    
    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_13.png)
    
    i. В текстовом поле **Состояние ретранслятора** введите значение в следующем формате: `https://app.convercent.com/`.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_14.png)

    j. Сохраните параметры.

6. На странице **Настройка единого входа в Convercent** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_06.png)

    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


6. Чтобы настроить единый вход для своего приложения, обратитесь к [группе поддержки](mailTo:support@convercent.com) Convercent и предоставьте следующее.

    а. Скачанный файл **метаданных**.

7. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
 
    ![единого входа Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-an-convercent-test-user"></a>Создание тестового пользователя Convercent

В этом разделе описано, как создать пользователя Britta Simon в Convercent. Обратитесь к [группе поддержки](mailTo:support@convercent.com) Convercent, чтобы добавить пользователей на платформу Convercent.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Convercent.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Convercent, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Convercent**.

    ![Настройка единого входа](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Convercent на панели доступа, вы автоматически войдете в приложение Convercent.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO3-->


