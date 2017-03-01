---
title: "Руководство. Интеграция Azure Active Directory с PlanMyLeave | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и PlanMyLeave."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83334b1b02df214e51c86a862636c9392cd19474
ms.openlocfilehash: ba418a641b339a0d94a3c7b2596d37fbd88a30c5
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Руководство. Интеграция Azure Active Directory с PlanMyLeave

В этом руководстве описано, как интегрировать PlanMyLeave с Azure Active Directory (Azure AD).

Интеграция PlanMyLeave с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к PlanMyLeave.
- Вы можете включить автоматический вход пользователей в PlanMyLeave (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с PlanMyLeave, вам потребуется следующее:

- подписка Azure AD;
- подписка PlanMyLeave с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление PlanMyLeave из коллекции
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-planmyleave-from-the-gallery"></a>Добавление PlanMyLeave из коллекции
Чтобы настроить интеграцию PlanMyLeave с Azure AD, необходимо добавить PlanMyLeave из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PlanMyLeave из коллекции, выполните следующее.**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **PlanMyLeave**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_001.png)

5. На панели результатов выберите **PlanMyLeave** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в PlanMyLeave с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в PlanMyLeave соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PlanMyLeave.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PlanMyLeave.

Чтобы настроить и проверить единый вход Azure AD в PlanMyLeave, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения PlanMyLeave](#creating-a-planmyleave-test-user)** требуется для создания в PlanMyLeave пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении PlanMyLeave.

**Чтобы настроить единый вход Azure AD в PlanMyLeave, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **PlanMyLeave** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. На странице диалогового окна **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)

3. В разделе **Домены и URL-адреса приложения PlanMyLeave** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_02.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company-name>.planmyleave.com/Login.aspx`.
    
    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [группе поддержки PlanMyLeave](mailto:support@planmyleave.com).

4. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png)     

5. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_300.png)

6. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png)

7. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

8. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png) 

9. В разделе **PlanMyLeave Configuration** (Конфигурация Fuse) щелкните **Configure PlanMyLeave** (Настроить Fuse), чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_06.png) 

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_07.png)

10. В другом окне веб-браузера войдите в свой клиент PlanMyLeave в качестве администратора.

11. Перейдите в окно **System Setup** (Настройка системы). Затем в разделе **Security Management** (Управление безопасностью) щелкните **Company SAML settings** (Параметры SAML компании).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

12. В разделе **SAML Settings** (Параметры SAML) щелкните значок редактора.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

13. В разделе **Update SAML Settings** (Изменение параметров SAML) выполните следующее.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    а.  В текстовом поле **URL-адрес для входа** введите значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из окна настройки приложения Azure AD.

    b.  Откройте скачанный файл сертификата в Блокноте, скопируйте его содержимое между метками ---Begin Certificate--- и ---End certificate---- в буфер обмена, а затем вставьте его в текстовое поле **Certificate** (Сертификат).

    c. Для параметра "**Is Enable**" (Включено) установите значение "**Yes**" (Да).

    d. Щелкните **Сохранить**.



### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-planmyleave-test-user"></a>Создание тестового пользователя PlanMyLeave

Цель этого раздела — создать пользователя с именем Britta Simon в PlanMyLeave. Приложение PlanMyLeave поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к PlanMyLeave (если он еще не создан).

> [!NOTE]
> Если нужно создать пользователя вручную, обратитесь к [группе поддержки PlanMyLeave](mailto:support@planmyleave.com).



### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к PlanMyLeave.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в PlanMyLeave, выполните следующие действия:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **PlanMyLeave**.

    ![Настройка единого входа](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент PlanMyLeave на панели доступа, вы автоматически войдете в приложение PlanMyLeave.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
