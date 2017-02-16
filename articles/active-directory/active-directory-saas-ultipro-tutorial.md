---
title: "Руководство. Интеграция Azure Active Directory с UltiPro | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и UltiPro."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: afc0f2b9-2eac-47ec-af04-65ed0fb0ca5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 33f4c13930017bdcbaf899b16d32ba84bdfbf9ee
ms.openlocfilehash: e531ece88b85b9e91810909f088c1c08fa92415e


---
# <a name="tutorial-azure-active-directory-integration-with-ultipro"></a>Руководство. Интеграция Azure Active Directory с UltiPro
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением UltiPro.  
Интеграция Azure AD с приложением UltiPro обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к приложению UltiPro.
* Вы можете включить автоматический вход пользователей в UltiPro (единый вход) с использованием их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с UltiPro, вам потребуется:

* подписка Azure AD;
* подписка UltiPro с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление UltiPro из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-ultipro-from-the-gallery"></a>Добавление UltiPro из коллекции
Чтобы настроить интеграцию UltiPro с Azure AD, необходимо добавить UltiPro из коллекции в список управляемых приложений SaaS.

**Чтобы добавить UltiPro из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **UltiPro**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_01.png)
7. В области результатов выберите **Ultipro** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в UltiPro с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в UltiPro соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в UltiPro.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в UltiPro.

Чтобы настроить и проверить единый вход Azure AD в UltiPro, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя UltiPro](#creating-a-ultipro-test-user)** требуется для создания в UltiPro пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение UltiPro.

**Чтобы настроить единый вход Azure AD в UltiPro, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **UltiPro** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Ultipro** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_04.png) 

    а. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение UltiPro, в следующем формате: **https://\<название_организации\>.ultipro.com**.

    b. Нажмите кнопку **Далее**.

1. На странице **Настройка единого входа в Ultipro** сделайте следующее.
   
    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
2. Для настройки единого входа для своего приложения обратитесь к менеджеру по работе с клиентами UltiPro и предоставьте следующие сведения:
   
   * Скачанный файл сертификата
   *  **URL-адрес издателя**
   *  **URL-адрес единого входа SAML** 
   *  **URL-адрес службы единого выхода**
3. На классическом портале Azure подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-ultipro-test-user"></a>Создание тестового пользователя UltiPro
Цель этого раздела — создать пользователя с именем Britta Simon в Ultipro. Обратитесь в службу поддержки Ultipro, чтобы добавить пользователей в учетную запись Ultipro. 

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки UltiPro.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к UltiPro.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению UltiPro, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **UltiPro**.
   
    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент UltiPro на панели доступа, вы автоматически войдете в приложение UltiPro.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


