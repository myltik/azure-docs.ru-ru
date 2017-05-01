---
title: "Руководство по интеграции Azure Active Directory с Weekdone | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Weekdone."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 34921f9a-5637-4420-ab4c-9beb34421909
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 4172a4e4c7add3fc75d1c83e32e41ea9a596b647
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-weekdone"></a>Руководство. Интеграция Azure Active Directory с Weekdone
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Weekdone.

Интеграция Weekdone с Azure AD дает приведенные ниже преимущества.

* С помощью Azure AD вы можете контролировать доступ к Weekdone.
* Вы можете включить автоматический вход пользователей в Weekdone (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Weekdone, вам потребуется:

* подписка Azure AD;
* подписка на Weekdone с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Weekdone из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-weekdone-from-the-gallery"></a>Добавление Weekdone из коллекции
Чтобы настроить интеграцию Weekdone с Azure AD, необходимо добавить приложение Weekdone из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Weekdone из коллекции, выполните указанные ниже действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Weekdone**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_01.png)
7. В области результатов выберите **Weekdone** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Weekdone с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Weekdone соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Weekdone.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Weekdone.

Чтобы настроить и проверить единый вход Azure AD в Weekdone, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Weekdone](#creating-a-weekdone-test-user)** требуется для создания пользователя Britta Simon в Weekdone, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Weekdone.

**Чтобы настроить единый вход Azure AD в Weekdone, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Weekdone** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Weekdone** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_03.png) 
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_04.png) 

   1. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: **https://weekdone.com/a/azure**.
   2. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: **https://weekdone.com/a/azure/metadata**.
   3. Нажмите кнопку **Далее**.
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, на странице с диалоговым окном **Настроить параметры приложения** установите флажок **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес входа** и **идентификатор** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_06.png) 
   
   1. В текстовом поле **URL-адрес входа** введите URL-адрес, применяемый пользователями для входа в приложение Weekdone, в следующем формате: **https://weekdone.com/a/azure**.
   2. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: **https://weekdone.com/a/azure/metadata**.
   3. Нажмите кнопку **Далее**.
2. На странице **Настройка единого входа в Weekdone** выполните указанные ниже действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_05.png) 
   1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   2. Нажмите кнопку **Далее**.
    
3. Чтобы настроить единый вход для приложения, обратитесь в службу поддержки Weekdone по адресу hello@weekdone.com. 
4. Вложите в сообщение скачанный сертификат и укажите URL-адреса метаданных (URL-адрес издателя, URL-адрес единого входа SAML и URL-адрес службы единого выхода), чтобы специалисты Weekdone смогли настроить единый вход со своей стороны.
5. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
6. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_09.png) 
    
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_03.png) 
    
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_04.png) 
    
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_05.png) 
   
   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
    
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
  
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_07.png) 
    
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_08.png) 
   
   1. Запишите значение поля **Новый пароль**. 
   2. Нажмите **Завершено**.   

### <a name="create-a-weekdone-test-user"></a>Создание тестового пользователя Weekdone
Цель этого раздела — создать пользователя с именем Britta Simon в Weekdone. Приложение Weekdone поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Weekdone (если он еще не создан). [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки Weekdone по адресу hello@weekdone.com.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Weekdone, чтобы этот пользователь мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Weekdone, выполните указанные ниже действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Weekdone**.
   
    ![Настройка единого входа](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Weekdone на панели доступа, вы автоматически войдете в приложение Weekdone.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_205.png

