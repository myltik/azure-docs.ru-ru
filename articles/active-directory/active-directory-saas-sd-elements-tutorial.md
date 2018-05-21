---
title: Руководство по интеграции Azure Active Directory с SD Elements | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SD Elements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: b523841fc40f8ac07448abc80125f1f510cba840
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Учебник. Интеграция Azure Active Directory с SD Elements

В этом руководстве описано, как интегрировать SD Elements с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SD Elements обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению SD Elements.
- Вы можете включить автоматический вход пользователей в SD Elements (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SD Elements, вам потребуется:

- подписка Azure AD;
- подписка SD Elements с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SD Elements из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sd-elements-from-the-gallery"></a>Добавление SD Elements из коллекции.
Чтобы настроить интеграцию SD Elements с Azure AD, необходимо добавить SD Elements из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SD Elements из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **SD Elements**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_search.png)

5. На панели результатов выберите **SD Elements** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в SD Elements с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в SD Elements соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в SD Elements.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SD Elements.

Чтобы настроить и проверить единый вход Azure AD в SD Elements, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SD Elements](#creating-a-sd-elements-test-user)** требуется для того, чтобы в SD Elements существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SD Elements.

**Чтобы настроить единый вход Azure AD в SD Elements, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SD Elements** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SD Elements** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<tenantname>.sdelements.com/sso/saml2/acs/`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки SD Elements](mailto:support@sdelements.com).

4. SD Elements ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Атрибуты пользователя** приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_attribute.png)

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия. 

    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | email |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_officespace_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Нажмите кнопку **ОК**.
 
6. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_general_400.png)

8. В разделе **Конфигурация SD Elements** щелкните **Настроить SD Elements**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_configure.png)

9. Чтобы включить единый вход, обратитесь в [службу поддержки SD Elements](mailto:support@sdelements.com) и предоставьте загруженный файл сертификата. 

10. В другом окне браузера войдите в клиент SD Elements в качестве администратора.

11. В верхнем меню выберите **System** (Система) и щелкните **Single Sign-on** (Единый вход). 
   
    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 

12. В диалоговом окне **Параметры единого входа** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Для параметра **SSO Type** (Тип SSO) выберите значение **SAML**.
   
    Б. В текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure. 
   
    c. В текстовое поле **Identity Provider Single Sign-On Service** (Служба единого входа поставщика удостоверений) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure. 
   
    d. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-sd-elements-test-user"></a>Создание тестового пользователя SD Elements

Цель этого раздела — создать в приложении SD Elements пользователя с именем Britta Simon. Пользователи SD Elements создаются вручную.

**Чтобы создать пользователя с именем Britta Simon в SD Elements, выполните следующие действия.**

1. Откройте браузер и войдите на корпоративный сайт SD Elements с правами администратора.

2. На панели инструментов вверху щелкните **User Management** (Управление пользователями), а затем щелкните **Users** (Пользователи).
   
    ![Создание тестового пользователя SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 

3. Нажмите кнопку **Add New User**(Добавить нового пользователя).
   
    ![Создание тестового пользователя SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png)
 
4. В диалоговом окне **Add New User** (Добавление нового пользователя) выполните следующие действия.
   
    ![Создание тестового пользователя SD Elements](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. В текстовое поле **E-mail** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.
   
    Б. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.
   
    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.
   
    d. Для параметра **Role** (Роль) выберите значение **User** (Пользователь). 
   
    д. Нажмите кнопку **Создать пользователя**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SD Elements.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SD Elements, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SD Elements**.

    ![Настройка единого входа](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
  
Щелкнув элемент SD Elements на панели доступа, вы автоматически войдете в приложение SD Elements.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png

