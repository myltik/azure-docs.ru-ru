---
title: "Руководство по интеграции Azure Active Directory с Novatus | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Novatus."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d2f13779-bdb7-4408-9738-be67ed3de4e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f14f89b10145787bb2729f741ce8620238a7f246


---
# <a name="tutorial-azure-active-directory-integration-with-novatus"></a>Руководство по интеграции Azure Active Directory с Novatus
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Novatus.

Интеграция Azure AD с приложением Novatus обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Novatus.
* Вы можете включить автоматический вход пользователей в Novatus (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Novatus, вам потребуется:

* подписка Azure AD;
* подписка Novatus с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. 

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Novatus из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-novatus-from-the-gallery"></a>Добавление Novatus из коллекции
Чтобы настроить интеграцию Novatus с Azure AD, необходимо добавить Novatus из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Novatus из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Novatus**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/tutorial_novatus_01.png)
7. В области результатов выберите **Novatus** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/tutorial_novatus_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Novatus с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Novatus соответствует пользователю в Azure AD. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Novatus.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Novatus.

Чтобы настроить и проверить единый вход Azure AD в Novatus, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Novatus](#creating-a-Novatus-test-user)** требуется для создания в Novatus пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Novatus.

**Чтобы настроить единый вход Azure AD в Novatus, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Novatus** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Novatus?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-novatus-tutorial/tutorial_novatus_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-novatus-tutorial/tutorial_novatus_04.png) 

    а. В текстовое поле "URL-адрес входа" введите URL-адрес, используемый для входа в приложение Novatus, в формате **https://sso.novatuscontracts.com/companyname**. Значение **companyname** в ссылке необходимо заменить фактическим именем.


1. На странице **Настройка единого входа в Novatus** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-novatus-tutorial/tutorial_novatus_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Novatus по адресу jvinci@novatusinc.com. Прикрепите к сообщению скачанный сертификат и укажите URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода), чтобы специалисты Novatus смогли настроить единый вход со своей стороны.
3. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-novatus-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-novatus-test-user"></a>Создание тестового пользователя Novatus
Цель этого раздела — создать пользователя с именем Britta Simon в Novatus. Приложение Novatus поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к Novatus будет создан пользователь (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь в службу поддержки Novatus.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Novatus, чтобы пользователь мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Novatus, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **Novatus**.
   
    ![Настройка единого входа](./media/active-directory-saas-novatus-tutorial/tutorial_novatus_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Novatus на панели доступа, вы автоматически войдете в приложение Novatus.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-novatus-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


