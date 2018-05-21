---
title: Руководство по интеграции Azure Active Directory с Projectplace | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 3301e002315e12feb0ac886d4d8451007c36e6ff
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Учебник. Интеграция Azure Active Directory с Projectplace

В данном руководстве описано, как интегрировать Projectplace с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Projectplace обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Projectplace.
- Вы можете включить автоматический вход для пользователей в Projectplace (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Projectplace, вам потребуется:

- подписка Azure AD;
- подписка Projectplace с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Projectplace из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-projectplace-from-the-gallery"></a>Добавление Projectplace из коллекции
Чтобы настроить интеграцию Projectplace с Azure AD, необходимо добавить Projectplace из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Projectplace из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Projectplace**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_search.png)

5. На панели результатов выберите **Projectplace** и щелкните **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Projectplace с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Projectplace соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Projectplace.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Projectplace.

Чтобы настроить и проверить единый вход Azure AD в Projectplace, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Projectplace](#creating-a-projectplace-test-user)** требуется для создания в Projectplace пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Projectplace.

**Чтобы настроить единый вход Azure AD в Projectplace, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Projectplace** выберите **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Projectplace** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company>.projectplace.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Для получения этого значения обратитесь в [службу поддержки клиентов Projectplace](https://success.planview.com/Projectplace/Support). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **Projectplace**, отправьте скачанный **XML-файл метаданных** в [службу поддержки Projectplace](https://success.planview.com/Projectplace/Support). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

>[!NOTE]
>Настройку единого входа должна выполнять [служба поддержки Projectplace](https://success.planview.com/Projectplace/Support). Сразу же после завершения настройки вы получите уведомление.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-projectplace-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-projectplace-test-user"></a>Создание тестового пользователя Projectplace

Чтобы пользователи Azure AD могли выполнять вход в Projectplace, они должны быть подготовлены для Projectplace. В случае с Projectplace подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на сайт **Projectplace** компании в качестве администратора.

2. Перейдите к разделу **People** (Люди) и щелкните **Members** (Участники).
   
    ![Люди](./media/active-directory-saas-projectplace-tutorial/ic790228.png "Люди")

3. Щелкните **Добавить участника**.
   
    ![Добавление участников](./media/active-directory-saas-projectplace-tutorial/ic790232.png "Добавление участников")

4. В разделе **Добавление участника** выполните следующие действия.
   
    ![Новые участники](./media/active-directory-saas-projectplace-tutorial/ic790233.png "Новые участники")
   
    a. В разделе **Новые участники** введите электронный адрес действующей учетной записи AAD, которую вы хотите подготовить, в соответствующее текстовое поле.
   
    Б. Нажмите кнопку **Отправить**.

   Владельцу учетной записи Azure Active Directory будет отправлено электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Projectplace или API, предоставляемые Projectplace для подготовки учетных записей пользователя AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В данном разделе описано, как предоставить пользователю Britta Simon доступ к Projectplace, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Projectplace, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Projectplace**.

    ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/tutorial_projectplace_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Projectplace на панели доступа, вы автоматически войдете в приложение Projectplace.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-projectplace-tutorial/tutorial_general_203.png

