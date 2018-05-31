---
title: Руководство. Интеграция Azure Active Directory с AirWatch | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 89d9bc6a40a6ee814f92cdb4c5a26e660007e64e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344550"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Руководство. Интеграция Azure Active Directory с AirWatch

В этом руководстве описано, как интегрировать AirWatch с Azure Active Directory (Azure AD).

Интеграция приложения AirWatch c Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать, у кого есть доступ к приложению AirWatch.
- Вы можете включить автоматический вход пользователей в AirWatch (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с AirWatch, вам потребуется:

- подписка Azure AD;
- подписка AirWatch с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление AirWatch из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-airwatch-from-the-gallery"></a>Добавление AirWatch из коллекции
Чтобы настроить интеграцию приложения AirWatch с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Добавление приложения AirWatch из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **AirWatch**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. На панели результатов выберите **AirWatch** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение AirWatch с использованием тестового пользователя Britta Simon.

Для работы единого входа службе Azure AD нужно знать, какой пользователь в AirWatch соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в AirWatch.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в AirWatch.

Чтобы настроить и проверить единый вход Azure AD в AirWatch, выполните следующие действия:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя AirWatch](#creating-a-airwatch-test-user)** требуется для создания в AirWatch пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении AirWatch.

**Настройка единого входа Azure AD в AirWatch**

1. На портале Azure на странице интеграции с приложением **AirWatch** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. В разделе **Домены и URL-адреса приложения AirWatch** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    Б. В текстовом поле **Идентификатор** введите значение `AirWatch`.

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес для входа. Для получения этого значения обратитесь в [службу поддержки клиентов AirWatch](http://www.air-watch.com/company/contact-us/). 
 
4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. В разделе **Конфигурация AirWatch** щелкните **Настроить AirWatch**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. В другом окне веб-браузера войдите на корпоративный веб-сайт AirWatch с правами администратора.

8. На панели навигации слева щелкните **Accounts** (Учетные записи), а затем — **Administrators** (Администраторы).
   
   ![Администраторы](./media/active-directory-saas-airwatch-tutorial/ic791920.png "Администраторы")

9. Разверните меню **Settings** (Параметры) и выберите пункт **Directory Services** (Службы каталогов).
   
   ![Параметры](./media/active-directory-saas-airwatch-tutorial/ic791921.png "Параметры")

10. Откройте вкладку **User** (Пользователь), введите в текстовое поле **Base DN** (Базовое доменное имя) используемое доменное имя и щелкните **Save** (Сохранить).
   
   ![Пользователь](./media/active-directory-saas-airwatch-tutorial/ic791922.png "Пользователь")

11. Откройте вкладку **Server** (Сервер).
   
   ![Сервер](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Сервер")

12. Выполните следующие действия:
    
    ![Передача](./media/active-directory-saas-airwatch-tutorial/ic791924.png "Передача")   
    
    a. Для параметра **Directory Type** (Тип каталога) выберите значение **None** (Нет).

    Б. Установите флажок **Use SAML For Authentication**(Использовать SAML для проверки подлинности).

    c. Чтобы отправить скачанный сертификат, нажмите кнопку **Upload**(Отправить).

13. В разделе **Request** (Запрос) выполните следующие действия.
    
    ![Запрос](./media/active-directory-saas-airwatch-tutorial/ic791925.png "Запрос")  

    a. Для параметра **Request Binding Type** (Тип привязки запроса) выберите значение **POST**.

    Б. На портале Azure на диалоговой странице **Настройка единого входа в AirWatch** скопируйте значение в поле **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) и вставьте его в текстовое поле **Identity Provider Single Sign On URL** (URL-адрес единого входа для поставщика удостоверений).

    c. Для параметра **NameID Format** (Формат идентификатора имени) выберите значение **Email Address** (Адрес электронной почты).

    d. Выберите команду **Сохранить**.

14. Снова откройте вкладку **Пользователь** .
    
    ![Пользователь](./media/active-directory-saas-airwatch-tutorial/ic791926.png "Пользователь")

15. В разделе **Атрибут** выполните следующие действия.
    
    ![Атрибут](./media/active-directory-saas-airwatch-tutorial/ic791927.png "Атрибут")

    a. В текстовом поле **Object Identifier** (Идентификатор объекта) введите **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    Б. В текстовом поле **Username** (Имя пользователя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. В текстовом поле **Display Name** (Отображаемое имя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. В текстовом поле **First Name** (Имя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    д. В текстовое поле **Last Name** (Фамилия) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. В текстовое поле **Email** (Электронная почта) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    ж. Выберите команду **Сохранить**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-airwatch-test-user"></a>Создание тестового пользователя AirWatch

Чтобы пользователи Azure AD могли выполнять вход в AirWatch, они должны быть подготовлены в AirWatch.

* Подготовка в AirWatch выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративном веб-сайте **AirWatch** в качестве администратора.
2. На панели навигации слева щелкните **Accounts** (Учетные записи), а затем —**Users** (Пользователи).
   
   ![Пользователи](./media/active-directory-saas-airwatch-tutorial/ic791929.png "Пользователи")
3. В меню **Users** (Пользователи) выберите пункт **List View** (Представление списка), а затем щелкните **Add \> Add User** (Добавить > Добавить пользователя).
   
   ![Добавление пользователя](./media/active-directory-saas-airwatch-tutorial/ic791930.png "Добавление пользователя")
4. В диалоговом окне **Add / Edit User** (Добавление или изменение пользователя) выполните следующие действия.

   ![Добавление пользователя](./media/active-directory-saas-airwatch-tutorial/ic791931.png "Добавление пользователя")   
   1. В текстовых полях **Username** (Имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля), **First Name** (Имя), **Last Name** (Фамилия) и **Email Address** (Адрес электронной почты) введите соответствующие данные действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя AirWatch или API, предоставляемые AirWatch для подготовки учетных записей пользователя AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы разрешим пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к AirWatch.

![Назначение пользователя][200] 

**Назначение пользователя Britta Simon приложению AirWatch**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **AirWatch**.

    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

