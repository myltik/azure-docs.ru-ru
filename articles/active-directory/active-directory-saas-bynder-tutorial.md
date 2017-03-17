---
title: "Руководство. Интеграция Azure Active Directory с приложением Bynder | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Bynder."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4fb0ab26-b3b9-420a-8072-a0be80ea021e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: fc84c9d6c7648cb230db0d3cc10f31148a0a69a0
ms.openlocfilehash: 6786d7eb6a11405278ef7267f25279f9e39b3bde
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bynder"></a>Руководство. Интеграция Azure Active Directory с Bynder
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Bynder.

Интеграция Azure AD с приложением Bynder обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Bynder.
* Вы можете включить автоматический вход пользователей в Bynder (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Bynder, вам потребуется:

* подписка Azure AD;
* подписка на Bynder с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Microsoft Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Bynder из коллекции.
2. Настройка и проверка единого входа Microsoft Azure AD.

## <a name="add-bynder-from-the-gallery"></a>Добавление Bynder из коллекции
Чтобы настроить интеграцию Bynder с Azure AD, необходимо добавить Bynder из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Bynder из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Bynder**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_01.png)
7. На панели результатов выберите **Bynder** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_001.png)

## <a name="configure-and-test-microsoft-azure-ad-sso"></a>Настройка и проверка единого входа Microsoft Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Microsoft Azure AD в Bynder с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Bynder соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Bynder.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Bynder.

Чтобы настроить и проверить единый вход Microsoft Azure AD в Bynder, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Microsoft Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Bynder](#creating-a-bynder-test-user)** требуется для создания пользователя Britta Simon в Bynder, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Microsoft Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-microsoft-azure-ad-sso"></a>Настройка единого входа Microsoft Azure AD
В этом разделе описано, как включить единый вход Microsoft Azure AD на классическом портале и настроить его в приложении Bynder.

**Чтобы настроить единый вход Microsoft Azure AD в Bynder, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Bynder** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Bynder?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_03.png)
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_04.png)
  1. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.getbynder.com/sso/SAML/authenticate/`.
  2. Нажмите кнопку **Далее**.
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_10.png)
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.getbynder.com/login/`.
  2. Нажмите кнопку **Далее**.
  
   >[!NOTE]
   >Значение URL-адреса входа в этом руководстве — просто заполнитель. Чтобы получить фактическое значение для своей среды, обратитесь в службу поддержки Bynder.
   >

5. На странице **Настройка единого входа в Bynder** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_05.png)  
  1. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
  2. Нажмите кнопку **Далее**.
6. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Bynder. Присоедините к сообщению скачанный файл метаданных, чтобы специалисты Bynder смогли настроить единый вход со своей стороны.
7. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Единый вход в Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_05.png)
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_06.png)
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**. 
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bynder-tutorial/create_aaduser_08.png)
   1. Запишите значение поля **Новый пароль**.
   2. Нажмите **Завершено**.   

### <a name="create-a-bynder-test-user"></a>Создание тестового пользователя Bynder
Цель этого раздела — создать пользователя с именем Britta Simon в приложении Bynder. Приложение Bynder поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Bynder (если он еще не создан).

>[!NOTE]
>Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки Bynder. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Bynder, чтобы он мог использовать единый вход Azure.

   ![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в приложении Bynder, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Bynder**.
   
    ![Настройка единого входа](./media/active-directory-saas-bynder-tutorial/tutorial_bynder_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Microsoft Azure AD с помощью панели доступа.

Щелкнув элемент Bynder на панели доступа, вы автоматически войдете в приложение Bynder.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-bynder-tutorial/tutorial_general_205.png

