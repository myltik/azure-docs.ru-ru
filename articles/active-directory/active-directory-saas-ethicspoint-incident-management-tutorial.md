---
title: "Руководство. Интеграция Azure Active Directory с решением EthicsPoint Incident Management (EPIM) | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и EthicsPoint Incident Management (EPIM)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 33a5e085722fd9accf70793a580b5c8a5dc124c4
ms.openlocfilehash: 83cfbd6a4297577dc5284982fcc5d0c1318fbf5c


---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Руководство. Интеграция Azure Active Directory с решением EthicsPoint Incident Management (EPIM)

В этом руководстве описано, как интегрировать решение EthicsPoint Incident Management (EPIM) с Azure Active Directory (Azure AD).

Интеграция EthicsPoint Incident Management (EPIM) с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD можно контролировать доступ к EthicsPoint Incident Management (EPIM).
- Вы можете включить автоматический вход пользователей в EthicsPoint Incident Management (EPIM) (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с EthicsPoint Incident Management (EPIM), вам потребуется следующее:

- подписка Azure AD;
- подписка EthicsPoint Incident Management (EPIM) с поддержкой единого входа.


>[!NOTE] 
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление решения EthicsPoint Incident Management (EPIM) из коллекции.
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Добавление решения EthicsPoint Incident Management (EPIM) из коллекции
Чтобы настроить интеграцию EthicsPoint Incident Management (EPIM) с Azure AD, вам потребуется добавить EthicsPoint Incident Management (EPIM) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить EthicsPoint Incident Management (EPIM) из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **EthicsPoint Incident Management**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_01.png)

7. В области результатов выберите **EthicsPoint Incident Management** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описывается настройка и проверка единого входа Azure AD в EthicsPoint Incident Management (EPIM) с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в EthicsPoint Incident Management (EPIM) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в EthicsPoint Incident Management (EPIM).

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в EthicsPoint Incident Management (EPIM).

Чтобы настроить и проверить единый вход Azure AD в EthicsPoint Incident Management (EPIM), вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя EthicsPoint Incident Management (EPIM)](#creating-a-ethicspoint-incident-management-test-user)** требуется для создания пользователя Britta Simon в EthicsPoint Incident Management (EPIM), связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале и настроить его в EthicsPoint Incident Management (EPIM).


**Чтобы настроить единый вход Azure AD в EthicsPoint Incident Management (EPIM), сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **EthicsPoint Incident Management (EPIM)** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в EthicsPoint Incident Management (EPIM)** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_04.png) 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, c помощью которого пользователи входят в приложение EthicsPoint Incident Management (EPIM), в следующем формате: `https://<companyname>.navexglobal.com` или `https://<companyname>.ethicspointvp.com`.
    
    b. В текстовое поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<servername>.navexglobal.com/adfs/ls/`.

    c. Щелкните **Далее**
 
4. На странице **Настройка единого входа в EthicsPoint Incident Management (EPIM)** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_05.png)

    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Для настройки единого входа в вашем приложении обратитесь в службу поддержки EthicsPoint Incident Management (EPIM) и предоставьте скачанный файл **метаданных**.

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
 
    ![единого входа Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-an-ethicspoint-incident-management-epim-test-user"></a>Создание тестового пользователя EthicsPoint Incident Management (EPIM)

В этом разделе описано, как создать пользователя Britta Simon в EthicsPoint Incident Management (EPIM). Чтобы добавить пользователя в платформу EthicsPoint Incident Management (EPIM), обратитесь в службу поддержки этого решения.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к EthicsPoint Incident Management (EPIM).

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в EthicsPoint Incident Management (EPIM), сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **EthicsPoint Incident Management (EPIM)**.

    ![Настройка единого входа](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку EthicsPoint Incident Management (EPIM) на панели доступа, вы автоматически войдете в приложение EthicsPoint Incident Management (EPIM).


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


