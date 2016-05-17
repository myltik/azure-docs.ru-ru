<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Pluralsight | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Pluralsight."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Pluralsight

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с Pluralsight.

Интеграция Pluralsight с Azure AD обеспечивает приведенные далее преимущества.

- С помощью Azure AD вы можете контролировать доступ к Pluralsight.
- Вы можете включить автоматический вход пользователей в Pluralsight (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Pluralsight, вам потребуется:

- Подписка Azure
- подписка Pluralsight с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Pluralsight из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Pluralsight из коллекции
Чтобы настроить интеграцию Pluralsight с Azure AD, необходимо добавить Pluralsight из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pluralsight из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.
 
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Pluralsight**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)

7. В области результатов выберите **Pluralsight** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Pluralsight с использованием тестового пользователя Britta Simon.

Чтобы настроить и проверить единый вход Azure AD в Pluralsight, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Pluralsight](#creating-a-pluralsight-test-user)** требуется для создания пользователя Britta Simon в Pluralsight, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении Pluralsight.

Для приложения Pluralsight проверочные утверждения SAML должны иметь определенный формат. Для этого необходимо добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов маркера SAML. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png)

Также можно добавить атрибут **Уникальный идентификатор** с соответствующим значением, таким как EmployeeID или другим, которое подходит для вашей организации. Обратите внимание, что это необязательный атрибут, однако его можно добавить для идентификации уникального пользователя.

**Чтобы настроить единый вход Azure AD в Pluralsight, выполните следующие действия:**

1. На странице интеграции с приложением **Pluralsight** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.

	![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png)



1. Чтобы удалить лишние **атрибуты маркера SAML**, сделайте следующее.

	![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/2829.png)


    а. Для каждого атрибута пользователя в красной рамке в приведенной выше таблице наведите указатель мыши на атрибут и щелкните "Удалить".




1. Чтобы добавить требуемые **атрибуты маркера SAML**, для каждой строки в таблице ниже выполните следующие действия.

	| Имя атрибута | Значение атрибута |
	| --- | --- |    
	| Имя| user.givenname |
    | Фамилия | user.surname |
	| Email | user.mail |

	а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавление атрибута пользователя**.

	![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png)


	b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.

    г) Нажмите **Завершено**.
	


1. Нажмите кнопку **Применить изменения**.

	![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/3232.png)



1. В верхнем меню щелкните **Быстрый запуск**.

	![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)









1. На классическом портале Azure на странице интеграции с приложением **Pluralsight** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Pluralsight?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png)

3. На странице с диалоговым окном **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png)


    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение Pluralsight по следующей схеме: `https://<instance name>.pluralsight.com/sso/<comapny name>`.

    b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в Pluralsight** выполните указанные ниже действия. ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для своего приложения, свяжитесь с представителем отдела [профессиональных услуг](mailTo:professionalservices@pluralsight.com) Pluralsight и передайте ему скачанный файл метаданных.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
  
    ![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
 
    ![Единый вход в Azure AD][11]



### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы вывести на экран список пользователей, щелкните **Пользователи** в меню вверху.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png)

5. На диалоговой странице **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получение временного пароля** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png)

8. На диалоговой странице **Получение временного пароля** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Pluralsight

Цель этого раздела — создать пользователя с именем Britta Simon в Pluralsight. Чтобы добавить пользователей в учетную запись Pluralsight, обратитесь в службу поддержки Pluralsight.


> [AZURE.NOTE] Если вам нужно создать пользователя вручную, необходимо обратиться в службу поддержки Pluralsight.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Pluralsight.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Pluralsight, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Pluralsight**.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Pluralsight на панели доступа, вы автоматически войдете в приложение Pluralsight.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0504_2016-->