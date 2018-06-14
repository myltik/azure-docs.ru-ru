---
title: Учебник. Интеграция Azure Active Directory с Humanity | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Humanity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 4d86c7dbc72ee20c7ae15093abc545ebd73affce
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349147"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Учебник. Интеграция Azure Active Directory с Humanity

В этом учебнике описано, как интегрировать Humanity с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Humanity обеспечивает следующие преимущества.

- С помощью Azure AD можно управлять доступом к Humanity.
- Вы можете включить автоматический вход пользователей в Humanity (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Humanity, вам потребуется:

- подписка Azure AD;
- подписка Humanity с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Humanity из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-humanity-from-the-gallery"></a>Добавление Humanity из коллекции
Чтобы настроить интеграцию Humanity с Azure AD, необходимо добавить Humanity из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Humanity из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Humanity**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_search.png)

5. На панели результатов выберите **Humanity** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Humanity с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Humanity соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Humanity.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Humanity.

Чтобы настроить и проверить единый вход Azure AD в Humanity, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Humanity](#creating-a-humanity-test-user)** требуется для создания в Humanity пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на новом портале Azure и настроить его в приложении Humanity.

**Чтобы настроить единый вход Azure AD в Humanity, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Humanity** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Humanity** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://company.humanity.com/includes/saml/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://company.humanity.com/app/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов Humanity](https://www.humanity.com/support/). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Humanity** щелкните **Настроить Humanity**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML и URL-адрес выхода** из раздела **Краткий справочник.**

    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_configure.png) 

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **Humanity** в качестве администратора.

8. В верхнем меню щелкните **Администратор**.
   
    ![Администратор](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "Администратор")

9. В разделе **Integration** (Интеграция) щелкните **Single Sign-On** (Единый вход).
   
    ![Единый вход](./media/active-directory-saas-shiftplanning-tutorial/iC786620.png "Единый вход")

10. В разделе **Единый вход** сделайте следующее:
   
    ![Единый вход](./media/active-directory-saas-shiftplanning-tutorial/iC786905.png "Единый вход")
   
    a. Установите флажок **SAML включен**.

    Б. Установите флажок **Allow Password Login** (Разрешить вход с паролем).

    c. Вставьте **URL-адрес службы единого входа SAML** в текстовое поле **URL-адрес издателя SAML**.

    d. Вставьте значение **URL-адреса выхода** в текстовое поле **URL-адрес удаленного выхода**.
   
    д. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** .

11. Нажмите кнопку **Сохранить параметры**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-humanity-test-user"></a>Создание тестового пользователя Humanity

Чтобы пользователи Azure AD могли выполнять вход в Humanity, они должны быть подготовлены в Humanity. В случае с Humanity подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт **Humanity** вашей компании в качестве администратора.

2. Щелкните **Администратор**.
   
    ![Администратор](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "Администратор")

3. Щелкните **Персонал**.
   
    ![Персонал](./media/active-directory-saas-shiftplanning-tutorial/ic786623.png "Персонал")

4. В разделе **Действия** щелкните **Добавление сотрудников**.
   
    ![Добавить сотрудников](./media/active-directory-saas-shiftplanning-tutorial/iC786624.png "Добавить сотрудников")

5. В разделе **Добавление сотрудников** сделайте следующее:
   
    ![Сохранить сотрудников](./media/active-directory-saas-shiftplanning-tutorial/iC786625.png "Сохранить сотрудников")
   
    a. Заполните текстовые поля **Имя**, **Фамилия** и **Email** (Электронная почта) данными действующей учетной записи AAD, которую необходимо подготовить.

    Б. Щелкните **Сохранить сотрудников**.

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Humanity или API, предоставляемые Humanity для подготовки учетных записей пользователей AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Humanity.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Humanity, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Humanity**.

    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Humanity на панели доступа, вы автоматически войдете в приложение Humanity.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_203.png

