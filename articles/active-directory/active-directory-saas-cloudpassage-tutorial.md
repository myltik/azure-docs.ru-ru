<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с CloudPassage | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и CloudPassage."
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


# Учебник. Интеграция Azure Active Directory с CloudPassage

Цель этого учебника — показать, как интегрировать CloudPassage с Azure Active Directory (Azure AD).<br>Интеграция CloudPassage с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к CloudPassage. 
- Вы можете включить автоматический вход пользователей в CloudPassage (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через Azure Active Directory. 
- 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования 

Чтобы настроить интеграцию Azure AD с CloudPassage, вам потребуется следующее:

- подписка Azure AD;
- подписка с поддержкой единого входа CloudPassage.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- При отсутствии тестовой среды Azure AD [здесь](https://azure.microsoft.com/pricing/free-trial/) вы можете получить бесплатную пробную подписку Azure сроком на один месяц. 

 
## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. <br> Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление CloudPassage из коллекции 
2. Настройка и проверка единого входа в Azure AD


## Добавление CloudPassage из коллекции
Чтобы настроить интеграцию CloudPassage с Azure AD, вам потребуется добавить CloudPassage из коллекции в список управляемых приложений SaaS.

### Чтобы добавить CloudPassage из коллекции, выполните следующие действия:

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. <br><br> ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.<br><br> ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.<br><br> ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.<br><br> ![Приложения][4]
6. В поле поиска введите **CloudPassage**.<br><br> ![Приложения][5]
7. В области результатов выберите **CloudPassage** и нажмите кнопку **Завершить**, чтобы добавить приложение.<br><br> ![Приложения][6]



##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD в CloudPassage с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в CloudPassage соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в CloudPassage.<br> Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в CloudPassage.
 
Чтобы настроить и проверить единый вход Azure AD в CloudPassage, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя CloudPassage](#creating-a-halogen-software-test-user)** требуется для создания соответствующего пользователя Britta Simon в CloudPassage, связанного с ее представлением в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение CloudPassage.<br> Приложение CloudPassage ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. <br><br> ![Настройка единого входа][21]

**Чтобы настроить единый вход Azure AD в CloudPassage, выполните следующие действия:**

1. На странице интеграции с приложением **CloudPassage** классического портала Azure AD нажмите **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.<br><br> ![Настройка единого входа][7]

2. На странице **Как пользователи должны входить в CloudPassage?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.<br><br> ![Настройка единого входа][8]

3. На диалоговой странице **Настройка параметров приложения** выполните следующие действия: <br><br>![Настройка параметров приложения][9]
 
     3\.1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение CloudPassage (например, **https://portal.cloudpassage.com/saml/init/accountid*).

     3\.2. В текстовом поле «URL-адрес ответа» введите URL-адрес службы AssertionConsumerService например: **https://portal.cloudpassage.com/saml/consume/accountid*). <br> Значение этого атрибута можно получить, щелкнув **SSO Setup documentation** (Документация по настройке единого входа) в разделе **Single Sign-on Settings** (Параметры единого входа) на портале CloudPassage. <br><br>![Настройка единого входа][10]

     3\.3. Нажмите кнопку **Далее**.



4. На странице **Настройка единого входа в CloudPassage** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата на локальном компьютере. <br><br>![Настройка единого входа][11]

5. В другом окне браузера войдите на сайт CloudPassage компании в качестве администратора.

6. В верхнем меню щелкните **Параметры**, а затем выберите **Администрирование сайта**. <br><br> ![Настройка единого входа][12]

7. Выберите вкладку **Параметры проверки подлинности**. <br><br> ![Настройка единого входа][13]


8. В разделе **Параметры единого входа** выполните следующие действия: <br><br> ![Настройка единого входа][14]


     8\.1. На диалоговой странице **Настройка единого входа в CloudPassage** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **URL-адрес издателя SAML**.

     8\.2. На диалоговой странице **Настройка единого входа в CloudPassage** классического портала Azure скопируйте значение поля **Конечная точка, инициированная поставщиком услуг** и вставьте его в текстовое поле **URL-адрес конечной точки SAML**.

     8\.3. На диалоговой странице **Настройка единого входа в CloudPassage** классического портала Azure скопируйте значение поля **URL-адрес выхода** и вставьте его в текстовое поле **Целевая страница выхода**.

     8\.4. Создайте файл в кодировке **Base-64** из скачанного сертификата.
          
      >[AZURE.TIP] Дополнительные сведения см. в разделе [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

     8\.5. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509**.

     8\.6. Щелкните **Сохранить**.


9. На классическом портале Azure AD выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. <br><br> ![Настройка единого входа][15]


10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br> ![Настройка единого входа][16]



11. В меню вверху щелкните **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты маркера SAML**. <br><br> ![Настройка единого входа][17]

12. Чтобы добавить необходимые атрибуты пользователя, для каждой строки в таблице ниже выполните следующие действия: <br>

| Имя атрибута | Значение атрибута |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. На панели инструментов внизу щелкните **Применить изменения**. <br><br> ![Настройка единого входа][20]



### Создание тестового пользователя Azure AD

Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.<br><br> Из списка пользователей выберите **Britta Simon**.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.<br> ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, щелкните **Пользователи** в верхнем меню.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, нажмите кнопку **Добавить пользователя** на панели инструментов внизу. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. На диалоговой странице **Расскажите об этом пользователе** выполните следующие действия. <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку Далее.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия: <br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. Из списка **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.

7. На диалоговой странице **Получить временный пароль** нажмите кнопку **Создать**.<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. На диалоговой странице **Получение временного пароля** выполните следующие действия:<br>![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   


  
 
### Создание тестового пользователя CloudPassage

Цель этого раздела — создать пользователя с именем Britta Simon в CloudPassage.

#### Чтобы создать пользователя с именем Britta Simon в CloudPassage, выполните следующие действия:

1.	Выполните вход на сайт **CloudPassage** компании в качестве администратора. 

2.	В верхнем меню щелкните **Параметры**, а затем выберите **Администрирование сайта**. <br>![Создание тестового пользователя CloudPassage][22]

3.	Откройте вкладку **Пользователи**, а затем щелкните **Добавить нового пользователя**. <br>![Создание тестового пользователя CloudPassage][23]
	
4.	В разделе **Добавить нового пользователя** выполните следующие действия: <br>![Создание тестового пользователя CloudPassage][24]

     4\.1. В текстовом поле **Имя** введите Britta.

     4\.2. В текстовом поле **Фамилия** введите Simon.

     4\.3. В текстовых полях **Пользователь**, **Электронная почта** и **Введите адрес еще раз** введите имя пользователя Britta в Azure AD.

     4\.4. Для параметра **Тип доступа** выберите **Включить доступ к порталу Halo**.

     4\.5. Щелкните **Добавить**.










### Назначение тестового пользователя Azure AD

Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к CloudPassage. <br><br>![Назначение пользователя][30]

**Чтобы назначить Britta Simon в CloudPassage, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню.<br> <br><br>![Назначение пользователя][26]
2. Из списка приложений выберите **CloudPassage**. <br><br>![Назначение пользователя][27]
1. В верхнем меню щелкните **Пользователи**.<br> <br><br>![Назначение пользователя][25]
1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**. <br><br>![Назначение пользователя][29]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.<br> Щелкнув элемент CloudPassage на панели доступа, вы автоматически войдете в приложение CloudPassage.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=AcomDC_0128_2016-->