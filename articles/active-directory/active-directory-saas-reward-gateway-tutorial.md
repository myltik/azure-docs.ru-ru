---
title: "Руководство по интеграции Azure Active Directory с Reward Gateway | Документация Майкрософт"
description: "Узнайте, как настроить единый вход для Azure Active Directory и Reward Gateway."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 4d4cc0c13fb0d05ecad6c81c4f7b704fa3152ed5
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Учебник. Интеграция Azure Active Directory с Reward Gateway
В этом учебнике описано, как интегрировать Reward Gateway с Azure Active Directory (Azure AD).

Интеграция Reward Gateway с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Reward Gateway.
* Вы можете включить автоматический вход пользователей в Reward Gateway (единый вход) с помощью их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Reward Gateway, вам потребуется:

* подписка Azure AD;
* подписка на Reward Gateway с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Reward Gateway из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-reward-gateway-from-the-gallery"></a>Добавление Reward Gateway из коллекции
Чтобы настроить интеграцию Reward Gateway с Azure AD, необходимо добавить Reward Gateway из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Reward Gateway из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Reward Gateway**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_01.png)
7. В области результатов выберите **Reward Gateway** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Reward Gateway с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Reward Gateway соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Reward Gateway.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Reward Gateway.

Чтобы настроить и проверить единый вход Azure AD в Reward Gateway, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Reward Gateway](#creating-a-reward-gateway-test-user)** требуется для создания в Reward Gateway пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Reward Gateway.

**Чтобы настроить единый вход Azure AD в Reward Gateway, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Reward Gateway** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Reward Gateway?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_04.png) 
  1. В текстовом поле **URL-адрес идентификатора** введите URL-адрес, используемый пользователями для входа в приложение Reward Gateway, применяя следующий шаблон: 
   
    | URL-адрес идентификатора |
    | --- |
    | `https://<company name>.rewardgateway.com/` |
    | `https://<company name>.rewardgateway.co.uk/` |
    | `https://<company name>.rewardgateway.co.nz/` |
    | `https://<company name>.rewardgateway.com.au/` |
  2. В текстовое поле **URL-адрес ответа** введите URL-адрес в следующем формате: 

    | URL-адрес ответа |
    | --- |
    | `https://<company name>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |

  3. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в Reward Gateway** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_05.png)
  * Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
5. Чтобы настроить единый вход для своего приложения, обратитесь к [группе поддержки](mailTo:clientsupport@rewardgateway.com) Reward Gateway и предоставьте следующие сведения:
   
    • скачанный файл **метаданных**
6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
6. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_05.png)    
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_06.png)    
  1. В текстовом поле **Имя** введите **Britta**.   
  2. В текстовом поле **Фамилия** введите **Simon**. 
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**. 
  2. Нажмите **Завершено**.   

### <a name="create-an-reward-gateway-test-user"></a>Создание тестового пользователя Reward Gateway
В этом разделе описано, как создать пользователя Britta Simon в приложении Reward Gateway. Обратитесь в [службу поддержки](mailTo:clientsupport@rewardgateway.com) Reward Gateway, чтобы добавить пользователей на платформу Reward Gateway.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Reward Gateway.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Reward Gateway, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Reward Gateway**.
   
    ![Настройка единого входа](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Reward Gateway на панели доступа, вы автоматически войдете в приложение Reward Gateway.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_205.png

