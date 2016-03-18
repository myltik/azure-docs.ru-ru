<properties
	pageTitle="Руководство по интеграции Azure Active Directory с PerformanceCentre | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в PerformanceCentre."
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
	ms.date="01/26/2016"
	ms.author="jeedes"/>


# Руководство по интеграции Azure Active Directory с PerformanceCentre

Цель этого руководства — показать, как интегрировать PerformanceCentre с Azure Active Directory (Azure AD).<br>Интеграция PerformanceCentre с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к PerformanceCentre. 
- Вы можете включить автоматический вход пользователей в PerformanceCentre (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с PerformanceCentre, вам потребуется следующее:

- подписка Azure AD;
- подписка PerformanceCentre с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из следующих основных блоков:

1. Добавление PerformanceCentre из коллекции. 
2. Настройка и проверка единого входа в Azure AD


## Добавление PerformanceCentre из коллекции
Чтобы настроить интеграцию PerformanceCentre с Azure AD, вам потребуется добавить PerformanceCentre из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PerformanceCentre из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога в меню вверху щелкните пункт **Приложения**.<br><br> ![Приложения][2]<br>
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]<br>
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]<br>
6. В поле поиска введите **PerformanceCentre**.<br><br> ![Приложения][5]<br>
7. В области результатов выберите **PerformanceCentre** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Приложения][500]<br>


##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в PerformanceCentre с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в PerformanceCentre соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PerformanceCentre.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PerformanceCentre.
 
Чтобы настроить и проверить единый вход в Azure AD в PerformanceCentre, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя PerformanceCentre](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в PerformanceCentre, связанного с представлением этого же пользователя в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении PerformanceCentre.<br>

**Чтобы настроить единый вход Azure AD в PerformanceCentre, выполните следующие действия.**

1. На классическом портале Azure AD на странице интеграции с приложением **PerformanceCentre** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**. <br><br> ![Настройка единого входа][6] <br>

2. На странице **Как пользователи должны входить в PerformanceCentre?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**. <br><br> ![Единый вход в Azure AD][7] <br>

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. <br><br>![Единый вход в Azure AD][8] <br>
 
     а. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт PerformanceCentre (например, **http://companyname.performancecentre.com/saml/SSO*).
 
     b. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в PerformanceCentre** выполните следующие действия. <br><br>![Единый вход в Azure AD][9] <br>

    а. Нажмите **Загрузить метаданные** и сохраните файл сертификата на свой компьютер.



1. Войдите на корпоративный сайт **PerformanceCentre** с правами администратора.

2. На вкладке слева щелкните **Configure** (Настройка). <br><br>![Единый вход в Azure AD][10]

2. На вкладке слева щелкните **Miscellaneous** (Разное), а затем — **Single Sign On** (Единый вход). <br><br>![Единый вход в Azure AD][11]

2. В поле **Protocol** (Протокол) выберите **SAML**. <br><br>![Единый вход в Azure AD][12]

2. Откройте загруженный файл метаданных, скопируйте и вставьте его содержимое в текстовое поле **Identity Provider Metadata** (Метаданные поставщика удостоверений), а затем нажмите кнопку **Save** (Сохранить). <br><br>![Единый вход в Azure AD][13]

2. Проверьте правильность значений в полях **Entity Base URL** (Базовый URL-адрес сущности) и **Entity ID URL** (URL-адрес идентификатора сущности). <br><br>![Единый вход в Azure AD][14]


6. На классическом портале Azure AD выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br>![Единый вход в Azure AD][15]<br>

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Единый вход в Azure AD][16]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br> Из списка пользователей выберите **Britta Simon**.<br><br>![Создание пользователя Azure AD][20]<br>

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в меню вверху. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br>

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На диалоговой странице **Профиль пользователя** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**. e. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**. <br><br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. На диалоговой странице **Получить временный пароль** выполните следующие действия. <br><br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

  
 
### Создание тестового пользователя PerformanceCentre

Цель этого раздела — создать пользователя с именем Britta Simon в PerformanceCentre.

**Чтобы создать пользователя с именем Britta Simon в PerformanceCentre, выполните следующие действия.**

1. Войдите на корпоративный сайт PerformanceCentre с правами администратора.

2. В меню слева выберите пункт **Interrelate** (Установить взаимосвязь), а затем нажмите кнопку **Create Participant** (Создать участника). <br><br>![Создать пользователя][400]<br>

4. В диалоговом окне **Interrelate — Create Participant** (Взаимосвязь — создание участника) выполните следующие действия. <br><br>![Создание пользователя][401]<br>

    а. Введите необходимые атрибуты для пользователя Britta Simon в соответствующие текстовые поля.
    
    > [AZURE.IMPORTANT] Атрибут User Name этого пользователя в PerformanceCentre должен соответствовать имени пользователя в Azure AD.


    b. Выберите значение **Администратор клиента** в поле **Выберите роль**.

    c. Щелкните **Сохранить**.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к PerformanceCentre. <br><br>![Назначение пользователя][200] <br>

**Чтобы назначить Britta Simon в PerformanceCentre, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure нажмите **Приложения** в верхнем меню. <br><br>![Назначение пользователя][201] <br>
2. В списке приложений выберите **PerformanceCentre**. <br><br>![Назначение пользователя][202] <br>
1. В меню вверху щелкните пункт **Пользователи**. <br><br>![Назначение пользователя][203] <br>
1. Из списка пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент PerformanceCentre на панели доступа, вы автоматически войдете в приложение PerformanceCentre.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png

<!---HONumber=AcomDC_0128_2016-->