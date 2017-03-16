---
title: "Руководство по интеграции Azure Active Directory с Predictix Ordering | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Predictix Ordering."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1e0ef693f8176e61bf18e0a37be9bde1cc5d03a2
ms.openlocfilehash: 8f6eca0c23e6886fd302ab8371a99c8b366f8d75
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Руководство. Интеграция Azure Active Directory с Predictix Ordering
В этом руководстве описано, как интегрировать Predictix Ordering с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Predictix Ordering обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Predictix Ordering.
* Вы можете включить автоматический вход пользователей в Predictix Ordering (единый вход) с использованием учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Predictix Ordering, вам потребуется:

* подписка Azure AD;
* подписка на Predictix Ordering с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление приложения Predictix Ordering из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-predictix-ordering-from-the-gallery"></a>Добавление приложения Predictix Ordering из коллекции
Чтобы настроить интеграцию Predictix Ordering с Azure AD, необходимо добавить приложение Predictix Ordering из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Predictix Ordering из коллекции, выполните указанные ниже действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Predictix Ordering**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_01.png)
7. В области результатов выберите **Predictix Ordering** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Predictix Ordering с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Predictix Ordering соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Predictix Ordering.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Predictix Ordering.

Чтобы настроить и проверить единый вход Azure AD в Predictix Ordering, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Predictix Ordering](#creating-a-predictix-price-reporting-test-user)** требуется для создания в Predictix Ordering пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Predictix Ordering.

**Чтобы настроить единый вход Azure AD в Predictix Ordering, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Predictix Ordering** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Predictix Ordering?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_04.png) 
  1. В текстовом поле **URL-адрес входа** введите URL-адрес, применяемый пользователями для входа в приложение Predictix Ordering, в следующем формате: **https://\<имя_компании-ценовая_категория\>.ordering.predictix.com/sso/request**.
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Predictix Ordering** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_05.png) 
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. Для настройки единого входа в приложении обратитесь в службу поддержки Predictix Ordering и предоставьте следующие сведения:
  * Скачанный сертификат
  * **идентификатор сущности**
  * **URL-адрес единого входа SAML**
  * **URL-адрес службы единого выхода**
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-predictix-ordering-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-an-predictix-ordering-test-user"></a>Создание тестового пользователя в Predictix Ordering
В этом разделе описано, как создать пользователя Britta Simon в приложении Predictix Ordering. Чтобы добавить пользователей в Predictix Ordering, обратитесь в службу поддержки Predictix Ordering.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Predictix Ordering.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Predictix Ordering, выполните указанные ниже действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Predictix Ordering**.
   
    ![Настройка единого входа](./media/active-directory-saas-predictix-ordering-tutorial/tutorial_predictixordering_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Predictix Ordering на панели доступа, вы автоматически войдете в приложение Predictix Ordering.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-ordering-tutorial/tutorial_general_205.png

