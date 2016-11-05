---
title: Учебник. Интеграция Azure Active Directory с Blackboard Learn | Microsoft Docs
description: Узнайте, как настроить единый вход Azure Active Directory в Blackboard Learn.
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
# Учебник. Интеграция Azure Active Directory с Blackboard Learn
В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением Blackboard Learn.

Интеграция Blackboard Learn с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Blackboard Learn.
* Вы можете включить автоматический вход пользователей в Blackboard Learn (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с Blackboard Learn, вам потребуется:

* подписка Azure AD;
* подписка на облачную платформу Blackboard Learn с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Blackboard Learn из коллекции
2. Настройка и проверка единого входа в Azure AD

## Добавление Blackboard Learn из коллекции
Чтобы настроить интеграцию Blackboard Learn с Azure AD, необходимо добавить Blackboard Learn из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Blackboard Learn из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Blackboard Learn**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_01.png)
7. В области результатов выберите **Blackboard Learn** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_06.png)

## Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Blackboard Learn с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Blackboard Learn соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Blackboard Learn.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Blackboard Learn.

Чтобы настроить и проверить единый вход Azure AD в Blackboard Learn, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Blackboard Learn](#creating-a-blackboard-learn-test-user)** требуется для создания в Blackboard Learn пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Blackboard Learn.

Приложение Blackboard Learn ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке**"Атрибут"** приложения. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_51.png)

**Чтобы настроить единый вход Azure AD в Blackboard Learn, выполните следующие действия.**

1. На странице интеграции с приложением **Blackboard Learn** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.
   
    ![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_80.png)
2. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия: здесь мы сопоставили Userprincipalname как уникальный атрибут пользователя, но вы можете сопоставить его с соответствующим значением, которое однозначно различает пользователя в организации и сопоставляется с полем имени пользователя Blackboard Learn.
   
   | Имя атрибута | Значение атрибута |
   | --- | --- |
   | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |user.userprincipalname |

    а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.

    ![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_81.png)


    b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.

    г) Нажмите **Завершено**.

1. На классическом портале Azure на странице интеграции с приложением **Blackboard Learn** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в Blackboard Learn?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_03.png)
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_04.png)
   
    а. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение Blackboard Learn, в формате **https://\<имя\_компании-цены>.blackboard.com/**.
   
    b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Blackboard Learn** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_05.png)
   
    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Для настройки единого входа для своего приложения обратитесь в службу поддержки Blackboard Learn и предоставьте следующие сведения:
   
    • скачанный файл метаданных.
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы вывести на экран список пользователей, щелкните **Пользователи** в меню вверху.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_04.png)
5. На диалоговой странице **Тип учетной записи пользователя** выполните указанные ниже действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На диалоговой странице **Профиль пользователя** выполните указанные ниже действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На диалоговой странице **Получить временный пароль** щелкните **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_07.png)
8. На диалоговой странице **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя Blackboard Learn
В этом разделе описано, как создать пользователя Britta Simon в приложении Blackboard Learn.

Приложение Blackboard Learn поддерживает JIT-подготовку пользователей. Убедитесь, что утверждения настроены, как описано в разделе **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)**.

### Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Blackboard Learn.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Blackboard Learn, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Blackboard Learn**.
   
    ![Настройка единого входа](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Поддержка приложения Blackboard Learn. Щелкнув элемент Blackboard Learn на панели доступа, вы автоматически войдете в приложение Blackboard Learn.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0713_2016-->