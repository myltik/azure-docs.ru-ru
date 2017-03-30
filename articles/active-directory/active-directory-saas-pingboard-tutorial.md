---
title: "Руководство по интеграции Azure Active Directory с PingBoard | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и PingBoard."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: d9daa59b1f20a76dfe18390ffdab008245387332
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Руководство. Интеграция Azure Active Directory с PingBoard

В этом руководстве описано, как интегрировать PingBoard с Azure Active Directory (Azure AD).

Интеграция Azure AD с PingBoard обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к PingBoard.
- Вы можете включить автоматический вход пользователей в PingBoard (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с PingBoard, вам потребуется:

- подписка Azure AD;
- подписка PingBoard с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление PingBoard из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-pingboard-from-the-gallery"></a>Добавление PingBoard из коллекции
Чтобы настроить интеграцию PingBoard с Azure AD, необходимо добавить PingBoard из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PingBoard из коллекции, выполните следующие действия.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **PingBoard**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_search.png)

5. На панели результатов выберите **PingBoard** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в PingBoard с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в PingBoard соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PingBoard.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PingBoard.

Чтобы настроить и проверить единый вход Azure AD в PingBoard, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя PingBoard](#creating-a-pingboard-test-user)** требуется для создания пользователя Britta Simon в PingBoard, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении PingBoard.

**Чтобы настроить единый вход Azure AD в PingBoard, выполните следующие действия.**

1. На портале управления Azure на странице интеграции с приложением **PingBoard** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения PingBoard** выполните следующее.

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/tutorial_PingBoard_url.png)

    а. В текстовом поле **Идентификатор** введите значение `http://<entity-id>.pingboard.com/sp`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<entity-id>.pingboard.com/auth/saml/consume`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо указать фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов PingBoard](https://support.pingboard.com/). 

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если вы хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/tutorial_pingboard_sp_initiated01.png)

    а. В текстовом поле **URL-адрес для входа** введите значение `http://<sub-domain>.pingboard.com/sign_in`.
     
5. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/tutorial_pingboard_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне PingBoard, откройте новое окно браузера и войдите в учетную запись PingBoard. Для настройки единого входа нужны права администратора PingBoard.

8. В верхнем меню выберите **Apps > Integrations** ("Приложения" > "Интеграция").

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/pingboard_integration.png)

9.    На странице **Integrations** (Интеграция) найдите элемент **Azure Active Directory** и щелкните его.

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/pingboard_aad.png)

10. В появившемся модальном окне щелкните **Configure** (Настройка).

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/pingboard_configure.png)

11. На следующей странице вы увидите сообщение "Azure SSO Integration is enabled" (Интеграция для единого входа Azure включена). Откройте скачанный XML-файл метаданных в Блокноте и вставьте его содержимое в поле **IDP Metadata** (Метаданные IdP).

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/pingboard_sso_configure.png)

12. Файл будет проверен, и если он пройдет проверку, то единый вход будет включен.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-PingBoard-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-pingboard-test-user"></a>Создание тестового пользователя PingBoard

Чтобы пользователи Azure AD могли входить в PingBoard, их необходимо подготовить в PingBoard.  
В случае с PingBoard подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Выполните вход на корпоративный сайт PingBoard в качестве администратора.

2. Нажмите кнопку **Add Employee** (Добавить сотрудника) на странице **Directory** (Каталог).

    ![Добавление сотрудника](./media/active-directory-saas-PingBoard-tutorial/create_testuser_add.png)

3. На диалоговой странице **Add Employee** (Добавление сотрудника) выполните следующие действия.

    ![Приглашение пользователей](./media/active-directory-saas-PingBoard-tutorial/create_testuser_name.png)

    А. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя Britta Simon.

    b. В текстовом поле **Электронная почта** введите адрес электронной почты учетной записи Britta Simon.

    c. В текстовом поле **Job Title** (Должность) введите должность пользователя Britta Simon.

    d. Из раскрывающегося списка **Location** (Расположение) выберите расположение пользователя Britta Simon.
    
    д. Щелкните **Добавить**.    

4. Откроется окно подтверждения добавления пользователя.
    
    ![Подтверждение](./media/active-directory-saas-PingBoard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к PingBoard.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в PingBoard, выполните следующие действия.**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **PingBoard**.

    ![Настройка единого входа](./media/active-directory-saas-PingBoard-tutorial/tutorial_pingboard_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "PingBoard" на панели доступа, вы автоматически войдете в приложение PingBoard.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-PingBoard-tutorial/tutorial_general_203.png

