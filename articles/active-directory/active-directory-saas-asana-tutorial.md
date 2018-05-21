---
title: Руководство по интеграции Azure Active Directory с Asana | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: 2042c1525849696efaf5fda5f35c1abc72839737
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Руководство. Интеграция Azure Active Directory с Asana

В этом руководстве описано, как интегрировать Asana с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Asana обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Asana.
- Вы можете включить автоматический вход пользователей в Asana (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Asana, вам потребуется:

- подписка Azure AD;
- подписка Asana с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Asana из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-asana-from-the-gallery"></a>Добавление Asana из коллекции.
Чтобы настроить интеграцию Asana с Azure AD, необходимо добавить Asana из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Asana из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Asana**, выберите **Asana** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Asana с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Asana соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Asana.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Asana.

Чтобы настроить и проверить единый вход Azure AD в Asana, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Asana](#create-an-asana-test-user)** нужно для того, чтобы в Asana существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Asana.

**Чтобы настроить единый вход Azure AD в Asana, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Asana** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Asana** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес `https://app.asana.com/`.

    Б. В текстовом поле **Идентификатор** введите значение `https://app.asana.com/`.
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Asana** щелкните **Настроить Asana**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. В другом окне браузера войдите в приложение Asana. Чтобы настроить единый вход в Asana, перейдите к параметрам рабочей области, щелкнув имя рабочей области в правом верхнем углу экрана. Щелкните элемент **\<your workspace name\> Settings** (Параметры <имя_рабочей_области>). 
   
    ![Параметры единого входа Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. В окне **Organization settings** (Параметры организации) щелкните **Administration** (Администрирование). Затем щелкните **Members must log in via SAML** (Участники должны входить с помощью SAML), чтобы активировать конфигурацию единого входа. Выполните следующие действия.
   
    ![Настройка параметров единого входа организации](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. Вставьте **URL-адрес службы единого входа SAML** в текстовое поле **Sign-in page URL** (URL-адрес страницы для входа).

     Б. Щелкните правой кнопкой мыши сертификат, скачанный с портала Azure, а затем откройте файл сертификата в Блокноте или в любом текстовом редакторе. Скопируйте содержимое между операторами begin и end заголовка сертификата в текстовое поле **X.509 Certificate** (Сертификат X.509).

9. Выберите команду **Сохранить**. Если вам нужна дополнительная помощь, изучите [руководство по настройке единого входа в Asana](https://asana.com/guide/help/premium/authentication#gl-saml) .

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-asana-test-user"></a>Создание тестового пользователя Asana

В этом разделе описано, как создать пользователя Britta Simon в приложении Asana.

1. В приложении **Asana** откройте раздел **Teams** (Группы) на левой панели. Нажмите кнопку со знаком "плюс". 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Введите адрес электронной почты britta.simon@contoso.com в текстовое поле и выберите **Invite**(Пригласить).

3. Щелкните **Send Invite**(Отправить приглашение). Новый пользователь получит сообщение электронной почты. С его помощью пользователь должен создать и подтвердить учетную запись.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Asana.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Asana, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Asana**.

    ![Ссылка на Asana в списке "Приложения"](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить работу единого входа Azure AD.

Перейдите на страницу входа в Asana. Вставьте адрес электронной почты britta.simon@contoso.com в соответствующее текстовое поле. Текстовое поле пароля оставьте пустым и щелкните **Log In** (Вход). Вы перейдете на страницу входа в Azure AD. Введите учетные данные Azure AD. Теперь вы должны войти в Asana.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
