---
title: "Руководство по интеграции Azure Active Directory с Atlassian Cloud | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в приложении Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8928581d9636f571008f965185eeb61b414a16e7
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Руководство по интеграции Azure Active Directory с Atlassian Cloud

В этом руководстве описано, как интегрировать приложение Atlassian Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Atlassian Cloud обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Atlassian Cloud.
- Вы можете включить автоматический вход пользователей в Atlassian Cloud (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Atlassian Cloud, вам потребуется:

- подписка Azure AD;
- подписка Atlassian Cloud с поддержкой единого входа.


>[!NOTE] 
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. добавление Atlassian Cloud из коллекции;
2. Настройка и проверка единого входа Azure AD.


## <a name="add-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции
Чтобы настроить интеграцию Atlassian Cloud с Azure AD, необходимо добавить Atlassian Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Atlassian Cloud из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Atlassian Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. В области результатов выберите **Atlassian Cloud** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Atlassian Cloud с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Atlassian Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Atlassian Cloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Atlassian Cloud.

Чтобы настроить и проверить единый вход Azure AD в Atlassian Cloud, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Atlassian Cloud](#creating-Atlassian-cloud-test-user)** требуется для создания в Atlassian Cloud пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Atlassian Cloud.


**Чтобы настроить единый вход Azure AD в Atlassian Cloud, сделайте следующее:**

1. На странице интеграции с приложением **Atlassian Cloud** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Atlassian Cloud?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 
 1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Atlassian Cloud в следующем формате: `https://<instancename>.atlassian.net`    
 2. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://id.atlassian.com/login`

    >[!NOTE] 
    >Точное значение **идентификатора** можно получить на экране настройки SAML для Atlassian Cloud.
    >

 3. Нажмите кнопку **Далее**.
 
4. На странице **Настройка единого входа в Atlassian Cloud** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)
 1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
 2. Нажмите кнопку **Далее**.

5. Чтобы настроить единый вход для вашего приложения, войдите на портал Atlassian с правами администратора.

6. В левой области навигации в разделе Authentication (Проверка подлинности) выберите пункт **Domains** (Домены).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)
 1. В текстовом поле введите имя домена, а затем нажмите кнопку **Add domain** (Добавить домен).
        
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)
 2. Чтобы проверить домен, нажмите кнопку **Verify** (Проверить). 

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png) 
  3. Скачайте HTML-файл проверки домена, передайте его в корневую папку веб-сайта в домене и нажмите кнопку **Verify domain** (Проверить домен).
    
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)
  4. После проверки домена значение в поле **Status** (Cостояние) изменится на **Verified** (Проверено).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. В левой панели навигации щелкните **SAML**.
 
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. Создайте новую конфигурацию SAML и задайте параметры поставщика удостоверений:
  1. скопируйте значение идентификатора сущности из Azure AD и вставьте его в поле "Identity Provider Entity ID" (Идентификатор сущности поставщика удостоверений);
  2. скопируйте URL-адрес единого входа SAML и вставьте его в поле Identity Provider SSO URL (URL-адрес единого входа поставщика удостоверений);
  3. откройте скачанный сертификат из Azure AD в Блокноте, скопируйте значение без строк Begin и End и вставьте его в поле Public X509 certificate (Общий сертификат X509);
  4. Сохраните параметры.

      ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
 
9. В параметрах Azure AD задайте правильный URL-адрес идентификатора.
  1. скопируйте идентификатор удостоверения поставщика услуг на экране SAML и вставьте его в Azure AD в качестве значения поля **Идентификатор**;
  2. URL-адрес входа является URL-адресом клиента приложения Atlassian Cloud.     

     ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
 
    ![единого входа Azure AD][11]


### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-an-atlassian-cloud-test-user"></a>Создание тестового пользователя в Atlassian Cloud

В этом разделе описано, как создать пользователя Britta Simon в приложении Atlassian Cloud. Перед выполнением единого входа пользователь уже должен быть создан в приложении Atlassian Cloud. 

Выполните вход в экземпляр Atlassian Cloud с правами администратора и выполните приведенные ниже действия.

>[!NOTE] 
>Вы также можете создать случайных пользователей, нажав кнопку **Bulk Create** (Массовое создание) в разделе Users (Пользователи).

1. В разделе Site administration (Администрирование сайта) нажмите кнопку **Users** (Пользователи).

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Нажмите кнопку **Create User** (Создать пользователя), чтобы создать пользователя в приложении Atlassian Cloud.

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Введите адрес электронной почты, имя пользователя и полное имя и назначьте ему доступ к приложению. 

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Нажмите кнопку **Create user** (Создать пользователя). После этого пользователю будет отправлено приглашение по электронной почте. Как только пользователь примет приглашение, он станет активным в системе. 

### <a name="assig-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Atlassian Cloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Atlassian Cloud, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Atlassian Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Atlassian Cloud на панели доступа, вы автоматически войдете в приложение Atlassian Cloud.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png

