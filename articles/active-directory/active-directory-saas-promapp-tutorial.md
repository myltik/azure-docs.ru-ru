---
title: "Руководство по интеграции Azure Active Directory с Promapp | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Promapp."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: ac73b5c2ece8044d9f75e017428c43259f8a9357


---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Руководство по интеграции Azure Active Directory с Promapp
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Promapp.  
Интеграция Azure AD с приложением Promapp обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к Promapp. 
* Вы можете включить автоматический вход пользователей в Promapp (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Promapp, вам потребуется:

* подписка Azure AD;
* подписка Promapp с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Promapp из коллекции 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-promapp-from-the-gallery"></a>Добавление Promapp из коллекции
Чтобы настроить интеграцию Promapp с Azure AD, необходимо добавить Promapp из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Promapp из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Promapp**.
   
    ![Приложения][5]
7. В области результатов выберите **Promapp** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Promapp с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Promapp соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Promapp.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Promapp.

Чтобы настроить и проверить единый вход Azure AD в Promapp, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Promapp](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в Promapp, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Promapp.

**Чтобы настроить единый вход Azure AD в Promapp, выполните следующие действия.**

1. На классическом портале Azure AD на странице интеграции с приложением **Promapp** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Promapp?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![единого входа Azure AD][8] 
   
   1. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт Promapp (например, *https://companyname.promapp.com/instancename*).
   2. Нажмите кнопку **Далее**.

1. На странице **Настройка единого входа в Promapp** выполните следующие действия.
   
    ![единого входа Azure AD][9] 
   
   1. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.
   2. Нажмите кнопку **Далее**.
   
2. Войдите на корпоративный сайт Promapp с правами администратора. 
3. В верхнем меню щелкните **Администратор**. 
   
    ![Единый вход в Azure AD][12]
4. Нажмите **Настроить**. 
   
    ![Единый вход в Azure AD][13]
5. В диалоговом окне **Security** (Безопасность) сделайте следующее.
   
    ![единого входа Azure AD][14] 
   
   1. На классическом портале Azure в диалоговом окне **Настройка единого входа в Promapp** скопируйте значение поля **URL-адрес удаленного входа**, вставьте его в текстовое поле **SSO-Login URL** (URL-адрес единого входа) и нажмите кнопку **Сохранить**.
   2. В поле **SSO - Single Sign-on Mode** (SSO — режим единого входа) выберите **Optional** (Необязательно), а затем нажмите кнопку **Save** (Сохранить).
   3. Откройте скачанный сертификат в блокноте, скопируйте содержимое сертификата без первой строки (*-----BEGIN CERTIFICATE-----*) и последней строки (*-----END CERTIFICATE-----*), вставьте его в текстовое поле **SSO-x.509 Certificate** (Сертификат единого входа x.509) и нажмите кнопку **Save** (Сохранить).
6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)  
   
   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
   
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.   
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
   
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) 
   
   1. Запишите значение поля **Новый пароль**.
   2. Нажмите **Завершено**.   

### <a name="creating-a-promapp-test-user"></a>Создание тестового пользователя Promapp
Приложение Promapp поддерживает JIT-подготовку.
Это значит, что учетная запись пользователя при необходимости создается автоматически во время попытки доступа к приложению с помощью панели доступа.  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Promapp.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Promapp, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Promapp**.
   
    ![Назначение пользователя][202] 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку Promapp на панели доступа, вы автоматически войдете в приложение Promapp.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png



<!--HONumber=Feb17_HO1-->


