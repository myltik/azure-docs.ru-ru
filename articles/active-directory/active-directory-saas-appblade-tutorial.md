---
title: "Руководство. Интеграция Azure Active Directory с AppBlade | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении AppBlade."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 38efa35e126a83de7ff2377428a5ec76ada101db


---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Руководство. Интеграция Azure Active Directory с AppBlade
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением AppBlade.  
Интеграция AppBlade с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к приложению AppBlade.
* Вы можете включить автоматический вход пользователей в AppBlade (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с AppBlade, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа AppBlade.

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

1. Добавление AppBlade из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-appblade-from-the-gallery"></a>Добавление AppBlade из коллекции
Чтобы настроить интеграцию AppBlade с Azure AD, необходимо добавить AppBlade из коллекции в список управляемых приложений SaaS.

**Чтобы добавить AppBlade из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **AppBlade**.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_01.png)

1. В области результатов выберите **AppBlade** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в AppBlade с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в AppBlade соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AppBlade.  
Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения параметра **Username** (Имя пользователя) в AppBlade.

Чтобы настроить и проверить единый вход Azure AD в AppBlade, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя AppBlade](#creating-a-appblade-test-user)** требуется для создания пользователя Britta Simon в AppBlade, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение AppBlade.

**Чтобы настроить единый вход Azure AD в AppBlade, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **AppBlade** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в AppBlade?** выберите параметр **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_03.png) >
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_04.png) 

    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение AppBlade по следующей схеме: **"https://companyname.appblade.com/saml/tenantid"**.

    b. Нажмите кнопку **Далее**.


1. На странице **Настройка единого входа в AppBlade** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_05.png) 
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Для получения единого входа, настроенного для вашего приложения, обратитесь в службу поддержки AppBlade через **support@appblade.com** и присоедините к сообщению скачанный файл метаданных. Кроме того, попросите их настроить для параметра **URL-адрес единого входа издателя** значение **https://appblade.com/saml**. Без этого параметра единый вход работать не будет.
3. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-appblade-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-appblade-test-user"></a>Создание тестового пользователя AppBlade
Цель этого раздела — создать в приложении AppBlade пользователя с именем Britta Simon. Приложение AppBlade поддерживает JIT-подготовку. Эта функция включена по умолчанию. **Убедитесь, что ваше доменное имя настроено в AppBlade для подготовки пользователей. После этого будет работать только своевременная подготовка пользователей.**

Если у пользователя есть адрес электронной почты в домене, настроенном приложением AppBlade для вашей учетной записи, такой пользователь автоматически станет участником этой учетной записи и получит указанный вами уровень разрешений: "Базовый" (базовый пользователь, который может только устанавливать приложения), "Член команды" (пользователь, который может загружать новые версии приложений и управлять проектами) или "Администратор" (все права администратора учетной записи). Обычно выбирается базовый уровень, для повышения которого пользователю предоставляется имя входа администратора (в AppBlade имя входа администратора можно настроить заранее на основе адреса электронной почты или повысить уровень пользователя от имени клиента после того, как он выполнит вход).

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к AppBlade (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки AppBlade.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к AppBlade.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в AppBlade, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **AppBlade**.
   
    ![Настройка единого входа](./media/active-directory-saas-appblade-tutorial/tutorial_appblade_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент AppBlade на панели доступа, вы автоматически войдете в приложение AppBlade.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-appblade-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


