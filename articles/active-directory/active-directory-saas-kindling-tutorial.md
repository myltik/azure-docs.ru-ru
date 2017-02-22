---
title: "Учебник. Интеграция Azure Active Directory с Kindling | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в Kindling."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 71229751-74eb-4c2c-abb4-07caa95754c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: cf030c5f6d5b3d3cb6c33ba741df08723a4db779


---
# <a name="tutorial-azure-active-directory-integration-with-kindling"></a>Учебник. Интеграция Azure Active Directory с Kindling
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Kindling.  
Интеграция Azure AD с приложением Kindling дает следующие преимущества. 

* с помощью Azure AD вы можете контролировать доступ к Kindling; 
* вы можете включить автоматический вход пользователей в Kindling (единый вход) с использованием учетных записей Azure AD;
* Вы можете управлять учетными записями централизованно — через классический портал Azure. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Kindling, вам потребуется:

* подписка Azure AD;
* подписка Kindling.

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

1. Добавление Kindling из коллекции 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-kindling-from-the-gallery"></a>Добавление Kindling из коллекции
Чтобы настроить интеграцию Kindling с Azure AD, необходимо добавить Kindling из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kindling из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **Kindling**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_01.png)

7. В области результатов выберите **Kindling** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Kindling с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Kindling соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kindling.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Kindling.

Чтобы настроить и проверить единый вход Azure AD в Kindling, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Kindling](#creating-a-kindling-test-user)** требуется для создания пользователя Britta Simon в Kindling, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Kindling. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

Для настройки единого входа в приложение Kindling вам потребуется зарегистрированный домен. Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Kindling по адресу [support@kindlingapp.com](mailto:support@kindlingapp.com).  

**Чтобы настроить единый вход Azure AD в Kindling, выполните следующие действия.**

1. На странице интеграции с приложением **Kindling** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Kindling?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_04.png) 

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение Kindling, в следующем формате: `https://<company name>.kindlingapp.com/`.

    b. Для получения значений **Издатель** и **URL-адрес ответа** обратитесь в службу поддержки Kindling по адресу [support@kindlingapp.com](mailto:support@kindlingapp.com).   

    В. В текстовом поле **Издатель** введите URL-адрес издателя.

    d. В текстовом поле **URL-адрес ответа** введите URL-адрес ответа.   

    д. Нажмите кнопку **Далее**.


1. На странице **Configure single sign-on at Kindling** (Настройка единого входа в Kindling) сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.

2. Обратитесь в службу поддержки Kindling по адресу [support@kindlingapp.com](mailto:support@kindlingapp.com) и предоставьте следующие сведения:
   
    - Скачанный сертификат
    - **URL-адрес издателя**, соответствующий значению **Entity ID** (Идентификатор сущности) в Kindling;
    - **URL-адрес службы единого входа**, соответствующий значению **SSO Sign On URL** (URL-адрес единого входа) в Kindling; 
    - **URL-адрес службы единого выхода**, соответствующий значению **SSO Sign Out URL** (URL-адрес единого выхода) в Kindling. 

3. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![единого входа Azure AD][10]

4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_05.png)  
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kindling-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-kindling-test-user"></a>Создание тестового пользователя Kindling
Цель этого раздела — создать пользователя с именем Britta Simon в Kindling.
Приложение Kindling поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа Azure AD](#configuring-azure-ad-single-single-sign-on).

В этом разделе никакие действия с вашей стороны не требуются.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления ему доступа к Kindling.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Kindling, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **Kindling**.
   
    ![Настройка единого входа](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку Kindling на панели доступа, вы автоматически войдете в приложение Kindling.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_205.png









<!--HONumber=Dec16_HO1-->


