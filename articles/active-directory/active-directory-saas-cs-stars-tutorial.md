<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с CS Stars | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и CS Stars."
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
	ms.date="02/29/2016"
	ms.author="jeedes"/>


# Руководство. Интеграция Azure Active Directory с CS Stars

Цель этого учебника — показать, как интегрировать CS Stars с Azure Active Directory (Azure AD).<br>Интеграция CS Stars с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к CS Stars. 
- Вы можете включить автоматический вход пользователей в CS Stars (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с CS Stars, вам потребуется:

- подписка Azure AD;
- подписка на CS Stars с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление CS Stars из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление CS Stars из коллекции
Чтобы настроить интеграцию CS Stars с Azure AD, необходимо добавить CS Stars из коллекции в список управляемых приложений SaaS.

**Чтобы добавить CS Stars из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в верхнем меню представления каталога щелкните **Приложения**. <br><br>![Приложения][2]<br>

4. В нижней части страницы нажмите кнопку **Добавить**. <br><br>![Приложения][3]<br>

5. В диалоговом окне **Что необходимо сделать** выберите **Добавить приложение из коллекции**. <br><br>![Приложения][4]<br>

6. В поле поиска введите **CS Stars**. <br><br>![Приложения][5]<br>

7. В области результатов выберите **CS Stars** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Приложения][400]<br>



##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в CS Stars с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в CS Stars соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в CS Stars.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в CS Stars.
 
Чтобы настроить и проверить единый вход Azure AD в CS Stars, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя CS Stars](#creating-a-cs-stars-test-user)** требуется для создания пользователя Britta Simon в CS Stars, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение CS Stars.<br>

**Чтобы настроить единый вход Azure AD в CS Stars, выполните следующие действия:**

1. На странице интеграции с приложением **CS Stars** классического портала Azure AD щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br>![Настройка единого входа][6]<br>

2. На странице **Как пользователи должны входить в CS Stars?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][7]<br>

3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия: <br><br>![Настройка параметров приложения][8]<br>
 
     3\.1. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение CS Stars (например, **https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2*).

     > [AZURE.NOTE] Если вы не знаете, какое значение использовать, обратитесь к представителю Marsh ClearSight.

     3\.2. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в CS Stars** нажмите кнопку **Загрузить метаданные**, а затем сохраните файл метаданных на локальном компьютере. <br><br>![Что такое Azure AD Connect?][9]<br>

5. Чтобы включить единый вход для CS Stars, обратитесь к представителю Marsh ClearSight и передайте ему файл метаданных.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Что такое Azure AD Connect?][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Что такое Azure AD Connect?][11]<br>




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> В списке пользователей выберите **Britta Simon**. <br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, нажмите **Пользователи** в верхнем меню. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, нажмите кнопку **Добавить пользователя** на панели инструментов внизу. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br>
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку Далее.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия: <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br>
  1. В текстовое поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. Из списка **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получение временного пароля** нажмите кнопку **Создать**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br>
 
8. На странице диалогового окна **Получение временного пароля** выполните следующие действия: <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br>
  1. Запишите значение в поле **Новый пароль**.
  2. Нажмите **Завершено**.   

  
 
### Создание тестового пользователя CS Stars

Цель этого раздела — создать пользователя с именем Britta Simon в CS Stars.

**Чтобы создать пользователя с именем Britta Simon в CS Stars, выполните следующие действия:**

1. Обратитесь к представителю Marsh ClearSight.


### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход в Azure, предоставив ему доступ к CS Stars. <br><br>![Назначение пользователя][200]<br>

**Чтобы назначить пользователя Britta Simon в CS Stars, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201]<br>
2. В списке приложений выберите **CS Stars**. <br><br>![Назначение пользователя][202]<br>
1. В меню вверху нажмите **Пользователи**.<br> <br><br>![Назначение пользователя][203]<br>
1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу нажмите **Назначить**. <br><br>![Назначение пользователя][205]<br>



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа в Azure AD с помощью панели доступа.<br> Щелкнув элемент CS Stars на панели доступа, вы автоматически войдете в приложение CS Stars.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png

<!---HONumber=AcomDC_0302_2016-->