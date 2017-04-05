---
title: "Руководство по интеграции Azure Active Directory с RightScale | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и RightScale."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: adcc4e35f8febe1d0dc1bc093954dee56bf34652
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Учебник. Интеграция Azure Active Directory с RightScale
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением RightScale.

Интеграция RightScale с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к RightScale.
* Вы можете включить автоматический вход пользователей в RightScale (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением RightScale, вам потребуется:

* подписка Azure AD;
* подписка RightScale с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>  

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление RightScale из коллекции
2. Настройка и проверка единого входа Azure AD

## <a name="add-rightscale-from-the-gallery"></a>Добавление RightScale из коллекции
Чтобы настроить интеграцию RightScale с Azure AD, необходимо добавить RightScale из коллекции в список управляемых приложений SaaS.

**Чтобы добавить RightScale из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **RightScale**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_01.png)

7. В области результатов выберите **RightScale** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в RightScale с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в RightScale соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в RightScale.  

Чтобы настроить и проверить единый вход Azure AD в RightScale, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя RightScale](#creating-a-rightscale-test-user)** требуется для создания пользователя Britta Simon в RightScale, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении RightScale.

**Чтобы настроить единый вход Azure AD в RightScale, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **RightScale** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в RightScale?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_03.png) 

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_04.png) 
  1. В текстовом поле "URL-адрес ответа" введите URL-адрес в следующем формате: `https://login.rightscale.com/login/saml2/consume`
  2. Нажмите кнопку **Далее**.

1. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_06.png) 
  1. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение RightScale, применяя следующий шаблон: `https://login.rightscale.com/`
  2. Нажмите кнопку **Далее**.

2. На странице **Настройка единого входа в RightScale** выполните указанные ниже действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_05.png) 
  1. Нажмите **Скачать сертификат**и сохраните файл сертификата в кодировке Base 64 на свой компьютер.
  2. Нажмите кнопку **Далее**.

3. Чтобы настроить для приложения единый вход, нужно войти в клиент приложения RightScale с правами администратора.
  1. В меню в верхней части откройте вкладку **Параметры** и выберите **Единый вход**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png) 
  2. Нажмите кнопку **Новый** и добавьте свои поставщики удостоверений SAML в области **Your SAML Identity Providers** (Ваши поставщики удостоверений SAML).
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png)  
  3. В текстовом поле **Отображаемое имя** введите название своей компании.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png) 
  4. Выберите **Allow RightScale-initiated SSO using a discovery hint** (Разрешить единый вход, инициированный RightScale, с использованием подсказки обнаружения) и в приведенном ниже текстовом поле введите **имя домена**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)
  5. Скопируйте URL-адрес единого входа SAML из Azure AD в поле **SAML SSO Endpoint** (Конечная точка единого входа SAML) в RightScale.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_005.png)
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)
  6. Скопируйте идентификатор сущности из Azure AD в поле **SAML EntityID** (идентификатор сущности SAML) в RightScale.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_007.png)
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)
  7. Нажмите кнопку **Браузер** , чтобы отправить сертификат, скачанный в шаге 4.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)
  8. Щелкните **Сохранить**.

4. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]

5. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_05.png) 
  1. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_08.png)  
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-rightscale-test-user"></a>Создание тестового пользователя RightScale
В этом разделе описано, как создать пользователя Britta Simon в приложении RightScale. Обратитесь в службу поддержки RightScale по адресу support@rightscale.com , чтобы добавить пользователей в платформу RightScale.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к RightScale.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в RightScale, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **RightScale**.
   
    ![Настройка единого входа](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент RightScale на панели доступа, вы автоматически войдете в приложение RightScale.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_205.png

