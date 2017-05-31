---
title: "Руководство по интеграции Azure Active Directory с LinkedIn Learning | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и LinkedIn Learning."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a61cf0ffce8bede930744d445df88dde22061a59
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Руководство по интеграции Azure Active Directory с LinkedIn Learning

В этом руководстве описано, как интегрировать приложение LinkedIn Learning с Azure Active Directory (Azure AD).

Интеграция Azure AD с LinkedIn Learning обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LinkedIn Learning.
- Вы можете включить автоматический вход пользователей в LinkedIn Learning (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с LinkedIn Learning, вам потребуется:

- подписка Azure AD;
- подписка LinkedIn Learning с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление LinkedIn Learning из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-linkedin-learning-from-the-gallery"></a>Добавление LinkedIn Learning из коллекции
Чтобы настроить интеграцию LinkedIn Learning с Azure AD, необходимо добавить LinkedIn Learning из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LinkedIn Learning из коллекции, выполните следующие действия:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **LinkedIn Learning**. На панели результатов щелкните **LinkedIn Learning**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение LinkedIn Learning с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в LinkedIn Learning соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LinkedIn Learning.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LinkedIn Learning.

Чтобы настроить и проверить единый вход Azure AD в LinkedIn Learning, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LinkedIn Learning](#creating-a-linkedin-learning-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении LinkedIn Learning.

**Чтобы настроить единый вход Azure AD в LinkedIn Learning, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **LinkedIn Learning** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. В другом окне веб-браузера войдите в свой клиент LinkedIn Learning с правами администратора.

4. В **Account Center** (Центр учетных записей) в разделе **Settings** (Параметры) щелкните **Global Settings** (Глобальные параметры). Также выберите **Learning - Default** в раскрывающемся списке.

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Щелкните **OR Click Here to load and copy individual fields from the form** (Или щелкните здесь, чтобы загрузить и скопировать отдельные поля из формы) и скопируйте значения **Entity Id** (Идентификатор сущности) и **Assertion Consumer Service (ACS) Url** (URL-адрес службы обработчика утверждений (ACS)).

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. Если вы хотите настроить единый вход в режиме, **инициированном поставщиком удостоверений**, то на портале Azure в разделе **Домены и URL-адреса приложения LinkedIn Learning** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    а. В текстовом поле **Идентификатор** введите **идентификатор сущности**, скопированный с портала LinkedIn. 

    b. В текстовом поле **URL-адрес ответа** введите **URL-адрес службы обработчика утверждений (ACS)**, скопированный с портала LinkedIn.

7. Если вы хотите настроить единый вход в режиме, **инициированном поставщиком услуг**, то установите флажок "Показать дополнительные параметры URL-адресов" в разделе настроек и настройте URL-адрес входа в таком формате:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. Приложение LinkedIn Learning ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. По умолчанию **идентификатор пользователя** имеет значение **user.userprincipalname**, но для LinkedIn Learning требуется сопоставить это значение с адресом электронной почты пользователя. Для этого можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации. 

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/updateusermail.png)
    
9. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и задайте эти атрибуты. Необходимо также добавить утверждение с именем **department**, значение которого должно быть сопоставлено с **user.department**.

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | department| user.department |

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/userattribute.png)

   а. Щелкните "Добавить атрибут", чтобы открыть страницу сведений об атрибутах. Добавьте атрибут department, как показано ниже.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/adduserattribute.png)
   
   b. Нажмите кнопку **ОК**, чтобы сохранить атрибут.

10. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png) 

11. Щелкните **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_400.png)

12. Перейдите в раздел **LinkedIn Admin Settings** (Параметры администратора LinkedIn). Передайте XML-файл, скачанный с портала Azure. Для этого нажмите кнопку "Upload XML file" (Передать XML-файл).

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

13. Нажмите кнопку **On** (Включить), чтобы включить единый вход. Состояние единого входа изменится с **Not Connected** (Не подключено) на **Connected** (Подключено.)

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 

### <a name="creating-a-linkedin-learning-test-user"></a>Создание тестового пользователя LinkedIn Learning

Приложение Linked Learning поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. На странице параметров администратора на портале LinkedIn Learning включите параметр **Automatically assign licenses** (Автоматически назначать лицензии), чтобы активировать JIT-подготовку. При этом пользователю будет также назначена лицензия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к LinkedIn Learning.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LinkedIn Learning, выполните следующие действия:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **LinkedIn Learning**.

    ![Настройка единого входа](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент LinkedIn Learning на панели доступа, вы перейдете на страницу входа Azure и, успешного выполнив вход, войдете в приложение LinkedIn Learning.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_203.png

