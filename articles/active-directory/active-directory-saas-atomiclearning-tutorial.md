---
title: Руководство. Интеграция Azure Active Directory с Atomic Learning | Microsoft Docs
description: Узнайте, как настроить единый вход Azure Active Directory в приложение Atomic Learning.
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
ms.date: 08/31/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с Atomic Learning
В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением Atomic Learning.

Интеграция приложения Atomic Learning с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Atomic Learning.
* Вы можете включить автоматический вход пользователей в Atomic Learning (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с Atomic Learning, вам потребуется:

* подписка Azure AD;
* подписка на **Atomic Learning** с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Atomic Learning из коллекции
2. Настройка и проверка единого входа в Azure AD

## Добавление Atomic Learning из коллекции
Чтобы настроить интеграцию Atomic Learning с Azure AD, необходимо добавить Atomic Learning из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Atomic Learning из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Atomic Learning**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/tutorial_atomiclearning_01.png)
7. В области результатов выберите **Atomic Learning** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/tutorial_atomiclearning_02.png)

## Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Atomic Learning с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Atomic Learning соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Atomic Learning. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Atomic Learning.

Чтобы настроить и проверить единый вход Azure AD в Atomic Learning, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Atomic Learning](#creating-an-atomic-learning-test-user)** требуется для создания в Atomic Learning пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Atomic Learning.

**Чтобы настроить единый вход Azure AD в Atomic Learning, сделайте следующее:**

1. В верхнем меню щелкните **Быстрый запуск**.
   
    ![Настройка единого входа][6]
2. На классическом портале Azure на странице интеграции с приложением **Atomic Learning** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7]
3. На странице **Как пользователи должны входить в Atomic Learning?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-atomiclearning-tutorial/tutorial_atomiclearning_06.png)
4. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-atomiclearning-tutorial/tutorial_atomiclearning_07.png)

    а. В текстовое поле "URL-адрес для входа" введите URL-адрес в следующем формате: `https://secure2.atomiclearning.com/sso/shibboleth/<companyname>`.

    b. Нажмите кнопку **Далее**.

1. На странице **Настройка единого входа в Atomic Learning** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на свой компьютер.
   
    ![Настройка единого входа](./media/active-directory-saas-atomiclearning-tutorial/tutorial_atomiclearning_08.png)
2. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Atomic Learning. Они помогут выбрать правильный канал для настройки единого входа. Обратите внимание, что необходимо будет отправить по электронной почте сообщение, прикрепив к нему скачанный файл метаданных <mailto:cs@atomiclearning.com>.
3. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atomiclearning-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя Atomic Learning
В этом разделе описано, как создать пользователя Britta Simon в приложении Atomic Learning. Приложение Atomic Learning поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Atomic Learning (если он еще не создан). Для этого будет использоваться адрес электронной почты.

### Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Atomic Learning.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Atomic Learning, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Atomic Learning**.
   
    ![Настройка единого входа](./media/active-directory-saas-atomiclearning-tutorial/tutorial_atomiclearning_09.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке "Все пользователи" выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Atomic Learning на панели доступа, вы автоматически войдете в приложение Atomic Learning.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atomiclearning-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->