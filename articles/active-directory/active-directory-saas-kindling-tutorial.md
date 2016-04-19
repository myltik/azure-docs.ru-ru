<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Kindling | Microsoft Azure"
	description="Сведения о настройке единого входа Azure Active Directory в Kindling."
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
	ms.date="04/05/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Kindling

Цель этого руководства — показать, как интегрировать Kindling с Azure Active Directory (Azure AD).<br>Интеграция Kindling с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к Kindling; 
- вы можете включить автоматический вход пользователей в Kindling (единый вход) с использованием учетных записей Azure AD;
- Вы можете управлять учетными записями централизованно — через классический портал Azure. 


Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с Kindling, вам потребуется:

- подписка Azure AD;
- Подписка Kindling


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Kindling из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление Kindling из коллекции
Чтобы настроить интеграцию Kindling с Azure AD, необходимо добавить Kindling из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kindling из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **Kindling**.<br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_01.png)<br>
7. В области результатов выберите **Kindling** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_02.png)<br>

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Kindling с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Kindling соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kindling.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **Username** в Kindling.
 
Чтобы настроить и проверить единый вход Azure AD в Kindling, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Kindling](#creating-a-kindling-test-user)** требуется для создания пользователя Britta Simon в Kindling, связанного с соответствующим представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
6. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Kindling. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

Для настройки единого входа в приложение Kindling вам потребуется зарегистрированный домен. Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Kindling по адресу [support@kindlingapp.com](mailto:support@kindlingapp.com).



**Чтобы настроить единый вход Azure AD в Kindling, выполните следующие действия.**

1. На странице интеграции с приложением **Kindling** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в Kindling?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_03.png) <br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_04.png) <br>


    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Kindling (например, *https://\<название\_компании>.kindlingapp.com/*).

    b. Чтобы получить значения **Издатель** и **URL-адрес ответа**, обратитесь в службу поддержки Kindling по адресу [support@kindlingapp.com](mailto:support@kindlingapp.com).

    c. В текстовом поле **Издатель** введите URL-адрес издателя.

    г) В текстовом поле **URL-адрес ответа** введите ваш URL-адрес ответа.
 
    д. Нажмите кнопку **Далее**.
 
 
4. На странице **Настройка единого входа в Kindling** выполните следующие действия. <br><br>![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_05.png) <br>

    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.

    b. Обратитесь в службу поддержки Kindling и попросите их настроить единый вход для приложения. Службе поддержки потребуется предоставить загруженный сертификат, значение **URL-адрес издателя**, соответствующее **идентификатору сущности** Kindling, **URL-адрес службы единого входа**, соответствующий **URL-адресу единого входа** Kindling, и **URL-адрес службы единого выхода**, соответствующий **URL-адресу единого выхода** Kindling.

    c. Нажмите кнопку **Далее**.

6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][10]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_07.png) <br>
 
8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя Kindling

Цель этого раздела — создать пользователя с именем Britta Simon в Kindling. Приложение Kindling поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа Azure AD](#configuring-azure-ad-single-single-sign-on).

В этом разделе никакие действия с вашей стороны не требуются.




### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Kindling. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить пользователя Britta Simon приложению Kindling, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>

2. В списке приложений выберите **Kindling**. <br><br>![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_50.png) <br>

1. В меню вверху щелкните пункт **Пользователи**. <br><br>![Назначение пользователя][203] <br>

1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув плитку Kindling на панели доступа, вы автоматически войдете в приложение Kindling.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0406_2016-->