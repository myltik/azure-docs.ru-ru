---
title: "Руководство по интеграции Azure Active Directory с SAP NetWeaver | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в SAP NetWeaver."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2793f2b59207d4c329b2fd76eb164b3c4237b9e3


---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Руководство. Интеграция Azure Active Directory с SAP NetWeaver
В этом руководстве описано, как интегрировать SAP NetWeaver с Azure Active Directory.

Интеграция SAP NetWeaver с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к приложению SAP NetWeaver.
* Вы можете включить автоматический вход пользователей в SAP NetWeaver (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с SAP NetWeaver, вам потребуется:

* подписка Azure AD;
* подписка на SAP NetWeaver с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SAP NetWeaver из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-sap-netweaver-from-the-gallery"></a>Добавление SAP NetWeaver из коллекции
Чтобы настроить интеграцию SAP NetWeaver с Azure AD, необходимо добавить SAP NetWeaver из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP NetWeaver из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **SAP NetWeaver**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_01.png)
7. В области результатов выберите **SAP NetWeaver** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в SAP NetWeaver с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP NetWeaver соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP NetWeaver.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP NetWeaver.

Чтобы настроить и проверить единый вход Azure AD в SAP NetWeaver, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP NetWeaver](#creating-a-sap-netweaver-test-user)** требуется для создания пользователя Britta Simon в SAP NetWeaver, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении SAP NetWeaver.

**Чтобы настроить единый вход Azure AD в SAP NetWeaver, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **SAP NetWeaver** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в SAP NetWeaver?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_04.png) 
   
    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение SAP NetWeaver, в следующем формате: **https://\<экземпляр компании SAP NetWeaver\>**.
   
    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: **https://\<экземпляр компании SAP NetWeaver\>**.
   
    В. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: **https://\<экземпляр компании SAP NetWeaver\>/sap/saml2/sp/acs/100**.
   
   > [!NOTE]
   > Все эти значения можно найти в документе метаданных федерации, предоставленном партнером SAP NetWeaver.
   > 
   > 
   
    d. click **Далее**
4. На странице **Настройка единого входа в SAP NetWeaver** сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_05.png)
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Чтобы настроить единый вход для приложения, обратитесь к партнеру SAP NetWeaver и предоставьте следующие сведения:
   
    • скачанный файл **метаданных**
   
    • **идентификатор сущности**
   
    • **URL-адрес единого входа SAML**
   
    • **URL-адрес службы единого выхода**
6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-an-sap-netweaver-test-user"></a>Создание тестового пользователя SAP NetWeaver
В этом разделе описано, как создать пользователя Britta Simon в приложении SAP NetWeaver. Обратитесь к партнеру SAP NetWeaver, чтобы добавить пользователей на платформу SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к SAP NetWeaver.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении SAP NetWeaver, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **SAP NetWeaver**.
   
    ![Настройка единого входа](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP NetWeaver на панели доступа, вы автоматически войдете в приложение SAP NetWeaver.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


