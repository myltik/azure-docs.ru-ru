---
title: "Учебник. Интеграция Azure Active Directory с Expensify | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении Expensify."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dae5382a07ca1308f9c543155dd93ecbecf72f4f


---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Руководство. Интеграция Azure Active Directory с Expensify
В этом руководстве описано, как интегрировать Expensify с Azure Active Directory (Azure AD).

Интеграция Expensify с Azure AD дает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Expensify.
* Вы можете включить автоматический вход пользователей в Expensify (единый вход) с помощью их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Expensify, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа Expensify.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Expensify из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-expensify-from-the-gallery"></a>Добавление Expensify из коллекции
Чтобы настроить интеграцию Expensify с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Expensify из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Expensify**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_01.png)
7. В области результатов выберите **Expensify** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Expensify с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Expensify соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Expensify.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Expensify.

Чтобы настроить и проверить единый вход Azure AD в Expensify, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Expensify](#creating-an-expensify-test-user)** требуется для создания в Expensify пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Expensify.

**Чтобы настроить единый вход Azure AD в Expensify, выполните следующие действия:**

1. На классическом портале на странице интеграции с приложением **Expensify** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Expensify?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_04.png) 

    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение Expensify, в следующем формате: **https://www.expensify.com/authentication/saml/login**.


1. На странице **Configure single sign-on at Expensify** (Настройка единого входа в Expensify) сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_05.png) 
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы включить единый вход в Expensify, сначала необходимо включить в этом приложении **управление доменами**. Это можно сделать, выполнив [приведенные здесь](http://help.expensify.com/domain-control) указания. За дополнительной поддержкой можно обратиться в службу поддержки Expensify по адресу [help@expensify.com](mailto:help@expensify.com). После включения управления доменами сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_51.png) 
   
    а. Войдите в приложение Expensify.
   
    b. На панели инструментов в верхней части экрана нажмите **Администратор**.
   
    c. На панели слева щелкните **Управление доменами**.
   
    d. Щелкните имя проверенного домена.
   
    д. На панели слева щелкните **SAML**, а затем переведите переключатель в положение **Включено**.
   
    Е. Откройте скачанные метаданные федерации из Azure AD, скопируйте содержимое и вставьте его в текстовое поле **метаданных поставщика удостоверений** .
3. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-an-expensify-test-user"></a>Создание тестового пользователя Expensify
В этом разделе описано, как создать пользователя Britta Simon в приложении Expensify. Обратитесь в службу поддержки Expensify, чтобы добавить пользователей на платформу Expensify.

> [!NOTE]
> Если нужно создать пользователя вручную, необходимо обратиться в службу поддержки Expensify.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Expensify.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Expensify, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Expensify**.
   
    ![Настройка единого входа](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент Expensify на панели доступа, вы автоматически войдете в приложение Expensify.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


