---
title: "Руководство по интеграции Azure Active Directory со SkyDesk Email | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в SkyDesk Email."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: fcea07a412de7b35931ff95b01fbe1276302f1ea
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Учебник. Интеграция Azure Active Directory со SkyDesk Email
Цель этого учебника — показать, как интегрировать SkyDesk Email с Azure Active Directory (Azure AD).

Интеграция SkyDesk Email с Azure AD дает приведенные ниже преимущества.

* С помощью Azure AD вы можете контролировать доступ к SkyDesk Email.
* Вы можете включить автоматический вход пользователей в SkyDesk Email (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD со SkyDesk Email, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа SkyDesk Email.

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

1. Добавление SkyDesk Email из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-skydesk-email-from-the-gallery"></a>Добавление SkyDesk Email из коллекции
Чтобы настроить интеграцию SkyDesk Email с Azure AD, необходимо добавить приложение SkyDesk Email из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SkyDesk Email из коллекции, выполните указанные ниже действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **SkyDesk Email**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)
7. В области результатов выберите **SkyDesk Email** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход в Azure AD в SkyDesk Email с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SkyDesk Email соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SkyDesk Email.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SkyDesk Email.

Чтобы настроить и проверить единый вход в Azure AD в SkyDesk Email, вам потребуется выполнить действия в приведенных ниже стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SkyDesk Email](#creating-a-Skydesk-Email-test-user)** требуется для создания пользователя Britta Simon в SkyDesk Email, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход в Azure AD на классическом портале Azure и настроить единый вход в приложение SkyDesk Email.

**Чтобы настроить единый вход в Azure AD в SkyDesk Email, выполните указанные ниже действия.**

1. На классическом портале Azure на странице интеграции с приложением **SkyDesk Email** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в SkyDesk Email?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png) 

   1. В текстовом поле "URL-адрес входа" введите URL-адрес, применяемый пользователями для входа в приложение SkyDesk Email, по следующему шаблону: **https://mail.skydesk.jp/portal/\<название_организации\>**.
   2. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в SkyDesk Email** выполните указанные ниже действия.
   
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
    b. Нажмите кнопку **Далее**.
5. Чтобы включить единый вход в **SkyDesk Email**, выполните указанные ниже действия.
   1. Войдите в учетную запись SkyDesk Email от имени администратора.
   2. В меню в верхней части страницы нажмите Setup (Настройка) и выберите пункт Org (Организация). 
    
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)  
   3. На панели слева выберите пункт Domains (Домены).
    
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)
   4. Щелкните Add Domain (Добавить домен).
    
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)
   5. Введите доменное имя, а затем проверьте домен.
    
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)
   6. На панели слева выберите пункт **SAML Authentication** (Аутентификация SAML).
    
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)
6. На странице **SAML Authentication** выполните указанные ниже действия.
   
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
   > [!NOTE]
   > Для использования проверки подлинности на основе SAML у вас должен быть настроен **проверенный домен** или **URL-адрес портала**. Для URL-адреса портала можно настроить уникальное имя.
   > 
   > 
   
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

   1. На классическом портале Azure AD скопируйте значение поля **URL-адрес единого входа SAML**, а затем вставьте его в текстовое поле **URL-адрес входа**.
   2. На классическом портале Azure AD скопируйте значение поля **URL-адрес службы единого входа**, а затем вставьте его в текстовое поле **URL-адрес выхода**.
   3. **Изменить URL-адрес пароля** является необязательным, поэтому оставьте его пустым.
   4. Щелкните **Get Key From File** (Получить ключ из файла), чтобы выбрать загруженный сертификат SkyDesk Email, а затем нажмите кнопку **Открыть**, чтобы отправить сертификат.
   5. В поле **Algorithm** (Алгоритм) задайте значение **RSA**.
   6. Нажмите кнопку **ОК** , чтобы сохранить изменения.

7. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
8. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**. 
   2. В текстовом поле **Фамилия** введите **Simon**.  
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png) 
   
   1. Запишите значение поля **Новый пароль**.
   2. Нажмите **Завершено**.   

### <a name="creating-a-skydesk-email-test-user"></a>Создание тестового пользователя SkyDesk Email
В этом разделе описано, как создать пользователя Britta Simon в приложении SkyDesk Email.

1. На левой панели в SkyDesk Email выберите пункт **User Access** (Доступ пользователя), а затем введите свое имя пользователя. 

![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Если вам нужно массово создать пользователей, обратитесь в службу поддержки SkyDesk Email.
>


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SkyDesk Email.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SkyDesk Email, выполните указанные ниже действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **SkyDesk Email**.
   
    ![Настройка единого входа](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
![Назначение пользователя][203] 
4. Из списка пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SkyDesk Email на панели доступа, вы автоматически войдете в приложение SkyDesk Email.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

