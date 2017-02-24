---
title: "Учебник. Интеграция Azure Active Directory с Deputy | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Deputy."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: c853d61257493d73cd0f8a51a15f0389e1e83cf4


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Руководство. Интеграция Azure Active Directory с Deputy
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Deputy.

Интеграция Azure AD с приложением Deputy обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Deputy.
* Вы можете включить автоматический вход пользователей в Deputy (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Deputy, вам потребуется:

* подписка Azure AD;
* подписка Deputy с поддержкой единого входа.

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

1. Добавление Deputy из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-deputy-from-the-gallery"></a>Добавление Deputy из коллекции
Чтобы настроить интеграцию Deputy с Azure AD, необходимо добавить Deputy из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Deputy из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Deputy**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. На панели результатов выберите **Deputy** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Deputy с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Deputy соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Deputy.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Deputy.

Чтобы настроить и проверить единый вход Azure AD в Deputy, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Deputy](#creating-a-deputy-test-user)** требуется для создания пользователя Britta Simon в Deputy, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Deputy.

**Чтобы настроить единый вход Azure AD в Deputy, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Deputy** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Deputy?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
  1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<your-subdomain>.<region>.deputy.com`.
  2. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.
  3. Нажмите кнопку **Далее**.
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<your-subdomain>.<region>.deputy.com`.
   2. Нажмите кнопку **Далее**.
   
   > [!NOTE]
   > Суффикс региона Deputy необязательный, или следует использовать один из следующих: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an.
   > 
   > 
5. На странице **Настройка единого входа в Deputy** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
6. Перейдите по следующему URL-адресу: https://(your-subdomain).deputy.com/exec/config/system_config. Выберите пункт **Параметры безопасности** и нажмите кнопку **Изменить**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. На классическом портале Azure на странице "Настройка единого входа в Deputy" скопируйте URL-адрес единого входа SAML. 
8. На странице **Параметры безопасности** сделайте следующее:
   
![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

   1. разрешите **вход с помощью учетных записей социальных сетей**;
   2. откройте сертификат в кодировке Base64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **OpenSSL Certificate** (Сертификат OpenSSL);
   3. в текстовом поле SAM SSO URL (URL-адрес единого входа) введите `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`;
   4. В текстовом поле SAM SSO URL (URL-адрес единого входа) замените `<your subdomain>` на поддомен.
   5. В текстовом поле SAM SSO URL (URL-адрес единого входа) замените `<saml sso url>` на URL-адрес единого входа SAML, скопированный c классического портала Azure.
   6. Нажмите кнопку **Сохранить параметры**.
9. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
   
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
   1. Запишите значение поля **Новый пароль**.
   2. Нажмите **Завершено**.   

### <a name="creating-a-deputy-test-user"></a>Создание тестового пользователя приложения Deputy
Чтобы пользователи Azure AD могли выполнять вход в Deputy, они должны быть подготовлены для Deputy. В случае с Deputy подготовка выполняется вручную.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Чтобы подготовить учетную запись пользователя, выполните следующие действия.
1. Войдите на сайт Deputy компании в качестве администратора.
2. В верхней части области навигации щелкните **People**(Люди).
   
   ![Люди](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Люди")
3. Нажмите кнопку **Add People** (Добавить людей) и выберите пункт **Add a single person** (Добавить одного человека).
   
   ![Добавить участников](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "добавить участников")
4. Выполните следующие действия и нажмите кнопку **Save & Invite** (Сохранить и пригласить).
   
   ![Новый пользователь](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Новый пользователь")
   
  1. В текстовом поле **Name** (Имя) введите **Britta** и **Simon**.  
  2. В текстовое поле **Email** (Электронная почта) введите адрес электронной почты той учетной записи Azure AD, которую нужно подготовить.
  3. В текстовом поле **Work at** (Место работы) введите название компании.
  4. Нажмите кнопку **Save & Invite** (Сохранить и пригласить).
   
   > [!NOTE]
   > Владелец учетной записи Azure AD получит электронное сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие средства создания учетной записи пользователя Deputy или API, предоставляемые Deputy для подготовки учетных записей пользователя AAD.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Deputy, чтобы пользователь мог использовать единый вход Azure.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Deputy, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Deputy**.
   
    ![Настройка единого входа](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Deputy на панели доступа, вы автоматически войдете в приложение Deputy.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


