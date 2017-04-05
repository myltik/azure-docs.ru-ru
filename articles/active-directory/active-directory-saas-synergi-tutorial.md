---
title: "Руководство по интеграции Azure Active Directory с Synergi | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Synergi."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 73c970e1-f1ba-420b-b225-414fdf93b140
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 69b1cecc695c71f226eb28f72dd890df04452359
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-synergi"></a>Руководство. Интеграция Azure Active Directory с Synergi
В этом руководстве описано, как интегрировать Synergi с Azure Active Directory (Azure AD).

Интеграция Azure AD с Synergi обеспечивает следующие преимущества:

* С помощью Azure AD можно контролировать доступ к Synergi.
* Вы можете включить автоматический вход пользователей в Synergi (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Synergi, вам потребуется:

* подписка Azure AD;
* подписка Synergi с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Synergi из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-synergi-from-the-gallery"></a>Добавление Synergi из коллекции
Чтобы настроить интеграцию Synergi с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Synergi из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **Synergi**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/tutorial_synergi_01.png)

7. В области результатов выберите **Synergi** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/tutorial_synergi_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в Synergi с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Synergi соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Synergi.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Synergi.

Чтобы настроить и проверить единый вход Azure AD в Synergi, необходимо выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Synergi](#creating-a-synergi-test-user)** требуется для создания пользователя Britta Simon в Synergi, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Synergi.

**Чтобы настроить единый вход Azure AD в Synergi, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Synergi** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Synergi** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-synergi-tutorial/tutorial_synergi_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-synergi-tutorial/tutorial_synergi_04.png) 
  1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Synergi, в формате: **https://\<имя_компании\>.irmsecurity.com/sso/\<идентификатор_организации\>**. 
  2. click **Далее**

4. На странице **Настройка единого входа в Synergi** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-synergi-tutorial/tutorial_synergi_05.png)
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.

5. Чтобы настроить единый вход для своего приложения, обратитесь к группе поддержки Synergi и предоставьте следующие сведения:
   
  * Скачанный сертификат
  * **идентификатор сущности** 
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
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-synergi-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-an-synergi-test-user"></a>Создание тестового пользователя Synergi
В этом разделе описано, как создать пользователя Britta Simon в приложении Synergi. Обратитесь в службу поддержки Synergi, чтобы добавить пользователей на платформу Synergi.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Synergi.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Synergi, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **Synergi**.
   
    ![Настройка единого входа](./media/active-directory-saas-synergi-tutorial/tutorial_synergi_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Synergi на панели доступа, вы автоматически войдете в приложение Synergi.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-synergi-tutorial/tutorial_general_205.png

