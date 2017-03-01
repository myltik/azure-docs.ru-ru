---
title: "Руководство по интеграции Azure Active Directory с Pacific Timesheet | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении Pacific Timesheet."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e546e8ba-821a-4942-9545-c84b0670beab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 603505ed07f7ab38470b669295acf08a35d9f38e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pacific-timesheet"></a>Руководство по интеграции Azure Active Directory с Pacific Timesheet
В этом руководстве описано, как интегрировать Pacific Timesheet с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Pacific Timesheet обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Pacific Timesheet.
* Вы можете включить автоматический вход пользователей в Pacific Timesheet (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Pacific Timesheet, вам потребуется:

* подписка Azure AD;
* подписка Pacific Timesheet с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>  

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

* Добавление Pacific Timesheet из коллекции.
* Настройка и проверка единого входа в Azure AD

## <a name="add-pacific-timesheet-from-the-gallery"></a>Добавление Pacific Timesheet из коллекции
Чтобы настроить интеграцию Pacific Timesheet с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pacific Timesheet из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Pacific Timesheet**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_01.png)
7. В области результатов выберите **Pacific Timesheet** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Pacific Timesheet с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Pacific Timesheet соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pacific Timesheet.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pacific Timesheet. Чтобы настроить и проверить единый вход Azure AD в Pacific Timesheet, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pacific Timesheet](#creating-a-pacific-timesheet-test-user)** требуется для создания пользователя Britta Simon в Pacific Timesheet, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Pacific Timesheet.

**Чтобы настроить единый вход Azure AD в Pacific Timesheet, сделайте следующее:**

1. В верхнем меню щелкните **Быстрый запуск**.
   
    ![Настройка единого входа][6]
2. На классическом портале на странице интеграции с приложением **Pacific Timesheet** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7] 
3. На странице **Как пользователи должны входить в Pacific Timesheet?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_06.png)
4. На диалоговой странице **Настроить параметры приложения** настройте приложение в **режиме, инициированном поставщиком удостоверений**, сделав следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_07.png)
  1. В текстовом поле "Идентификатор" введите URL-адрес в следующем формате: `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`
  2. В текстовом поле "URL-адрес ответа" введите URL-адрес в следующем формате: `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`.
  3. Нажмите кнопку **Далее**.

5. На странице **Настройка единого входа в Pacific Timesheet**. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_09.png)
6. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Pacific Timesheet. Обратите внимание, что сообщения электронной почты необходимо отправлять со значениями URL-адреса издателя и URL-адреса единого входа SAML со страницы **Настройка единого входа в Pacific Timesheet**, прикрепив скачанный сертификат.

7. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.   
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-pacific-timesheet-test-user"></a>Создание тестового пользователя Pacific Timesheet
В этом разделе описано, как создать пользователя Britta Simon в приложении Pacific Timesheet. Чтобы создать пользователя в приложении, обратитесь в службу поддержки Pacific Timesheet.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Pacific Timesheet.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pacific Timesheet, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Pacific Timesheet**.
   
    ![Настройка единого входа](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacific_timesheet_10.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке "Все пользователи" выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Pacific Timesheet на панели доступа, вы автоматически войдете в приложение Pacific Timesheet.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_205.png

