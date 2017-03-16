---
title: "Руководство по интеграции Azure Active Directory с Asana | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Asana."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 257c1032ed8f1347c17be7e51021b9284b73f4a2
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Руководство. Интеграция Azure Active Directory с Asana
В этом руководстве описано, как интегрировать Asana с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Asana обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Asana.
* Вы можете включить автоматический вход пользователей в Asana (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Asana, вам потребуется:

* подписка Azure AD;
* подписка на **Asana** с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Asana из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-asana-from-the-gallery"></a>Добавление Asana из коллекции.
Чтобы настроить интеграцию Asana с Azure AD, необходимо добавить Asana из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Asana из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Asana**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)
7. В области результатов выберите **Asana** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе можно настроить и проверить единый вход Azure AD в Asana с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Asana соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Asana.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Asana.

Чтобы настроить и проверить единый вход Azure AD в Asana, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Asana](#creating-an-Asana-test-user)** требуется для создания в Asana пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Asana.

**Чтобы настроить единый вход Azure AD в Asana, выполните следующие действия.**

1. В верхнем меню щелкните **Быстрый запуск**.
   
    ![Настройка единого входа][6]
2. На классическом портале на странице интеграции с приложением **Asana** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7] 
3. На странице **Как пользователи должны входить в Asana?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)
4. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия. 
   
    ![Настройка единого входа](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)
  1. В текстовом поле "URL-адрес для входа" введите URL-адрес в следующем формате: `https://app.asana.com`.
  2. Нажмите кнопку **Далее**.
5. На странице **Настройка единого входа в Asana** щелкните **Скачать сертификат**, а затем сохраните файл на своем компьютере. Кроме того, скопируйте значение URL-адреса единого входа SAML.
   
    ![Настройка единого входа](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)
6. Щелкните сертификат правой кнопкой мыши, а затем откройте файл сертификата в Блокноте или в любом текстовом редакторе. Скопируйте содержимое между маркерами начала и окончания сертификата. Это сертификат X.509, который будет использоваться в Asana для настройки единого входа.
7. В другом окне браузера войдите в приложение Asana. Чтобы настроить единый вход в Asana, перейдите к параметрам рабочей области, щелкнув имя рабочей области в правом верхнем углу экрана. Щелкните элемент **\<your workspace name\> Settings** (Параметры <имя_рабочей_области>). 
   
    ![Настройка единого входа](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)
8. В окне **Organization settings** (Параметры организации) щелкните **Administration** (Администрирование). Затем щелкните **Members must log in via SAML** (Участники должны входить с помощью SAML), чтобы активировать конфигурацию единого входа. Выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  
  1. В текстовое поле **Sign-in page URL** (URL-адрес страницы входа) скопируйте URL-адрес единого входа SAML из Azure AD.
  2. В текстовое поле **X.509 Certificate** (Сертификат X.509) вставьте сертификат X.509, который вы скопировали из Azure AD.
9. Щелкните **Сохранить**. Если вам нужна дополнительная помощь, изучите [руководство по настройке единого входа в Asana](https://asana.com/guide/help/premium/authentication#gl-saml) .
10. На портале Azure AD откройте страницу **Настройка единого входа в Asana**, выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Единый вход в Azure AD][10]
11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Единый вход в Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.   
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**. 
  2. Нажмите **Завершено**.   

### <a name="create-an-asana-test-user"></a>Создание тестового пользователя Asana
В этом разделе описано, как создать пользователя Britta Simon в приложении Asana.

1. В приложении **Asana** откройте раздел **Teams** (Группы) на левой панели. Нажмите кнопку со знаком "плюс". 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 
2. Введите адрес электронной почты britta.simon@contoso.com в текстовое поле и выберите **Invite**(Пригласить).
3. Щелкните **Send Invite**(Отправить приглашение). Новый пользователь получит сообщение электронной почты. С его помощью пользователь должен создать и подтвердить учетную запись.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Asana, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Asana, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Asana**.
   
    ![Настройка единого входа](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке "Все пользователи" выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить работу единого входа Azure AD.

Перейдите на страницу входа в Asana. Вставьте адрес электронной почты britta.simon@contoso.com в соответствующее текстовое поле. Текстовое поле пароля оставьте пустым и щелкните **Log In** (Вход). Вы перейдете на страницу входа в Azure AD. Введите учетные данные Azure AD. Теперь вы должны войти в Asana.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png

