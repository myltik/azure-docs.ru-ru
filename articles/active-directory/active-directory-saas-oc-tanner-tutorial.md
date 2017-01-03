---
title: "Руководство по интеграции Azure Active Directory с O. C. Tanner — AppreciateHub | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в O. C. Tanner — AppreciateHub."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a2129813f7214b2b32b2d7d6c9d666e96b60a926
ms.openlocfilehash: 10cf00155767b0cc8a318993e3ce95e55079312f


---
# <a name="tutorial-azure-active-directory-integration-with-o-c-tanner---appreciatehub"></a>Руководство по интеграции Azure Active Directory с O. C. Tanner — AppreciateHub
Цель данного руководства — показать, как интегрировать O.C. Tanner — AppreciateHub с Azure Active Directory (Azure AD).  
Интеграция O.C. Tanner — AppreciateHub с Azure AD обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к O.C. Tanner — AppreciateHub 
* Вы можете включить автоматический вход пользователей в O.C. Tanner — AppreciateHub (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с O.C. Tanner — AppreciateHub, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа O.C. подписка Tanner — AppreciateHub с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление O.C. Tanner — AppreciateHub из коллекции 
2. Настройка и проверка единого входа в Azure AD.

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Добавление O.C. Tanner — AppreciateHub из коллекции
Чтобы настроить интеграцию O.C. Tanner — AppreciateHub с Azure AD, необходимо добавить O.C. Tanner — AppreciateHub из коллекции в список управляемых приложений SaaS.

**Чтобы добавить O.C. Tanner — AppreciateHub из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1] 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2] 
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3] 
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4] 
6. В поле поиска введите **O.C. Tanner — AppreciateHub**.
   
    ![Приложения][5] 
7. В области результатов выберите **O.C. Tanner — AppreciateHub** и щелкните **Завершено**, чтобы добавить приложение.
   
    ![Приложения][25] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в O.C. Tanner — AppreciateHub с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в O.C. Tanner — AppreciateHub соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в O.C. Необходимо установить связь Tanner — AppreciateHub.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в O.C. Tanner — AppreciateHub.

Чтобы настроить и проверить единый вход Azure AD в O.C. Tanner — AppreciateHub, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя O.C. Tanner — AppreciateHub](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в O.C. Tanner — AppreciateHub, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение O.C. Tanner — AppreciateHub.

**Чтобы настроить единый вход Azure AD в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции приложения **O.C. Tanner — AppreciateHub** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в O.C. Tanner — AppreciateHub** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7]
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка параметров приложения][8]
   
     а. Откройте файл метаданных, используя следующую ссылку: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).
   
     b. Найдите узел **md:AssertionConsumerService** . 
   
     c. Скопируйте значение атрибута **Location**. 
   
     ![Настройка параметров приложения][12]
   
     d. Вставьте значение, полученное на предыдущем шаге, в текстовом поле **URL-адрес для входа**.
   
    > [!NOTE]
    > Если у вас возникли проблемы с получением URL-адреса ответа из файла метаданных, обратитесь в службу поддержки O.C. Tanner — AppreciateHub по адресу [sso@octanner.com](mailto:sso@octanner.com).
    > 
    > 
   
    д. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в O.C. Tanner — AppreciateHub** щелкните **Скачать метаданные**, а затем сохраните файл метаданных локально на компьютере.
   
    ![Что такое Azure AD Connect?][9]
5. Отправьте файл метаданных службе поддержки O.C. Tanner — AppreciateHub по адресу xyz и попросите активировать для вас единый вход.
6. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**. 
   
    ![Что такое Azure AD Connect?][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Что такое Azure AD Connect?][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Создание тестового пользователя O.C. Tanner — AppreciateHub
Цель этого раздела — создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub.

**Чтобы создать пользователя с именем Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

Попросите службу поддержки OC Tanner создать пользователя, у которого значение атрибута nameID совпадает с именем пользователя Simon Britta в Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к O.C. Tanner — AppreciateHub.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в O.C. Tanner — AppreciateHub, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **O.C. Tanner — AppreciateHub**.
   
    ![Назначение пользователя][202]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку O.C. Tanner — AppreciateHub на панели доступа, вы автоматически войдете в приложение O.C. Tanner — AppreciateHub.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png









<!--HONumber=Nov16_HO4-->


