---
title: Руководство по интеграции Azure Active Directory с Learning Seat LMS | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Learning Seat LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bb056fcf-4135-478e-85b1-5015d1f07b85
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: jeedes
ms.openlocfilehash: 38b9f017fee4cf0d6905bb20848145386ac6df2c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341871"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-seat-lms"></a>Руководство по интеграции Azure Active Directory с Learning Seat LMS

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением Learning Seat LMS.

Интеграция Learning Seat LMS с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Learning Seat LMS.
- Вы можете включить автоматический вход пользователей в Learning Seat LMS (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Learning Seat LMS, вам потребуется:

- подписка Azure AD;
- подписка на Learning Seat LMS с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Learning Seat LMS из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-learning-seat-lms-from-the-gallery"></a>Добавление Learning Seat LMS из коллекции
Чтобы настроить интеграцию Learning Seat LMS с Azure AD, необходимо добавить Learning Seat LMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Learning Seat LMS из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Learning Seat LMS**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_search.png)

5. На панели результатов выберите **Learning Seat LMS** и нажмите кнопку **Добавить**, чтобы добавить приложение.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описаны настройка и проверка единого входа Azure AD в Learning Seat LMS с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Learning Seat LMS соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Learning Seat LMS.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Learning Seat LMS.

Чтобы настроить и проверить единый вход Azure AD в Learning Seat LMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Learning Seat LMS](#creating-a-learnconnect-test-user)** требуется для создания в Learning Seat LMS пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Learning Seat LMS.

**Чтобы настроить единый вход Azure AD в Learning Seat LMS, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Learning Seat LMS** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Learning Seat LMS** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.learningseatlms.com`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.learningseatlms.com/Account/AssertionConsumerService`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если вы хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.learningseatlms.com`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их на фактические значения идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки Learning Seat](http://help.learningseatlms.com/help). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-learnconnect-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **Learning Seat LMS**, отправьте в [службу поддержки Learning Seat](http://help.learningseatlms.com/help) скачанный **XML-файл метаданных**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-learning-seat-lms-test-user"></a>Создание тестового пользователя Learning Seat LMS

В этом разделе описано, как создать пользователя Britta Simon в приложении Learning Seat LMS. Обратитесь в [службу поддержки Learning Seat](http://help.learningseatlms.com/help), указав все необходимые сведения о пользователе, чтобы добавить пользователей в приложение Learning Seat LMS.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Learning Seat LMS.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Learning Seat LMS, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Learning Seat LMS**.

    ![Настройка единого входа](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа. 

Щелкнув элемент Learning Seat LMS на панели доступа, вы автоматически войдете в приложение Learning Seat LMS. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_203.png

