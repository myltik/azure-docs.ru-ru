---
title: "Руководство. Интеграция Azure Active Directory с CA PPM | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и CA PPM."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ca9d5e71-e429-4891-8d10-3498e7210e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 014f81f1577f67c3e186e2f55d3ca7486e8a6d53
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ca-ppm"></a>Учебник. Интеграция Azure Active Directory с CA PPM
В этом учебнике описано, как интегрировать CA PPM с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением CA PPM обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к CA PPM.
* Вы можете включить автоматический вход пользователей в CA PPM (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с CA PPM, вам потребуется:

* подписка Azure AD;
* подписка на CA PPM с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление CA PPM из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-ca-ppm-from-the-gallery"></a>Добавление CA PPM из коллекции
Чтобы настроить интеграцию CA PPM с Azure AD, необходимо добавить CA PPM из коллекции в список управляемых приложений SaaS.

**Чтобы добавить CA PPM из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **CA PPM**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/tutorial_cappm_01.png)
7. В области результатов выберите **CA PPM** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/tutorial_cappm_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе можно настроить и проверить единый вход Azure AD в CA PPM с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в CA PPM соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в CA PPM.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в CA PPM.

Чтобы настроить и проверить единый вход Azure AD в CA PPM, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя CA PPM](#creating-an-ca-ppm-test-user)** требуется для создания пользователя Britta Simon в CA PPM, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении CA PPM.

**Чтобы настроить единый вход Azure AD в CA PPM, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **CA PPM** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в CA PPM?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-cappm-tutorial/tutorial_cappm_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-cappm-tutorial/tutorial_cappm_04.png) 
  1. В текстовом поле **Идентификатор** введите URL-адрес, используемый пользователями для входа в приложение CA PPM, в следующем формате: **https://ca.ondemand.saml.20.post.\<имя_компании\>**.
  2. В текстовом поле **URL-адрес ответа** введите **https://fedsso.ondemand.ca.com/affwebservices/public/saml2assertionconsumer**. 
  3. click **Далее**
4. На странице **Настройка единого входа в CA PPM** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-cappm-tutorial/tutorial_cappm_05.png)
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. Для настройки единого входа для своего приложения обратитесь в отдел [технической поддержки CA](mailto:catechnicalsupport@ca.com) и предоставьте следующие сведения:
  
  * Скачанный сертификат
  * **идентификатор сущности**
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

 ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_06.png)   
  1. В текстовом поле **Имя** введите **Britta**.    
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cappm-tutorial/create_aaduser_08.png)  
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-an-ca-ppm-test-user"></a>Создание тестового пользователя CA PPM
В этом разделе описано, как создать пользователя Britta Simon в приложении CA PPM. Обратитесь в службу поддержки CA PPM, чтобы добавить пользователей в платформу CA PPM.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к CA PPM.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в CA PPM, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **CA PPM**.
   
    ![Настройка единого входа](./media/active-directory-saas-cappm-tutorial/tutorial_cappm_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент CA PPM на панели доступа, вы автоматически войдете в приложение CA PPM.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cappm-tutorial/tutorial_general_205.png

