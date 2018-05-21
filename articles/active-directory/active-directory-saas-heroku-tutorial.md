---
title: Учебник. Интеграция Azure Active Directory с Heroku | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Heroku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 97b837b32c8ec679497f97c03ddccdc935985963
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>Учебник. Интеграция Azure Active Directory с Heroku

В этом учебнике описано, как интегрировать Heroku с Azure Active Directory (Azure AD).

Интеграция Heroku с Azure AD дает приведенные далее преимущества:

- С помощью Azure AD вы можете контролировать доступ к Heroku.
- Вы можете включить автоматический вход пользователей в Heroku (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Heroku, вам потребуется:

- подписка Azure AD;
- подписка Heroku с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Heroku из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-heroku-from-the-gallery"></a>Добавление Heroku из коллекции
Чтобы настроить интеграцию Heroku с Azure AD, необходимо добавить Heroku из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Heroku из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Heroku**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_search.png)

5. На панели результатов выберите **Heroku** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.

В этом разделе описана настройка и проверка единого входа Azure AD в Heroku с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Heroku соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Heroku.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Heroku.

Чтобы настроить и проверить единый вход Azure AD в Heroku, вам потребуется выполнить действия в указанных далее стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Heroku](#creating-a-heroku-test-user)** требуется для создания в Heroku пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Heroku.

**Чтобы настроить единый вход Azure AD в Heroku, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Heroku** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Heroku** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:    
    `https://sso.heroku.com/saml/<company-name>/init`

    Б. В текстовом поле **Identifier URL** (URL-адрес идентификатора) введите URL-адрес в следующем формате:            
    `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    >Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Вы получаете эти значения от команды Heroku. Как это сделать, описано в последующих разделах этой статьи. 
        
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-heroku-tutorial/tutorial_general_400.png)

6. Чтобы включить единый вход в Heroku, выполните следующие действия:
   
    a. Войдите в учетную запись Heroku с помощью прав администратора.

    Б. Перейдите на вкладку **Параметры** .

    c. На странице **Single Sign On Page** (Страница единого входа) щелкните **Upload Metadata** (Передать метаданные).

    d. Отправьте файл метаданных, который вы скачали с портала Azure.

    д. После успешной установки администраторы увидят диалоговое окно подтверждения. Для конечных пользователей будет отображен URL-адрес единого входа. 

    f. Скопируйте **URL-адрес входа Heroku** и **идентификатор сущности Heroku**, а затем вернитесь в раздел **Домены и URL-адреса приложения Heroku** на портале Azure и вставьте эти значения в текстовые поля **URL-адреса входа** и **Идентификатор** соответственно.

    ![Настройка единого входа](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) 
    
8. Нажмите кнопку **Далее**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-heroku-test-user"></a>Создание тестового пользователя Heroku

В этом разделе описано, как создать пользователя Britta Simon в приложении Heroku. Приложение Heroku поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь, если он еще не существует, создается при доступе к Heroku. После подготовки учетной записи конечный пользователь получает проверочное сообщение электронной почты, в котором ему нужно щелкнуть ссылку подтверждения.

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться в [службу поддержки клиентов Heroku](https://www.heroku.com/support).
>  

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Heroku, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Heroku, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Heroku**.

    ![Настройка единого входа](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Heroku на панели доступа, вы автоматически войдете в приложение Heroku.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
