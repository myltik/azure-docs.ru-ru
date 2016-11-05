---
title: Руководство. Интеграция Azure Active Directory с Predictix Assortment Planning | Microsoft Docs
description: Узнайте, как настроить единый вход между Azure Active Directory и Predictix Assortment Planning.
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
ms.date: 07/26/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с Predictix Assortment Planning
В этом руководстве описано, как интегрировать Predictix Assortment Planning с Azure Active Directory (Azure AD).

Интеграция Azure AD с Predictix Assortment Planning дает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Predictix Assortment Planning.
* Вы можете включить автоматический вход пользователей в Predictix Assortment Planning (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с Predictix Assortment Planning, вам потребуется:

* подписка Azure AD;
* подписка на Predictix Assortment Planning с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
Цель этого руководства — показать, как выполнить проверку единого входа Microsoft Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Predictix Assortment Planning из коллекции
2. Настройка и проверка единого входа в Microsoft Azure AD

## Добавление Predictix Assortment Planning из коллекции
Чтобы настроить интеграцию Predictix Assortment Planning с Azure AD, необходимо добавить приложение Predictix Assortment Planning из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Predictix Assortment Planning из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Predictix Assortment Planning**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)
7. В области результатов выберите **Predictix Assortment Planning** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)

## Настройка и проверка единого входа в Microsoft Azure AD
В этом разделе описана настройка и проверка единого входа Microsoft Azure AD в Predictix Assortment Planning с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Predictix Assortment Planning соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Predictix Assortment Planning.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Predictix Assortment Planning.

Чтобы настроить и проверить единый вход Microsoft Azure AD в Predictix Assortment Planning, выполните следующие стандартные действия.

1. **[Настройка единого входа Microsoft Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Microsoft Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Predictix Assortment Planning](#creating-a-predictix-price-reporting-test-user)** требуется для создания в Predictix Assortment Planning пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Microsoft Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа Microsoft Azure AD
В этом разделе описано, как включить единый вход Microsoft Azure AD на классическом портале и настроить его в приложении Predictix Assortment Planning.

**Чтобы настроить единый вход Microsoft Azure AD в Predictix Assortment Planning, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Predictix Assortment Planning** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в Predictix Assortment Planning** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png)
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png)
   
    а. В текстовом поле **URL-адрес входа** введите URL-адрес, применяемый пользователями для входа в приложение Predictix Assortment Planning, в следующем формате: **https://\<имя\_компании-pricing>.ap.predictix.com/sso/request**.
   
    b. Нажмите кнопку **Далее**.
4. На странице **Configure single sign-on at Predictix Assortment Planning** (Настройка единого входа в Predictix Assortment Planning) выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)
   
    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Для настройки единого входа для своего приложения обратитесь в службу поддержки Predictix Assortment Planning и предоставьте следующие сведения:
   
    • скачанный сертификат;
   
    • **идентификатор сущности**;
   
    • **URL-адрес единого входа SAML**;
   
    • **URL-адрес службы единого выхода**.
6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя Predictix Assortment Planning
В этом разделе описано, как создать пользователя Britta Simon в приложении Predictix Assortment Planning. Чтобы добавить пользователей на платформу Predictix Assortment Planning, обратитесь в службу поддержки Predictix Assortment Planning.

### Назначение тестового пользователя Azure AD
В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Predictix Assortment Planning.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению Predictix Assortment Planning, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Predictix Assortment Planning**.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Microsoft Azure AD с помощью панели доступа.

Щелкнув элемент Predictix Assortment Planning на панели доступа, вы автоматически войдете в приложение Predictix Assortment Planning.

## дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0824_2016-->