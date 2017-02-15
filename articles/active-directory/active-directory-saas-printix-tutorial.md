---
title: "Руководство по интеграции Azure Active Directory с Printix | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Printix."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 04b506da28def0549d3ecc3b5d424371790b51d4


---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Руководство. Интеграция Azure Active Directory с Printix
В этом руководстве описано, как интегрировать Printix с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Printix обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Printix.
* Вы можете включить автоматический вход пользователей в Printix (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Printix, вам потребуется:

* подписка Azure AD;
* подписка Printix с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Printix из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-printix-from-the-gallery"></a>Добавление Printix из коллекции
Чтобы настроить интеграцию Printix с Azure AD, необходимо добавить Printix из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Printix из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **Printix**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/tutorial_printix_01.png)

7. В области результатов выберите **Printix** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Printix с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Printix соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Printix.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Printix.

Чтобы настроить и проверить единый вход Azure AD в Printix, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Printix](#creating-a-printix-test-user)** требуется для создания в Printix пользователя с именем Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Printix.

**Чтобы настроить единый вход Azure AD в Printix, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **Printix** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Printix?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_04.png) 
   
    а. В текстовом поле **URL-адрес ответа** введите `https://auth.printix.net/saml/SSO`.
   
    b. click **Далее**

4. На странице **Настройка единого входа в Printix** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_05.png)
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.

5. Войдите в клиент Printix как администратор.

6. В меню вверху щелкните значок в правом верхнем углу и выберите**Authentication**(Проверка подлинности).
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_06.png)

7. На вкладке **Setup** (Настройка) выберите **Enable Azure/Office 365 authentication** (Включить проверку подлинности Azure/Office 365).
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_07.png)

8. На вкладке **Azure** введите URL-адрес метаданных федерации в поле ввода **Federation metadata document** (Документ метаданных федерации). 
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_08.png)
   
    а. Передайте XML-файл метаданных, который вы загрузили на шаге 4, в службу поддержки Printix по адресу**support@printix.net**. Служба отправит этот XML-файл на сервер и предоставит вам URL-адрес метаданных федерации.

9. Нажмите кнопку **Test** (Тестировать). Если проверка будет выполнена успешно, нажмите кнопку **ОК**.
   
    а. После нажатия кнопки **тестирования** отобразится страница Azure Active Directory. Если на ней есть сообщение The test was successful (Проверка выполнена успешно), введите учетные данные для тестовой учетной записи Azure. Появится окно с сообщением Settings tested OK (Параметры успешно проверены). Нажмите кнопку **ОК**.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_09.png)

10. Щелкните кнопку **Save** (Сохранить) на странице **Authentication** (Проверка подлинности).

11. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

12. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-an-printix-test-user"></a>Создание тестового пользователя Printix
Цель этого раздела — создать пользователя с именем Britta Simon в приложении Printix. Приложение Printix поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Printix (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки Printix.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Printix.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Printix, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **Printix**.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Printix на панели доступа, вы автоматически войдете в приложение Printix.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-printix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-printix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-printix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-printix-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-printix-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-printix-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-printix-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-printix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-printix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-printix-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-printix-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-printix-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-printix-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


