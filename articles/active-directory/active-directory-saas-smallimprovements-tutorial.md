---
title: "Руководство по интеграции Azure Active Directory со Small Improvements | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Small Improvements."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75a1578578cabd6178b15db87c04854a6cbac6fe


---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Учебник. Интеграция Azure Active Directory со Small Improvements
Цель этого учебника — показать, как интегрировать приложение Small Improvements с Azure Active Directory (Azure AD).  
Интеграция Azure AD с приложением Small Improvements обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к приложению Small Improvements.
* Вы можете включить автоматический вход пользователей в Small Improvements (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD со Small Improvements, вам потребуется:

* подписка Azure AD;
* подписка Small Improvements с поддержкой единого входа.

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

1. Добавление Small Improvements из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-small-improvements-from-the-gallery"></a>Добавление Small Improvements из коллекции
Чтобы настроить интеграцию Small Improvements с Azure AD, необходимо добавить Small Improvements из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Small Improvements из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Small Improvements**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)
7. В области результатов выберите **Small Improvements** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Small Improvements с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Small Improvements соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Small Improvements.  
Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Small Improvements.

Чтобы настроить и проверить единый вход Azure AD в Small Improvements, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Small Improvements](#creating-a-small-improvements-test-user)** требуется для создания пользователя Britta Simon в Small Improvements, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Small Improvements.

**Чтобы настроить единый вход Azure AD в Small Improvements, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Small Improvements** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Small Improvements?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) 
   
   > [!NOTE]
   > Обратитесь в службу поддержки Small Improvements по адресу [Support@small-improvements.com](mailto:support@small-improvements.com) , чтобы настроить доменное имя для своей учетной записи. Без него единый вход работать не будет.
   > 
   > 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Small Improvements.  
    b. Нажмите кнопку **Далее**.


1. На странице **Настройка единого входа в Small Improvements** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. В другом окне браузера войдите на корпоративный сайт Small Improvements с правами администратора.
3. На главной странице панели мониторинга нажмите слева кнопку **Администрирование** .
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 
4. Нажмите кнопку **SAML SSO** (Единый вход SAML) в разделе **Integrations** (Интеграции).
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 
5. На странице настройки единого входа выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png) 
   
    а. На странице диалогового окна **Настройка единого входа в Small Improvements** портала Azure скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **Конечная точка HTTP**.
   
    b. Откройте загруженный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** .
   
    c. Если вы хотите предоставить пользователям единый вход и возможность проверки подлинности через форму входа, установите флажок **Включить также доступ по имени входа и паролю** . 
   
    d. Введите значение имени единого входа в текстовом поле **SAML Prompt** (Приглашение SAML). 
   
    д. Щелкните **Сохранить**.
6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-small-improvements-test-user"></a>Создание тестового пользователя Small Improvements
Цель этого раздела — создать в приложении Small Improvements пользователя с именем Britta Simon.
Эта функция уже включена в ходе [настройки единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

**Чтобы создать в приложении Small Improvements пользователя с именем Britta Simon, выполните следующие действия.**

1. Войдите на корпоративный сайт Small Improvements с правами администратора.
2. На главной странице перейдите к меню в левой части страницы и щелкните **Администрирование**.
3. Нажмите кнопку **Каталог пользователей** в разделе "Управление пользователями". 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 
4. Щелкните **Add Team Member**(Добавить участника команды).
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 
5. Введите в текстовые поля **First Name** (Имя), **Last Name** (Фамилия) и **Email Address** (Адрес электронной почты) соответствующие данные допустимого пользователя, которого вы хотите подготовить. 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) 
6. Кроме того, вы можете ввести персональное сообщение в поле **Отправить уведомление по электронной почте** . Если уведомление отправлять не нужно, снимите этот флажок.
7. Щелкните **Создать пользователей**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Small Improvements.

    ![Assign User][200] 

**Чтобы назначить пользователя Britta Simon в Small Improvements, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **Small Improvements**.
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент Small Improvements на панели доступа, вы автоматически войдете в приложение Small Improvements.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


