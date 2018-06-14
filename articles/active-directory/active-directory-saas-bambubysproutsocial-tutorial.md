---
title: Руководство по интеграции Azure Active Directory с Bambu by Sprout Social | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Bambu by Sprout Social.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2b9ddbc-cab7-40d6-aca1-5b171cab4199
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: d6b94e1a2d002459089730fc5b47cca33ca55f20
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337951"
---
# <a name="tutorial-azure-active-directory-integration-with-bambu-by-sprout-social"></a>Руководство по интеграции Azure Active Directory с Bambu by Sprout Social

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Bambu by Sprout Social.

Интеграция Azure AD с Bambu by Sprout Social обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Bambu by Sprout Social.
- Вы можете включить автоматический вход пользователей в Bambu by Sprout Social (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Bambu by Sprout Social, вам потребуется:

- подписка Azure AD;
- подписка Bambu by Sprout Social с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Bambu by Sprout Social из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-bambu-by-sprout-social-from-the-gallery"></a>Добавление Bambu by Sprout Social из коллекции
Чтобы настроить интеграцию Bambu by Sprout Social с Azure AD, нужно добавить Bambu из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Bambu by Sprout Social из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Bambu by Sprout Social**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_search.png)

5. На панели результатов выберите **Bambu by Sprout Social** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Bambu by Sprout Social с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Bambu by Sprout Social соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Bambu by Sprout Social.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Bambu by Sprout Social.

Чтобы настроить и проверить единый вход Azure AD в Bambu by Sprout Social, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Bambu by Sprout Social](#creating-a-bambu-by-sprout-social-test-user)** требуется для создания в Bambu by Sprout Social пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в Bambu by Sprout Social.

**Чтобы настроить единый вход Azure AD в Bambu by Sprout Social, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Bambu by Sprout Social** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_samlbase.png)

3. В разделе **Домен и URL-адреса единого входа в Bambu by Sprout Social** не нужно выполнять никаких действий, поскольку приложение уже предварительно интегрировано с Azure. 

    ![Настройка единого входа](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_url.png)

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_400.png)
    
6. В разделе **Настройка Bambu by Sprout Social** щелкните **Настроить Bambu by Sprout Social**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_configure.png) 

7. Чтобы настроить единый вход на стороне **Bambu by Sprout Social**, нужно отправить скачанный **XML метаданных** и **URL-адрес службы единого входа SAML** в [службу поддержки Bambu by Sprout Social](mailto:support@getbambu.com). Это позволит службе поддержки правильно настроить подключение единого входа SAML на обоих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

<!--### Next steps

To ensure users can sign-in to Bambu by Sprout Social after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Bambu by Sprout Social prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Bambu by Sprout Social in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Bambu by Sprout Social users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bambubysproutsocial-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bambubysproutsocial-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bambubysproutsocial-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bambubysproutsocial-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Name** (Имя) введите **Britta Simon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-bambu-by-sprout-social-test-user"></a>Создание тестового пользователя для Bambu by Sprout Social

Приложение поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Bambu by Sprout Social.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении Bambu by Sprout Social, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Bambu by Sprout Social**.

    ![Настройка единого входа](./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Bambu by Sprout Social на панели доступа, вы автоматически войдете в приложение Bambu by Sprout Social. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bambubysproutsocial-tutorial/tutorial_general_203.png

