---
title: Руководство по интеграции Azure Active Directory со Small Improvements | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Small Improvements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2f6cab5dd7c10e4036cdd2013c809142bf7ec846
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352571"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Учебник. Интеграция Azure Active Directory со Small Improvements

В этом руководстве описано, как интегрировать Small Improvements с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Small Improvements обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению Small Improvements.
- Вы можете включить автоматический вход пользователей в Small Improvements (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD со Small Improvements, вам потребуется:

- подписка Azure AD;
- подписка Small Improvements с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Small Improvements из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-small-improvements-from-the-gallery"></a>Добавление Small Improvements из коллекции
Чтобы настроить интеграцию Small Improvements с Azure AD, необходимо добавить Small Improvements из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Small Improvements из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Small Improvements**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_search.png)

5. На панели результатов выберите **Small Improvements** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Small Improvements с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Small Improvements соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Small Improvements.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Small Improvements.

Чтобы настроить и проверить единый вход Azure AD в Small Improvements, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Small Improvements](#creating-a-small-improvements-test-user)** требуется для того, чтобы в Small Improvements существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Small Improvements.

**Чтобы настроить единый вход Azure AD в Small Improvements, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Small Improvements** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Small Improvements** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.small-improvements.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Small Improvements](mailto:support@small-improvements.com). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Small Improvements** щелкните **Настроить Small Improvements**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

7. В другом окне браузера войдите на корпоративный сайт Small Improvements с правами администратора.

8. На главной странице панели мониторинга нажмите слева кнопку **Администрирование** .
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

9. Нажмите кнопку **SAML SSO** (Единый вход SAML) в разделе **Integrations** (Интеграции).
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

10. На странице настройки единого входа выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. В текстовое поле **HTTP Endpoint** (Конечная точка HTTP) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    Б. Откройте загруженный сертификат в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** . 

    c. Если вы хотите предоставить пользователям возможность единого входа и аутентификации через форму входа, установите флажок **Enable access via login/password too** (Включить также доступ по имени входа и паролю).  

    d. Введите значение имени единого входа в текстовом поле **SAML Prompt** (Приглашение SAML).  

    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-small-improvements-test-user"></a>Создание тестового пользователя Small Improvements

Чтобы пользователи Azure AD могли выполнять вход в Small Improvements, они должны быть подготовлены в Small Improvements. В случае Small Improvements подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Small Improvements с правами администратора.

2. На главной странице перейдите к меню в левой части страницы и щелкните **Администрирование**.

3. Нажмите кнопку **Каталог пользователей** в разделе "Управление пользователями". 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

4. Щелкните **Add users** (Добавить пользователей).

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

5. В диалоговом окне **Add Users** (Добавление пользователей) сделайте следующее. 

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. В текстовое поле **First name** (Имя) введите имя пользователя, например **Britta**.

    Б. В текстовое поле **Last name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовое поле **Email** (Электронная почта) введите электронную почту пользователя, например **brittasimon@contoso.com**. 

    d. Кроме того, вы можете ввести персональное сообщение в поле **Отправить уведомление по электронной почте** . Если уведомление отправлять не нужно, снимите этот флажок.

    д. Щелкните **Создать пользователей**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Small Improvements.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Small Improvements, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Small Improvements**.

    ![Настройка единого входа](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент Small Improvements на панели доступа, вы автоматически войдете в приложение Small Improvements.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png

