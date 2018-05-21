---
title: Руководство по интеграции Azure Active Directory с ThirdLight | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 95b5fb195f0f08eaa218536caf727c78826e1bc8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Учебник. Интеграция Azure Active Directory с ThirdLight

В этом учебнике описано, как интегрировать ThirdLight с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением ThirdLight обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к ThirdLight.
- Вы можете включить автоматический вход пользователей в ThirdLight (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ThirdLight, вам потребуется:

- подписка Azure AD;
- подписка ThirdLight с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ThirdLight из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-thirdlight-from-the-gallery"></a>Добавление ThirdLight из коллекции
Чтобы настроить интеграцию ThirdLight с Azure AD, необходимо добавить ThirdLight из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ThirdLight из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Thirdlight**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_search.png)

5. На панели результатов выберите **ThirdLight** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в ThirdLight с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в ThirdLight соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ThirdLight.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ThirdLight.

Чтобы настроить и проверить единый вход Azure AD в ThirdLight, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ThirdLight](#creating-a-thirdlight-test-user)** требуется для создания в ThirdLight пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении ThirdLight.

**Чтобы настроить единый вход Azure AD в ThirdLight, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **ThirdLight** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_samlbase.png)

3. В разделе **Домены и URL-адреса приложения ThirdLight** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.thirdlight.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.thirdlight.com/saml/sp`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов ThirdLight](https://www.thirdlight.com/support). 
 
4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-thirdlight-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на свой корпоративный веб-сайт ThirdLight в качестве администратора.

7. Выберите **Configuration (Конфигурация) \> System Administration (Системное администрирование)**, а затем щелкните **SAML2**.
   
    ![Администрирование системы](./media/active-directory-saas-thirdlight-tutorial/ic805843.png "Администрирование системы")

8. В разделе конфигурации SAML2 выполните следующие действия:
   
    ![Единый вход SAML](./media/active-directory-saas-thirdlight-tutorial/ic805844.png "Единый вход SAML")   

     a. Выберите параметр **Разрешить единый вход SAML2**.
 
     Б. В качестве **источника для метаданных IdP** выберите **Load IdP Metadata from XML** (Загрузить метаданные IdP из XML).
 
     c. Откройте скачанный файл метаданных, скопируйте его содержимое и вставьте его в текстовое поле **XML с метаданными IdP** . 
     
     d. Щелкните **Сохранить параметры SAML2**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-thirdlight-test-user"></a>Создание тестового пользователя ThirdLight

Чтобы пользователи Azure AD могли выполнять вход в систему ThirdLight, они должны быть подготовлены для нее.  
В случае с ThirdLight подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на веб-сайт **ThirdLight** вашей компании в качестве администратора.

2. Перейдите на вкладку **Пользователи** .

3. Выберите пункт **Пользователи и группы**.

4. Щелкните **Добавить пользователя** .

5. Введите **имя пользователя, имя или описание, адрес электронной почты, выберите предустановку или группу новых участников** для действующей учетной записи AAD, которую необходимо подготовить.

6. Нажмите кнопку **Создать**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Thirdlight или API-интерфейсы, предоставляемые Thirdlight для подготовки учетных записей пользователей AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к ThirdLight.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ThirdLight, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **ThirdLight**.

    ![Настройка единого входа](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент ThirdLight на панели доступа, вы автоматически войдете в приложение ThirdLight.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_203.png

