---
title: "Учебник. Интеграция Azure Active Directory с Front | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Front."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 71bc49398c576aa55159f4f62b6b6ad541d0fe5f


---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Учебник. Интеграция Azure Active Directory с Front
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Front.

Интеграция Azure AD с приложением Front обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Front.
* Вы можете включить автоматический вход пользователей в Front (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Front, вам потребуется:

* подписка Azure AD;
* подписка Front с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Front из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-front-from-the-gallery"></a>Добавление Front из коллекции
Чтобы настроить интеграцию Front с Azure AD, необходимо добавить Front из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Front из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Front**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)
7. В области результатов выберите **Front** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Front с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Front соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Front.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Front.

Чтобы настроить и проверить единый вход Azure AD в Front, вам потребуется выполнить действия в указанных далее стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Front](#creating-a-front-test-user)** требуется для создания пользователя Britta Simon в Front, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Front.

**Чтобы настроить единый вход Azure AD в Front, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **Front** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Front** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)
   
    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.frontapp.com`
   
    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.frontapp.com/sso/saml/callback`.
   
    c. Щелкните **Далее**
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)
   
    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.frontapp.com`.
   
    b. В нижней части страницы нажмите кнопку **Далее**
   
   > [!NOTE]
   > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Для получения этих значений см. **шаг 12** или свяжитесь с Front по адресу [support@frontapp.com](emailTo:support@frontapp.com).
   > 
   > 
5. На странице **Настройка единого входа в Front** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
6. Войдите в клиент Front с правами администратора.
7. Последовательно выберите пункты **Settings (Параметры) (значок шестеренки в нижней части левой боковой панели) > Preferences (Предпочтения)**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)
8. Щелкните ссылку **Single Sign On** (Единый вход).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)
9. В раскрывающемся списке **Single Sign On** (Единый вход) выберите **SAML**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)
10. В текстовое поле **Entry Point** (Точка входа) введите значение **URL-адрес службы единого входа** из мастера настройки приложения Azure AD.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)
11. Скопируйте содержимое скачанного файла сертификата и вставьте его в текстовое поле **Signing certificate** (Сертификат для подписи).
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)
12. Подтвердите, что эти URL-адреса соответствуют конфигурации из шага 3.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)
13. Нажмите кнопку **Сохранить** .
14. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]
15. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-front-test-user"></a>Создание тестового пользователя Front
Цель этого раздела — создать пользователя с именем Britta Simon в Front. Обратитесь в службу поддержки Front, чтобы добавить пользователей в учетную запись Front.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Front.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Front, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Front**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Front на панели доступа, вы автоматически войдете в приложение Front.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


