---
title: Руководство по интеграции Azure Active Directory с ADP Globalview | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в ADP Globalview.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 5471a4edfc6830367d120580c1c36f30f87a8f90
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347522"
---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Руководство по интеграции Azure Active Directory с ADP Globalview

В этом руководстве описано, как интегрировать ADP Globalview с Azure Active Directory (Azure AD).

Интеграция Azure AD с ADP Globalview обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к ADP Globalview.
- Вы можете включить автоматический вход пользователей в ADP Globalview (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ADP Globalview, вам потребуется:

- подписка Azure AD;
- подписка ADP Globalview с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ADP Globalview из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-adp-globalview-from-the-gallery"></a>Добавление ADP Globalview из коллекции
Чтобы настроить интеграцию ADP Globalview с Azure AD, необходимо добавить ADP Globalview из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ADP Globalview из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **ADP Globalview**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_search.png)

5. На панели результатов выберите **ADP Globalview** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в ADP Globalview с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в ADP Globalview соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ADP Globalview.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ADP Globalview.

Чтобы настроить и проверить единый вход Azure AD в ADP Globalview, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ADP Globalview](#creating-an-adp-globalview-test-user)** нужно для того, чтобы в ADP Globalview также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении ADP Globalview.

**Чтобы настроить единый вход Azure AD в ADP Globalview, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **ADP Globalview** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_samlbase.png)

3. В разделе **Домены и URL-адреса приложения ADP Globalview** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_url.png)

     В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.globalview.adp.com/federate` или `https://<subdomain>.globalview.adp.com/federate2`.

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него нужно указать фактический идентификатор. Обратитесь в [службу поддержки ADP Globalview](https://www.adp.com/contact-us/overview.aspx) для получения этого значения.
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_certificate.png) 

5. Приложение ADP Globalview ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. 

6. На следующем снимке экрана приведен пример. Утверждение всегда будет носить имя **PersonImmutableID** и иметь значение, которое было сопоставлено с ExtensionAttribute2, содержащим EmployeeID пользователя. Здесь выполняется сопоставление пользователя из Azure AD с ADP Globalview по значению EmployeeID, однако его можно сопоставить с другим значением, учитывая параметры приложения. Вместе со службой поддержки ADP GlobalView выполните действия по использованию правильного идентификатора пользователя и его сопоставлению с утверждением **PersonImmutableID**. Также можно сопоставить утверждения электронной почты и идентификатора пользователя, как показано на рисунке.

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_attribute.png)

7. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | personalimmutableid | user.extensionattribute2 |
    | email               | user.mail |
    | userid              | user.userprincipalname|
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

    > [!NOTE] 
    > Чтобы настроить утверждение SAML, обратитесь в [службу поддержки ADP Globalview](https://www.adp.com/contact-us/overview.aspx) и запросите значение атрибута уникального идентификатора для своего клиента. Это значение необходимо для настройки пользовательского утверждения для вашего приложения. 

8. В разделе **Конфигурация ADP Globalview** щелкните **Настроить ADP Globalview**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_configure.png) 

9. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_general_400.png)

10. Чтобы настроить единый вход на стороне **ADP Globalview**, нужно отправить скачанный **сертификат (Base64)**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки ADP Globalview](https://www.adp.com/contact-us/overview.aspx).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_01.png) 

2.  Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-adp-globalview-test-user"></a>Создание тестового пользователя ADP Globalview

Цель этого раздела — создать пользователя с именем Britta Simon в приложении ADP GlobalView. Обратитесь в [службу поддержки ADP Globalview](https://www.adp.com/contact-us/overview.aspx), чтобы добавить пользователей в учетную запись ADP Globalview. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ADP Globalview.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ADP Globalview, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **ADP Globalview**.

    ![Настройка единого входа](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент ADP GlobalView на панели доступа, вы автоматически войдете в приложение ADP GlobalView.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_203.png

