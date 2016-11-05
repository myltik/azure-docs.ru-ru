---
title: Руководство. Интеграция Azure Active Directory с Learning at Work | Microsoft Docs
description: Узнайте, как настроить единый вход между Azure Active Directory и Learning at Work.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с Learning at Work
В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением Learning at Work.

Интеграция Learning at Work с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Learning at Work.
* Вы можете включить автоматический вход пользователей в Learning at Work (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с Learning at Work, вам потребуется:

* подписка Azure AD;
* подписка Learning at Work (Saba Cloud) с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Learning at Work из коллекции.
2. Настройка и проверка единого входа в Azure AD

## Добавление Learning at Work из коллекции
Чтобы настроить интеграцию Learning at Work с Azure AD, необходимо добавить Learning at Work из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Learning at Work из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Learning at Work**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_01.png)
7. В области результатов выберите **Learning at Work** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_06.png)

## Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Learning at Work с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Learning at Work соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Learning at Work.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Learning at Work.

Чтобы настроить и проверить единый вход Azure AD в Learning at Work, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Learning at Work](#creating-a-predictix-price-reporting-test-user)** требуется для создания в Learning at Work пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Learning at Work.

**Чтобы настроить единый вход Azure AD в Learning at Work, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Learning at Work** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **How would you like users to sign on to Learning at Work** (Как пользователи должны входить в Learning at Work?) выберите **Azure AD Single Sign-On** (Единый вход Azure AD) и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_03.png)
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_04.png)
   
    а. В текстовом поле **URL-адрес входа** введите URL-адрес, при помощи которого пользователи входят в приложение Learning at Work, в следующем формате: `https://<company name>.sabacloud.com/Saba/Web/<company code>`
   
    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: "https://<имя\_компании>.sabacloud.com/Saba/SAML/sso/alias/<имя\_компании>".
   
    3\. Нажмите кнопку **Далее**.
4. На странице **Configure single sign-on at Learning at Work** (Настройка единого входа в Learning at Work) выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_05.png)
   
    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Learning at Work (Saba Cloud) и предоставьте следующие сведения:
   
    • скачанный файл метаданных.
   
    • **URL-адрес издателя**;
   
    • **URL-адрес единого входа SAML**;
   
    • **URL-адрес службы единого выхода**.
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы вывести на экран список пользователей, щелкните **Пользователи** в меню вверху.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_04.png)
5. На диалоговой странице **Тип учетной записи пользователя** выполните следующие действия:  
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На диалоговой странице **Профиль пользователя** выполните следующие действия: ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На диалоговой странице **Получение временного пароля** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_07.png)
8. На диалоговой странице **Получение временного пароля** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя Learning at Work
В этом разделе описано, как создать пользователя Britta Simon в приложении Learning at Work. Обратитесь в службу поддержки Learning at Work, чтобы добавить пользователей на платформу Learning at Work.

### Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Learning at Work.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Learning at Work, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Learning at Work**.
   
    ![Настройка единого входа](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Learning at Work на панели доступа, вы автоматически войдете в приложение Learning at Work.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_205.png


<!----HONumber=AcomDC_0720_2016-->
