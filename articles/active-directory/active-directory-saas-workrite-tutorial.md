---
title: "Руководство по интеграции Azure Active Directory с Workrite | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Workrite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2a5c2956-a011-4d5c-877b-80679b6587b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a410fb385ed2960139987a71236b7f84a32c8099
ms.openlocfilehash: 5e640983825fb521a00b84518eca76f007224269
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>Руководство. Интеграция Azure Active Directory с Workrite
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Workrite.

Интеграция Workrite с Azure AD обеспечивает следующие преимущества: 

* с помощью Azure AD вы можете контролировать доступ к Workrite; 
* Вы можете включить автоматический вход пользователей в Workrite (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Workrite, вам потребуется:

* подписка Azure AD;
* подписка на Workrite с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление Workrite из коллекции 
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-workrite-from-the-gallery"></a>Добавление Workrite из коллекции
Чтобы настроить интеграцию Workrite с Azure AD, необходимо добавить Workrite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workrite из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Workrite**.
   
    ![Приложения][5]
7. В области результатов выберите **Workrite** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][500]

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Workrite с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Workrite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workrite.  

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Workrite.

Чтобы настроить и проверить единый вход Azure AD в Workrite, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Workrite](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в Workrite, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить его в приложении Workrite.

**Чтобы настроить единый вход Azure AD в Workrite, сделайте следующее:**

1. На странице интеграции с приложением **Workrite** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Workrite** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Единый вход в Azure AD][8] 
  1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт Workrite (например, *https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*).

    >[!NOTE]
    >Обратитесь в службу поддержки Workrite ( [support@workrite.co.uk](mailto:support@workrite.co.uk) ), если вы не знаете URL-адрес входа. 
    >   
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Workrite** выполните следующие действия.
   
    ![Единый вход в Azure AD][9] 
 1. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.  
 2. Обратитесь в службу поддержки Workrite по адресу [support@workrite.co.uk](mailto:support@workrite.co.uk), предоставьте ее сотруднику скачанный сертификат, **URL-адрес издателя** (идентификатор сущности), **URL-адрес службы единого входа**, **URL-адрес единого выхода**, а затем попросите его настроить единый вход для вашего приложения Workrite.  
 3. Нажмите кнопку **Далее**.
5. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![единого входа Azure AD][10]
6. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Единый вход в Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png)  
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png)  
 1. В текстовом поле **Имя** введите **Britta**.   
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**.
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png)  
 1. Запишите значение поля **Новый пароль**.  
 2. Нажмите **Завершено**.   

### <a name="create-a-workrite-test-user"></a>Создание тестового пользователя Workrite
Цель этого раздела — создать пользователя с именем Britta Simon в Workrite.

**Чтобы создать пользователя с именем Britta Simon в Workrite, выполните следующие действия.**

1. Выполните вход на сайт компании Workrite в качестве администратора.
2. В области навигации щелкните **Администратор**.
   
    ![Назначение пользователя][400]
3. Перейдите в раздел быстрых ссылок и нажмите кнопку **Создать пользователя**. 
   
    ![Назначение пользователя][401]
4. В диалоговом окне **Создание пользователя** сделайте следующее:
   
    ![Назначение пользователя][402]
 1. Заполните поля **Email** (Адрес электронной почты), **First Name** (Имя) и **Surname** (Фамилия) данными действительного пользователя Azure AD, которого необходимо подготовить.  
 2. Выберите значение **Client Administrator** (Администратор клиента) в поле **Choose Role** (Выберите роль).  
 3. Щелкните **Сохранить**.   

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Workrite, чтобы этот пользователь мог использовать единый вход Azure.

    ![Assign User][200] 

**Чтобы назначить пользователя Britta Simon в Workrite, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Workrite**.
   
    ![Назначение пользователя][202] 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Workrite на панели доступа, вы автоматически войдете в приложение Workrite.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png





