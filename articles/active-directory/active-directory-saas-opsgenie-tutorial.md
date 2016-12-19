---
title: "Руководство по интеграции Azure Active Directory с OpsGenie | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и OpsGenie."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 552b1a2a7ef63bc1c958adf8e06cd1c92c5c0ea2


---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Руководство. Интеграция Azure Active Directory с OpsGenie
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением OpsGenie.

Интеграция Azure AD с приложением OpsGenie обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к OpsGenie.
* Вы можете включить автоматический вход пользователей в OpsGenie (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с OpsGenie, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа OpsGenie.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. 

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление OpsGenie из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-opsgenie-from-the-gallery"></a>Добавление OpsGenie из коллекции
Чтобы настроить интеграцию OpsGenie с Azure AD, необходимо добавить OpsGenie из коллекции в список управляемых приложений SaaS.

**Чтобы добавить OpsGenie из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **OpsGenie**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)
7. В области результатов выберите **OpsGenie** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в OpsGenie с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в OpsGenie соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OpsGenie.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OpsGenie.

Чтобы настроить и проверить единый вход Azure AD в OpsGenie, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя OpsGenie](#creating-a-opsgenie-test-user)** требуется для создания пользователя Britta Simon в OpsGenie, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении OpsGenie.

**Чтобы настроить единый вход Azure AD в OpsGenie, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **OpsGenie** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в OpsGenie?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 

    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение OpsGenie по следующей схеме: **https://app.opsgenie.com/auth/login**.

    > [AZURE.NOTE] Если вам нужен URL-адрес единого входа, обратитесь в [службу поддержки OpsGenie](mailto:support@opsgenie.com) .

    b. Нажмите кнопку **Далее**.


1. На странице **Настройка единого входа в OpsGenie** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 
   
    а. Щелкните **Загрузить сертификат**и сохраните файл сертификата на свой компьютер. Этот сертификат и URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода) понадобятся для настройки единого входа на стороне OpsGenie.
   
    b. Нажмите кнопку **Далее**.
2. Откройте другое окно браузера и войдите в OpsGenie с правами администратора.
3. Щелкните **Параметры** и откройте вкладку **Единый вход**.
   
    ![Единый вход в OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 
4. Чтобы включить единый вход, установите флажок **Включено**.
   
    ![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
5. В разделе **Поставщик** откройте вкладку **Azure Active Directory**.
   
    ![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
6. На странице диалогового окна Azure Active Directory выполните следующие действия:
   
    ![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 
   
    а. На классическом портале Azure на странице диалогового окна **Настройка единого входа в OpsGenie** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Конечная точка SAML 2.0**.
   
    b. Создайте файл в кодировке Base-64 из загруженного сертификата.      
   
   > [!NOTE]
   > Дополнительные сведения вы можете узнать в видео [Преобразование двоичного сертификата в текстовый файл](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).
   > 
   > 
   
    c. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.500** .
   
    d. Нажмите кнопку **Сохранить изменения**.
7. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-opsgenie-test-user"></a>Создание тестового пользователя OpsGenie
Цель этого раздела — создать пользователя с именем Britta Simon в OpsGenie. 

1. В окне веб-браузера войдите в клиент OpsGenie с правами администратора.
2. Перейдите к списку пользователей, щелкнув **Пользователь** на левой панели.
   
   ![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 
3. Нажмите кнопку**Добавить пользователя**.
4. На странице **Добавление пользователя** выполните следующие действия.
   
   ![Параметры OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 
   
   а. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя Britta Simon в Azure Active Directory.
   
   b. В текстовом поле **Full Name** (Полное имя) введите **Britta Simon**.
   
   c. Щелкните **Сохранить**. 

Britta получит по электронной почте инструкции по настройке профиля.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к OpsGenie.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в OpsGenie, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **OpsGenie**.
   
    ![Настройка единого входа](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент OpsGenie на панели доступа, вы автоматически войдете в приложение OpsGenie.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


