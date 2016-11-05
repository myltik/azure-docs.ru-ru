---
title: Учебник. Интеграция Azure Active Directory с CloudPassage | Microsoft Docs
description: Узнайте, как настроить единый вход между Azure Active Directory и CloudPassage.
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
ms.date: 10/07/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-cloudpassage"></a>Учебник. Интеграция Azure Active Directory с CloudPassage
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением CloudPassage.  
Интеграция приложения CloudPassage с Azure AD обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к CloudPassage. 
* Вы можете включить автоматический вход пользователей в CloudPassage (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через Azure Active Directory. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с CloudPassage, вам потребуется следующее:

* подписка Azure AD;
* подписка с поддержкой единого входа CloudPassage.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* При отсутствии тестовой среды Azure AD [здесь](https://azure.microsoft.com/pricing/free-trial/)вы можете получить бесплатную пробную подписку Azure сроком на один месяц. 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление CloudPassage из коллекции 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-cloudpassage-from-the-gallery"></a>Добавление CloudPassage из коллекции
Чтобы настроить интеграцию CloudPassage с Azure AD, вам потребуется добавить CloudPassage из коллекции в список управляемых приложений SaaS.

### <a name="to-add-cloudpassage-from-the-gallery,-perform-the-following-steps:"></a>Чтобы добавить CloudPassage из коллекции, выполните следующие действия:
1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **CloudPassage**.
   
    ![Приложения][5]
7. В области результатов выберите **CloudPassage** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD в CloudPassage с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в CloudPassage соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в CloudPassage.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в CloudPassage.

Чтобы настроить и проверить единый вход Azure AD в CloudPassage, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя CloudPassage](#creating-a-halogen-software-test-user)** требуется для создания соответствующего пользователя Britta Simon в CloudPassage, связанного с ее представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — показать, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложение CloudPassage.  
Приложение CloudPassage ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример.

![Настройка единого входа][21]

**Чтобы настроить единый вход Azure AD в CloudPassage, выполните следующие действия:**

1. На классическом портале Azure AD на странице интеграции с приложением **CloudPassage** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7]
2. На странице **Как пользователи должны входить в CloudPassage?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа][8]
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. 
   
    ![Настройка параметров приложения][9]
   
    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение CloudPassage (например, *https://portal.cloudpassage.com/saml/init/accountid*). 
   
    b. В текстовом поле **URL-адрес ответа** введите URL-адрес службы AssertionConsumerService (например, *https://portal.cloudpassage.com/saml/consume/accountid*). Значение этого атрибута можно получить, щелкнув **SSO Setup documentation** (Документация по настройке единого входа) в разделе **Single Sign-on Settings** на портале CloudPassage.  
    ![Настройка единого входа][10]
   
    C. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в CloudPassage** щелкните **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере. 
   
    ![Настройка единого входа][11]
5. В другом окне браузера войдите на сайт CloudPassage компании в качестве администратора.
6. В верхнем меню щелкните **Параметры**, а затем выберите **Администрирование сайта**. 
   
    ![Настройка единого входа][12]
7. Выберите вкладку **Параметры проверки подлинности** . 
   
    ![Настройка единого входа][13]
8. В разделе **Параметры единого входа** сделайте следующее: 
   
    ![Настройка единого входа][14]

    а. На странице диалогового окна **Настройка единого входа в CloudPassage** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **SAML issuer URL** (URL-адрес издателя SAML).

    b. На странице диалогового окна **Настройка единого входа в CloudPassage** классического портала Azure скопируйте значение поля **Конечная точка, инициированная поставщиком услуг** и вставьте его в текстовое поле **SAML endpoint URL** (URL-адрес конечной точки SAML).

    c. На странице диалогового окна **Настройка единого входа в CloudPassage** классического портала Azure скопируйте значение поля **URL-адрес выхода** и вставьте его в текстовое поле **Logout landing page** (Целевая страница выхода).

    d. Создайте файл в кодировке **Base-64** из скачанного сертификата. 

    >[AZURE.TIP] Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    д. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** .

    Е. Щелкните **Сохранить**.


1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![Настройка единого входа][15]
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. 
   
   ![Настройка единого входа][16]
3. В меню вверху щелкните **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**. 
   
   ![Настройка единого входа][17]
4. Чтобы добавить необходимые атрибуты пользователя, для каждой строки в таблице ниже выполните следующие действия: 
   
   | Имя атрибута | Значение атрибута |
   | --- | --- |
   | firstname |user.givenname |
   | lastname |user.surname |
   | email |user.mail |

    а. Щелкните **добавить атрибут пользователя**. 

    ![Настройка единого входа][18]

    b. В текстовом поле **Имя атрибута** введите имя, указанное в этой строке, а в поле **Значение атрибута** выберите значение, указанное в этой строке. 

    ![Настройка единого входа][19]

    c. Нажмите **Завершено**.


1. На панели инструментов внизу щелкните **Применить изменения**. 
   
   ![Настройка единого входа][20]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку Далее.
6. На диалоговой странице **Профиль пользователя** выполните следующие действия: 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-cloudpassage-test-user"></a>Создание тестового пользователя CloudPassage
Цель этого раздела — создать пользователя с именем Britta Simon в CloudPassage.

#### <a name="to-create-a-user-called-britta-simon-in-cloudpassage,-perform-the-following-steps:"></a>Чтобы создать пользователя с именем Britta Simon в CloudPassage, выполните следующие действия:
1. Выполните вход на сайт **CloudPassage** компании в качестве администратора. 
2. На панели инструментов в верхней части экрана щелкните **Settings** (Параметры), а затем выберите **Site Administration** (Администрирование сайта). 
   
   ![Создание тестового пользователя CloudPassage][22] 
3. Откройте вкладку **Пользователи**, а затем щелкните **Добавить нового пользователя**. 
   
   ![Создание тестового пользователя CloudPassage][23]
4. В разделе **Добавить нового пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя CloudPassage][24]
   
   а. В текстовом поле **Имя** введите Britta.
   
   b. В текстовом поле **Фамилия** введите Simon.
   
   c. В текстовых полях **Username** (Имя пользователя), **Email** (Электронная почта) и **Retype Email** (Введите адрес еще раз) укажите данные, соответствующие пользователю Britta в Azure AD.
   
   d. Для параметра **Access Type** (Тип доступа) выберите **Enable Halo Portal Access** (Включить доступ к порталу Halo).
   
   д. Щелкните **Добавить**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к CloudPassage.

![Назначение пользователя][30]

**Чтобы назначить Britta Simon в CloudPassage, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][26]
2. В списке приложений выберите **CloudPassage**.
   
    ![Назначение пользователя][27]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][25]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][29]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент CloudPassage на панели доступа, вы автоматически войдете в приложение CloudPassage.

## <a name="additional-resources"></a>Дополнительные ресурсы
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
























<!--HONumber=Oct16_HO2-->


