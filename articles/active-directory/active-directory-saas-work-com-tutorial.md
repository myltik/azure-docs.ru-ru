---
title: Руководство. Интеграция Azure Active Directory с Work.com | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: bfc76d05a52d0283e3367f9c98dc8ed427cbe592
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Руководство. Интеграция Azure Active Directory с Work.com

В этом руководстве описано, как интегрировать Work.com с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Work.com обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Work.com.
- Вы можете включить автоматический вход пользователей в Work.com (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Work.com, вам потребуется:

- подписка Azure AD;
- Подписка Work.com с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Work.com из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="add-workcom-from-the-gallery"></a>Добавление Work.com из коллекции
Чтобы настроить интеграцию Work.com с Azure AD, необходимо добавить Work.com из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Work.com из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Work.com**, выберите **Work.com** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Добавление из коллекции](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Work.com с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Work.com соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Work.com.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Work.com.

Чтобы настроить и проверить единый вход Azure AD в Work.com, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Work.com](#create-a-workcom-test-user)** требуется для того, чтобы в Work.com существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Work.com.

>[!NOTE]
>Чтобы настроить единый вход, также необходимо пользовательское имя домена Work.com. Вам требуется определить по крайней мере одно имя домена, а также проверить и развернуть его для всей организации.

**Чтобы настроить единый вход Azure AD в Work.com, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Work.com** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Параметр "Вход на основе SAML"](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Work.com** сделайте следующее:

    ![Раздел "Домены и URL-адреса приложения Work.com"](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Work.com](https://help.salesforce.com/articleView?id=000159855&type=3). 

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Раздел "Сертификат подписи SAML"](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить"](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Work.com** щелкните **Настроить Work.com**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Раздел "Настройка Work.com"](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Войдите в клиент Work.com от имени администратора.

8. Перейдите в раздел **Настройка**.
   
    ![Настройка](./media/active-directory-saas-work-com-tutorial/ic794108.png "Настройка")

9. В области навигации слева в разделе **Administer** (Администрирование) щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, а затем щелкните **My Domain** (Мой домен), чтобы открыть страницу **My Domain** (Мой домен). 
   
    ![Мой домен](./media/active-directory-saas-work-com-tutorial/ic767825.png "Мой домен")

10. Чтобы проверить правильность настройки домена, убедитесь, что он находится на этапе **Step 4 Deployed to Users** (Шаг 4. Развернут для пользователей), и просмотрите раздел **My Domain Settings** (Параметры моего домена).
   
    ![Домен, развернутый для пользователя](./media/active-directory-saas-work-com-tutorial/ic784377.png "Domain Deployed to User")

11. Войдите в клиент Work.com.

12. Перейдите в раздел **Настройка**.
    
    ![Настройка](./media/active-directory-saas-work-com-tutorial/ic794108.png "Настройка")

13. Разверните меню **Управление безопасностью**, а затем щелкните **Параметры единого входа**.
    
    ![Параметры единого входа](./media/active-directory-saas-work-com-tutorial/ic794113.png "Параметры единого входа")

14. На странице диалогового окна **Параметры единого входа** сделайте следующее:
    
    ![Включение SAML](./media/active-directory-saas-work-com-tutorial/ic781026.png "Включение SAML")
    
    a. Установите флажок **SAML включен**.
    
    Б. Нажмите кнопку **Создать**.

15. В разделе **Параметры единого входа SAML** сделайте следующее:
    
    ![Параметры единого входа SAML](./media/active-directory-saas-work-com-tutorial/ic794114.png "Параметры единого входа SAML")
    
    a. В текстовом поле **Имя** введите имя конфигурации.  
       
    > [!NOTE]
    > При вводе значения в поле **Имя** текстовое поле **Имя API** заполняется автоматически.
    
    Б. В текстовое поле **Issuer** (Издатель) вставьте **идентификатор сущности SAML**, скопированный на портале Azure.
    
    c. Чтобы отправить сертификат, скачанный с Azure AD, нажмите кнопку **Browse** (Обзор).
    
    d. В текстовое поле **Entity id** (Идентификатор сущности) введите `https://salesforce-work.com`.
    
    д. В поле **SAML Identity Type** (Тип удостоверения SAML) выберите значение **Assertion contains the Federation ID from the User object** (Проверочное утверждение содержит идентификатор федерации из объекта User).
    
    f. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentfier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).
    
    ж. В текстовое поле **Identity Provider Login URL** (URL-адрес входа IdP) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    h. В текстовое поле **Identity Provider Logout URL** (URL-адрес выхода IdP) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
    
    i. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициированных поставщиком услуг) выберите значение **HTTP POST**.
    
    j. Выберите команду **Сохранить**.

