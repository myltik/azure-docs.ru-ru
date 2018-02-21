---
title: "Руководство по интеграции Azure Active Directory с ADP Federated SSO | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в ADP Federated SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: jeedes
ms.openlocfilehash: 76bbee514eac36aec8eaf84e8a20f85a27559fc7
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp-federated-sso"></a>Руководство по интеграции Azure Active Directory с ADP Federated SSO

В этом руководстве описано, как интегрировать ADP Federated SSO с Azure Active Directory (Azure AD).

Интеграция ADP Federated SSO с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к ADP Federated SSO.
- Вы можете включить автоматический вход пользователей в ADP Federated SSO (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ADP Federated SSO, вам потребуется:

- подписка Azure AD;
- подписка на ADP Federated SSO с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ADP Federated SSO из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-adp-federated-sso-from-the-gallery"></a>Добавление ADP Federated SSO из коллекции
Чтобы настроить интеграцию ADP Federated SSO с Azure AD, необходимо добавить ADP Federated SSO из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение ADP Federated SSO из коллекции, сделайте следующее:**

1.  Войдите в среду поставщика удостоверений Microsoft Azure с правами администратора.

2. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

3. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
4. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

5. В поле поиска введите **ADP Federated SSO**, выберите **ADP Federated SSO** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ADP Federated SSO в списке результатов](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ADP Federated SSO с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в ADP Federated SSO соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ADP Federated SSO.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ADP Federated SSO.

Чтобы настроить и проверить единый вход Azure AD в ADP Federated SSO, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ADP Federated SSO](#create-an-adp-federated-sso-test-user)** требуется для того, чтобы в ADP Federated SSO существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении ADP Federated SSO.

**Чтобы настроить единый вход Azure AD в ADP Federated SSO, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **ADP Federated SSO** щелкните вкладку **Свойства** и выполните следующие действия. 

    ![Свойства единого входа](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_prop.png)

    a. Задайте для параметра **Включен ли вход для пользователей?** значение **Да**.

    Б. Скопируйте **URL-адрес пользовательского доступа** и вставьте его в раздел **Настройка URL-адреса для единого входа**, описанный далее в этом руководстве.

    c. Задайте для параметра **Требуется назначение пользователей** значение **Да**.

    d. Задайте для параметра **Видно пользователям?** значение **Нет**.

2. Щелкните пункт **Единый вход** на странице интеграции с приложением **ADP Federated SSO**.

    ![Ссылка "Настройка единого входа"][4]

3. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_samlbase.png)

4. В разделе **Домены и URL-адреса приложения ADP Federated SSO** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения ADP Federated SSO](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес: `https://fed.adp.com/` 
    
5. Приложение ADP Federated SSO ожидает проверочные утверждения SAML в определенном формате, который требует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. Утверждение всегда будет носить имя **PersonImmutableID** и иметь значение, которое было сопоставлено с **employeeid**. 

    Здесь выполняется сопоставление пользователя из Azure AD с ADP Federated SSO по значению **employeeid**, однако его можно сопоставить с другим значением, учитывая параметры приложения. Обратитесь к [группе поддержки ADP](https://www.adp.com/contact-us/overview.aspx), чтобы указать правильный идентификатор пользователя и сопоставить это значение с утверждением **PersonImmutableID**.

    ![Настройка единого входа](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

    > [!NOTE] 
    > Чтобы настроить утверждение SAML, обратитесь к [группе поддержки ADP](https://www.adp.com/contact-us/overview.aspx) и запросите значение атрибута уникального идентификатора для своего клиента. Это значение необходимо для настройки пользовательского утверждения для вашего приложения. 

7. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_certificate.png) 

8. Для настройки единого входа на стороне **ADP Federated SSO** необходимо передать загруженный **XML-файл метаданных** на [веб-сайт ADP Federated SSO](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Это может занять несколько дней. 

### <a name="configure-your-adp-services-for-federated-access"></a>Настройка федеративного доступа к службам ADP

>[!Important]
> Сотрудников, которым требуется федеративный доступ к службам ADP, необходимо назначить приложению службы ADP, а затем переназначить конкретной службе ADP.
После получения подтверждения от представителя ADP настройте службы ADP и назначьте пользователей, а также управляйте ими, чтобы контролировать пользовательский доступ к конкретной службе ADP.

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **ADP Federated SSO**, выберите **ADP Federated SSO** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ADP Federated SSO в списке результатов](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addservicegallery.png)

5. На портале Azure на странице интеграции с приложением **ADP Federated SSO** щелкните вкладку **Свойства** и выполните следующие действия.  

    ![Свойства единого входа по ссылке](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedproperties.png)

    a.  Задайте для параметра **Включен ли вход для пользователей?** значение **Да**.

    Б.  Задайте для параметра **Требуется назначение пользователей** значение **Да**.

    c.  Задайте для параметра **Видно пользователям?** значение **Да**.

6. Щелкните пункт **Единый вход** на странице интеграции с приложением **ADP Federated SSO**.

    ![Ссылка "Настройка единого входа"][4]

7. В диалоговом окне **Единый вход** для параметра **Режим** выберите значение **Вход по ссылке**, чтобы связать приложение с **федеративным единым входом ADP**.
<!---Loc Comment:Sentence "to link your application to ADP Federated SSO." should be concatenated with the previous sentence--->

    ![Single sign-on linked](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linked.png)

8. Перейдите в раздел **Настройка URL-адреса для единого входа** и выполните следующие действия.

    ![Свойства единого входа](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedsignon.png)
                                                              
    a. Вставьте **URL-адрес пользовательского доступа**, который вы скопировали ранее на вкладке **Свойства** (из основного приложения ADP Federated SSO).
                                                             
    Б. Ниже перечислены 5 приложений, которые поддерживают разные **URL-адреса состояния ретранслятора**. Необходимо вручную добавить соответствующее значение **URL-адреса состояния ретранслятора** к **URL-адресу пользовательского доступа** для конкретного приложения.
    
    * **ADP Workforce Now**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Сохраните** изменения.

10. Получив подтверждение от представителя ADP, приступите к тестированию с использованием одного или двух пользователей.

    a. Назначьте приложению службы ADP нескольких пользователей для тестирования федеративного доступа.

    Б. Проверка считается успешной, если пользователи получают доступ к приложению службы ADP в коллекции и могут открыть назначенную им службу ADP.
 
11. После подтверждения успешной проверки назначьте службу федеративного доступа ADP отдельным пользователям или группам пользователей, что описано далее в этом руководстве, и разверните ее для своих сотрудников. 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-adp-federated-sso-test-user"></a>Создание тестового пользователя приложения ADP Federated SSO

Цель этого раздела — создать пользователя с именем Britta Simon в приложении ADP Federated SSO. Обратитесь к [команде поддержки ADP Federated SSO](https://www.adp.com/contact-us/overview.aspx), чтобы добавить пользователей в учетную запись ADP Federated SSO.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к ADP Federated SSO, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ADP Federated SSO, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **ADP Federated SSO**.

    ![Ссылка на ADP Federated SSO в списке приложений](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "ADP Federated SSO" на панели доступа, вы автоматически войдете в приложение ADP Federated SSO.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

