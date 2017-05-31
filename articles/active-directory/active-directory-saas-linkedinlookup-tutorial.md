---
title: "Руководство по интеграции Azure Active Directory с LinkedIn Lookup | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и LinkedIn Lookup."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a2757a39-1ead-4a3e-91e4-270be3055683
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a4160a89c6a06222a9239d996fcca822e1a50382
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-lookup"></a>Руководство по интеграции Azure Active Directory с LinkedIn Lookup

В этом руководстве описано, как интегрировать приложение LinkedIn Lookup с Azure Active Directory (Azure AD).

Интеграция Azure AD с LinkedIn Lookup обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LinkedIn Lookup.
- Вы можете включить автоматический вход пользователей в LinkedIn Lookup (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с LinkedIn Lookup, вам потребуется:

- подписка Azure AD;
- подписка LinkedIn Lookup с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление LinkedIn Lookup из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-linkedin-lookup-from-the-gallery"></a>Добавление LinkedIn Lookup из коллекции
Чтобы настроить интеграцию LinkedIn Lookup с Azure AD, необходимо добавить LinkedIn Lookup из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LinkedIn Lookup из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **LinkedIn Lookup**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_search.png)

5. На панели результатов выберите **LinkedIn Lookup** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложении LinkedIn Lookup с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в LinkedIn Lookup соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LinkedIn Lookup.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LinkedIn Lookup.

Чтобы настроить и проверить единый вход Azure AD в LinkedIn Lookup, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LinkedIn Lookup](#creating-an-linkedin-lookup-test-user)** требуется для создания в LinkedIn Lookup пользователя Britta Simon, связанного с представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LinkedIn Lookup.

**Чтобы настроить единый вход Azure AD в LinkedIn Lookup, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **LinkedIn Lookup** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_samlbase.png)

3. В другом окне веб-браузера войдите на свой веб-сайт **LinkedIn Lookup** в качестве администратора.

4. В разделе **Параметры** **Центра управления учетной записью** щелкните **Глобальные параметры**. Также выберите **Lookup** в раскрывающемся списке.

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_011.png)

5. Щелкните **OR Click Here to load and copy individual fields from the form** (Или щелкните здесь, чтобы загрузить и скопировать отдельные поля из формы) и скопируйте значения **Entity Id** (Идентификатор сущности) и **Assertion Consumer Service (ACS) Url** (URL-адрес службы обработчика утверждений (ACS)).

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_032.png)

6. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения LinkedIn Lookup** портала Azure сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url.png)

    а. В текстовом поле **Идентификатор** введите **идентификатор сущности**, скопированный с портала LinkedIn. 

    b. В текстовом поле **URL-адрес ответа** введите **URL-адрес службы обработчика утверждений (ACS)**, скопированный с портала LinkedIn.

7. Установите флажок **Показать дополнительные параметры URL-адресов**, если вы хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.linkedIn.com/checkpoint/enterprise/login/<AccountId>?application=lookup`
     
    > [!NOTE] 
    > Обратите внимание, что это значение используется только в качестве примера. Пользователю необходимо заменить эти значения фактическим URL-адресом входа. Для получения этого значения обратитесь в [службу поддержки клиентов LinkedIn Lookup](https://business.LinkedIn.com/lookup).

8. Приложение **LinkedIn Lookup** ожидает утверждения SAML в определенном формате. Пользователю необходимо добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан пример. По умолчанию **идентификатор пользователя** имеет значение **user.userprincipalname**, но для LinkedIn Lookup требуется сопоставить это значение с адресом электронной почты пользователя. Можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации. 

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/updateusermail.png)
    
9. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и задайте эти атрибуты. Нужно также добавить утверждение с именем **department**, значение которого требуется сопоставить с **user.department**.

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | department| user.department |

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/userattribute.png)

   а. Щелкните **Добавить атрибут**, чтобы открыть страницу сведений об атрибутах. Добавьте атрибут department, как показано ниже.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/adduserattribute.png)
   
   b. Нажмите кнопку **ОК**, чтобы сохранить атрибут.

10. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_certificate.png) 

11. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_400.png)

12. Перейдите в раздел **LinkedIn Admin Settings** (Параметры администратора LinkedIn). Передайте XML-файл, скачанный с портала Azure. Для этого нажмите кнопку **Upload XML file** (Передать XML-файл).

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_metadata_03.png)

13. Нажмите кнопку **On** (Включить), чтобы включить единый вход. Состояние единого входа изменяется с **Not Connected** (Не подключено) на **Connected** (Подключено.)

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_admin_05.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_02.png) 

3. Щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_04.png) 

    А. В текстовом поле **Name** (Имя) введите **Britta Simon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-an-linkedin-lookup-test-user"></a>Создание тестового пользователя LinkedIn Lookup

Приложение LinkedIn Lookup поддерживает JIT-подготовку пользователей, поэтому после проверки подлинности пользователи создаются в приложении автоматически. Включите параметр **Automatically assign licenses** (Автоматически назначать лицензии), чтобы назначить лицензию пользователю.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedin_admin_license.png)

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к LinkedIn Lookup, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LinkedIn Lookup, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **LinkedIn Lookup**.

    ![Настройка единого входа](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При щелчке плитки LinkedIn Lookup на панели доступа должна открыться страница организации, где нужно указать сведения о личной учетной записи LinkedIn. В результате ваша личная учетная запись связывается с вашей рабочей учетной записью LinkedIn. 

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_203.png