16. В левой области навигации классического портала Work.com щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, и щелкните **My Domain** (Мой домен), чтобы открыть страницу **My Domain** (Мой домен). 
    
    ![Мой домен](./media/active-directory-saas-work-com-tutorial/ic794115.png "Мой домен")

17. На странице **My Domain** (Мой домен) в разделе **Login Page Branding** (Фирменная символика страницы входа) нажмите кнопку **Edit** (Изменить).
    
    ![Фирменная символика страницы входа](./media/active-directory-saas-work-com-tutorial/ic767826.png "Фирменная символика страницы входа")

14. На странице **Login Page Branding** (Фирменная символика страницы входа) в разделе **Authentication Service** (Служба аутентификации) отображается имя **SAML SSO Settings** (Параметры единого входа SAML). Выберите его, а затем нажмите кнопку **Сохранить**.
    
    ![Фирменная символика страницы входа](./media/active-directory-saas-work-com-tutorial/ic784366.png "Фирменная символика страницы входа")

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Выберите "Пользователи и группы" > "Все пользователи".](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Добавить](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-workcom-test-user"></a>Создание тестового пользователя Work.com
Чтобы пользователи Azure Active Directory могли входить систему, их необходимо подготовить для Work.com. В случае с Work.com подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на сайт компании Work.com в качестве администратора.

2. Перейдите в раздел **Настройка**.
   
    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Настройка")
3. Выберите **Manage Users \> Users** (Управление пользователями > Пользователи).
   
    ![Управление пользователями](./media/active-directory-saas-work-com-tutorial/IC784369.png "Управление пользователями")

4. Щелкните **Новый пользователь**.
   
    ![Все пользователи](./media/active-directory-saas-work-com-tutorial/IC794117.png "Все пользователи")

5. В разделе "User Edit" (Изменение пользователя) выполните следующие действия, разместив в текстовые поля атрибуты действующей учетной записи Azure AD, которую вы хотите подготовить.
   
    ![Изменение пользователя](./media/active-directory-saas-work-com-tutorial/ic794118.png "Изменение пользователя")
   
    a. В текстовое поле **First Name** (Имя) введите **имя пользователя** (**Britta**).
    
    Б. В текстовое поле **Last Name** (Фамилия) введите **фамилию** (**Simon**).
    
    c. В текстовое поле **Alias** (Альтернативное имя) введите **имя** пользователя (**BrittaS**).
    
    d. В текстовое поле **Email** (Адрес электронной почты) введите **адрес электронной почты пользователя** (**Brittasimon@contoso.com**).
    
    д. В текстовое поле **User Name** (Имя пользователя) введите имя пользователя (**Brittasimon@contoso.com**).
    
    f. В текстовое поле **Nick Name** (Псевдоним) укажите **псевдоним** пользователя (**Simon**).
    
    ж. Выберите значения параметров **Role** (Роль), **User License** (Пользовательская лицензия) и **Profile** (Профиль).
    
    h. Выберите команду **Сохранить**.  
      
    > [!NOTE]
    > Владелец этой учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Work.com.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Work.com, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Work.com**.

    ![Work.com в списке приложений](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Work.com на панели доступа, вы автоматически войдете в приложение Work.com.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

