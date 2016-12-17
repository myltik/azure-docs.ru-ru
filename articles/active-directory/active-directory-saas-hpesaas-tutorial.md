---
title: "Учебник. Интеграция Azure Active Directory с HPE SaaS | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и HPE SaaS."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 314003d6-ca66-4456-88c3-934254d4a9a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 92d61ff08b70e89b5e5310cf4b0f7fb88abf3ef9


---
# <a name="tutorial-azure-active-directory-integration-with-hpe-saas"></a>Руководство. Интеграция Azure Active Directory с HPE SaaS
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с HPE SaaS.  
Интеграция Azure AD с HPE SaaS обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к HPE SaaS.
* Вы можете включить автоматический вход пользователей в HPE SaaS (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с HPE SaaS, вам потребуется:

* подписка Azure AD;
* подписка HPE SaaS с поддержкой единого входа.

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

1. Добавление HPE SaaS из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-hpe-saas-from-the-gallery"></a>Добавление HPE SaaS из коллекции
Чтобы настроить интеграцию HPE SaaS с Azure AD, необходимо добавить HPE SaaS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HPE SaaS из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **HPE SaaS**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_01.png)
7. В области результатов выберите **HPE SaaS** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в HPE SaaS с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в HPE SaaS соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в HPE SaaS.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HPE SaaS.

Чтобы настроить и проверить единый вход Azure AD в HPE SaaS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя HPE SaaS](#creating-a-hpesaas-test-user)** требуется для создания в HPE SaaS пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении HPE SaaS.

**Чтобы настроить единый вход Azure AD в HPE SaaS, выполните следующие действия:**

1. На странице интеграции с приложением **HPE SaaS** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в HPE SaaS?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_03.png) 
3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_04.png) 

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение HPE SaaS, в следующем формате: **https://login.saas.hpe.com/msg**. Клиенты также могут заменить это значение URL-адресом конкретного приложения.

    b. Нажмите кнопку **Далее**.


1. На странице **Настройка единого входа в HPE SaaS** выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_05.png) 
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы получить данные единого входа, настроенные для вашего приложения, напишите в службу поддержки HPE SaaS по электронной почте, прикрепив скачанный файл сертификата. После этого служба поддержки сможет настроить для вас интеграцию единого входа.
3. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_09.png) 

1. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
2. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_03.png) 
3. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_04.png) 
4. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
5. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_07.png) 
7. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hpesaas-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-hpe-saas-test-user"></a>Создание тестового пользователя HPE SaaS
Цель этого раздела — создать пользователя с именем Britta Simon в HPE SaaS. Обратитесь в службу поддержки HPE SaaS, чтобы добавить пользователей в систему HPE SaaS. 

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь в службу поддержки HPE SaaS.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к HPE SaaS.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в HPE SaaS, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **HPE SaaS**.
   
    ![Настройка единого входа](./media/active-directory-saas-hpesaas-tutorial/tutorial_hpesaas_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку HPE SaaS на панели доступа, вы автоматически войдете в приложение HPE SaaS.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hpesaas-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


