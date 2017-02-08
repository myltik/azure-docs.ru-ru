---
title: "Учебник. Интеграция Azure Active Directory с Hightail | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Hightail."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: aa5cb974f0181b675faa0c1a064098e4a011ca51


---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Учебник. Интеграция Azure Active Directory с Hightail
Цель этого учебника — показать, как интегрировать Hightail с Azure Active Directory (Azure AD).

Интеграция Hightail с Azure AD дает приведенные далее преимущества:

* С помощью Azure AD вы можете контролировать доступ к Hightail.
* Вы можете включить автоматический вход пользователей в Hightail (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Hightail, вам потребуется:

* подписка Azure AD;
* подписка Hightail с поддержкой единого входа.

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

1. Добавление Hightail из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-hightail-from-the-gallery"></a>Добавление Hightail из коллекции
Чтобы настроить интеграцию Hightail с Azure AD, необходимо добавить Hightail из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Hightail из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Hightail**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_01.png)
7. В области результатов выберите **Hightail** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Hightail с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Hightail соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Hightail.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Hightail.

Чтобы настроить и проверить единый вход Azure AD в Hightail, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Hightail](#creating-a-hightail-test-user)** требуется для создания в Hightail пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure AD и настроить единый вход в приложении Hightail.

Приложение Hightail ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке **"Атрибут"** приложения. На следующем снимке экрана приведен пример. 

![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_51.png) 

**Чтобы настроить единый вход Azure AD в Hightail, выполните следующие действия:**

1. На странице интеграции с приложением **Hightail** классического портала Azure в меню в верхней части страницы щелкните **Атрибуты**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_general_81.png) 
2. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:
   
   | Имя атрибута | Значение атрибута |
   | --- | --- |
   | FirstName |user.givenname |
   | LastName |user.surname |
   | Email |user.mail |
   | UserIdentity |user.mail |
   
    а. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_general_82.png) 

    b. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.

    d. Нажмите **Завершено**.    




1. В меню вверху щелкните **Быстрый запуск**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  
2. На странице **Как пользователи должны входить в Hightail?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_03.png) 
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_04.png) 

    а. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: **https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse**.

    b. Щелкните **Далее**

1. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_06.png) 
   
    а. В текстовом поле "URL-адрес для входа" введите URL-адрес, используемый для входа в приложение Hightail, в следующем формате: **https://www.hightail.com/loginSSO**. Это общая страница входа для всех клиентов, которые хотят использовать единый вход.
   
    b. Щелкните **Далее**
2. На странице **Настройка единого входа в Hightail** выполните следующие действия и нажмите кнопку **Далее**:
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_05.png) 

    а. Нажмите **Скачать сертификат**и сохраните файл сертификата в кодировке Base 64 на свой компьютер.

    b. Нажмите кнопку **Далее**.

    > [AZURE.NOTE] Перед настройкой единого входа в приложение Hightail внесите почтовый домен Hightail в список разрешенных, чтобы все пользователи этого домена могли использовать функции единого входа.

1. Чтобы единый вход был настроен для вашего приложения, войдите в клиент Hightail с правами администратора.
   
    а. В меню в верхней части экрана откройте вкладку **Account** (Учетная запись) и выберите **Configure SAML** (Настроить SAML).
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. Установите флажок **Включить проверку подлинности SAML**.

    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат подписи токена SAML** .

    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 


    d. Скопируйте URL-адрес удаленного входа из Azure AD в поле **SAML Authority (Identity Provider)** (Центр SAML (поставщик удостоверений)) в Hightail.

    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_005.png)

    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    д. Чтобы настроить приложение в **режиме, инициированном поставщиком удостоверений**, выберите **Identity Provider (IdP) initiated log in** (Вход, инициированный поставщиком удостоверений (IdP)). Чтобы настроить приложение в **режиме, инициированном поставщиком услуг**, выберите **Service Provider (SP) initiated log in** (Вход, инициированный поставщиком услуг (SP)).

    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    Е. Скопируйте URL-адрес получателя SAML для своего экземпляра и вставьте его в текстовое поле **URL-адрес ответа** , как показано в шаге 4. 

    ж. Щелкните **Сохранить**.



1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. 
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_05.png) 
   
    а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-hightail-test-user"></a>Создание тестового пользователя Hightail
Цель этого раздела — создать пользователя с именем Britta Simon в Hightail. 

В этом разделе никакие действия с вашей стороны не требуются. Приложение Hightail поддерживает JIT-подготовку пользователей на основе настраиваемых утверждений. Если вы настроили пользовательские утверждения, как показано в разделе **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** выше, пользователь будет автоматически создан в приложении (если он еще не существует). 

> [!NOTE]
> Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Hightail по адресу [support@hightail.com](mailto:support@hightail.com).
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Hightail.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Hightail, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале Azure щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Hightail**.
   
    ![Настройка единого входа](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.

![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Hightail на панели доступа, вы автоматически войдете в приложение Hightail.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


