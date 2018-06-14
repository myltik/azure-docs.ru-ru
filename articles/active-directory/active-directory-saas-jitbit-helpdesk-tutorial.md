---
title: Учебник. Интеграция Azure Active Directory с Jitbit Helpdesk | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: c8e387bb98ad2e23c667ba058ff8ab5dbfedffbf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343935"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Руководство. Интеграция Azure Active Directory с Jitbit Helpdesk

В этом руководстве описано, как интегрировать Jitbit Helpdesk с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Jitbit Helpdesk обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Jitbit Helpdesk.
- Вы можете включить автоматический вход пользователей в Jitbit Helpdesk (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Jitbit Helpdesk, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Jitbit Helpdesk.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Jitbit Helpdesk из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Добавление Jitbit Helpdesk из коллекции
Чтобы настроить интеграцию Jitbit Helpdesk с Azure AD, необходимо добавить Jitbit Helpdesk из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Jitbit Helpdesk из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Jitbit Helpdesk**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. На панели результатов выберите **Jitbit Helpdesk** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описаны настройка и проверка единого входа Azure AD в Jitbit Helpdesk с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Jitbit Helpdesk соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Jitbit Helpdesk.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Jitbit Helpdesk.

Чтобы настроить и проверить единый вход Azure AD в Jitbit Helpdesk, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Jitbit Helpdesk](#creating-a-jitbit-helpdesk-test-user)** требуется для создания в Jitbit Helpdesk пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Jitbit Helpdesk.

**Чтобы настроить единый вход Azure AD в Jitbit Helpdesk, выполните следующее.**

1. На портале Azure на странице интеграции с приложением **Jitbit Helpdesk** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Jitbit Helpdesk** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Для получения этого значения обратитесь в [службу поддержки клиентов Jitbit Helpdesk](https://www.jitbit.com/support/). 
    
    Б.  В текстовом поле **Идентификатор** введите URL-адрес в формате: `https://www.jitbit.com/web-helpdesk/`

    
 


4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Jitbit Helpdesk** щелкните **Настроить Jitbit Helpdesk**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. В другом окне браузера войдите на свой корпоративный веб-сайт Jitbit Helpdesk в качестве администратора.

8. На панели инструментов в верхней части страницы щелкните **Администрирование**.
   
    ![Администрирование](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "Администрирование")

9. Щелкните **Общие параметры**.
   
    ![Пользователи, организации и разрешения](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "Пользователи, организации и разрешения")

10. В разделе конфигурации **Параметры проверки подлинности** сделайте следующее:
   
    ![Параметры аутентификации](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "Параметры аутентификации")
    
    a. Установите флажок **Enable SAML 2.0 single sign on** (Включить единый вход SAML 2.0) для выполнения единого входа с помощью **OneLogin**.

    Б. В текстовое поле **URL-адрес конечной точки** вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. Откройте сертификат в кодировке **Base-64** в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509**.

    d. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Создание тестового пользователя Jitbit Helpdesk

Чтобы пользователи Azure AD могли выполнять вход в Jitbit Helpdesk, они должны быть подготовлены для Jitbit Helpdesk.  В случае с Jitbit Helpdesk подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход в клиент **Jitbit Helpdesk** .

2. В верхнем меню щелкните **Администрирование**.
   
    ![Администрирование](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "Администрирование")

3. Нажмите кнопку **Пользователи, компании и разрешения**.
   
    ![Пользователи, организации и разрешения](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "Пользователи, организации и разрешения")

4. Нажмите кнопку **Добавить пользователя**.
   
    ![Добавление пользователя](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "Добавление пользователя")
   
5. В разделе Create (Создание) введите данные учетной записи Azure AD, которую необходимо подготовить, в следующие текстовые поля:

    ![Создание](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "Создание")
   
   a. В текстовое поле **Username** (Имя пользователя) введите имя пользователя **Britta Simon**, используемое на портале Azure.

   Б. В текстовое поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя, например **BrittaSimon@contoso.com**.

   c. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

   d. В текстовое поле **Last Name** (Фамилия) введите фамилию, например, **Simon**.
   
   д. Нажмите кнопку **Создать**.

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Jitbit Helpdesk или API, предоставляемые Jitbit Helpdesk для подготовки учетных записей пользователя Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Jitbit Helpdesk.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Jitbit Helpdesk, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Jitbit Helpdesk**.

    ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Когда вы щелкните элемент Jitbit Helpdesk на панели доступа, должна появиться страница входа в приложение Jitbit Helpdesk.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

