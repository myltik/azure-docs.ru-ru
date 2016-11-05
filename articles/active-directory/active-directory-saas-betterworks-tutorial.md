---
title: Учебник. Интеграция Azure Active Directory с BetterWorks | Microsoft Docs
description: Узнайте, как настроить единый вход между Azure Active Directory и BetterWorks.
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
ms.date: 07/11/2016
ms.author: jeedes

---
# Учебник. Интеграция Azure Active Directory с BetterWorks
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением BetterWorks.

Интеграция Azure AD с BetterWorks обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к BetterWorks.
* Вы можете включить автоматический вход пользователей в BetterWorks (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с BetterWorks, вам потребуется:

* подписка Azure AD;
* подписка BetterWorks с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление BetterWorks из коллекции
2. Настройка и проверка единого входа в Azure AD

## Добавление BetterWorks из коллекции
Чтобы настроить интеграцию BetterWorks с Azure AD, необходимо добавить BetterWorks из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BetterWorks из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **BetterWorks**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)
7. В области результатов выберите **BetterWorks** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

## Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в BetterWorks с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BetterWorks соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BetterWorks.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в BetterWorks.

Чтобы настроить и проверить единый вход Azure AD в BetterWorks, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BetterWorks](#creating-a-betterworks-test-user)** требуется для создания пользователя Britta Simon в BetterWorks, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение BetterWorks.

Приложение BetterWorks ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке "**Атрибут**" приложения. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Чтобы настроить единый вход Azure AD в BetterWorks, выполните следующие действия.**

1. На странице интеграции с приложением **BetterWorks** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)
2. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | saml\_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)

    b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение атрибута** введите идентификатор токена SAML, отображаемый для этой строки.

    г) Нажмите кнопку **Завершить**.

1. В верхнем меню щелкните **Быстрый запуск**.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png)
2. На странице **Как пользователи должны входить в BetterWorks?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, на диалоговой странице **Настроить параметры приложения** выполните указанные ниже действия.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)

    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://app.betterworks.com/saml2/metadata/`.


    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://app.betterworks.com/saml2/acs/`.


    c. Нажмите кнопку **Далее**.

1. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, на диалоговой странице **Настроить параметры приложения** выполните указанные ниже действия.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)
   
    а. Выберите **Показать дополнительные параметры (необязательно)**.

    b. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение BetterWorks, в следующем формате: `https://app.betterworks.com`.

    b. Нажмите кнопку **Далее**.

1. На странице **Настройка единого входа в BetterWorks** выполните указанные ниже действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)
   
    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки BetterWorks по адресу <mailto:support@betterworks.com>. Присоедините к сообщению скачанный файл метаданных, чтобы специалисты BetterWorks смогли настроить единый вход со своей стороны.
3. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
   ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу щелкните **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)
5. На диалоговой странице **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На диалоговой странице **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На диалоговой странице **Получить временный пароль** щелкните **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)
8. На диалоговой странице **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя BetterWorks
В этом разделе описано, как создать пользователя Britta Simon в приложении BetterWorks.

Обратитесь в службу поддержки BetterWorks по адресу <mailto:support@betterworks.com>, чтобы добавить пользователей в платформу BetterWorks.

### Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к BetterWorks.

   ![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в BetterWorks, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **BetterWorks**.
   
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. Из списка пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент BetterWorks на панели доступа, вы автоматически войдете в приложение BetterWorks.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->