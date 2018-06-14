---
title: Руководство. Интеграция Azure Active Directory с UltiPro | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и UltiPro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: afc0f2b9-2eac-47ec-af04-65ed0fb0ca5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 32e3f40c39f12062cbc28fec51bb24564f22e9bf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348678"
---
# <a name="tutorial-azure-active-directory-integration-with-ultipro"></a>Руководство. Интеграция Azure Active Directory с UltiPro

В этом руководстве описано, как интегрировать UltiPro с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением UltiPro обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению UltiPro.
- Вы можете включить автоматический вход пользователей в UltiPro (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с UltiPro, вам потребуется:

- подписка Azure AD;
- подписка UltiPro с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление UltiPro из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ultipro-from-the-gallery"></a>Добавление UltiPro из коллекции
Чтобы настроить интеграцию UltiPro с Azure AD, необходимо добавить UltiPro из коллекции в список управляемых приложений SaaS.

**Чтобы добавить UltiPro из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **UltiPro**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_search.png)

5. На панели результатов выберите **UltiPro**, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в UltiPro с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в UltiPro соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в UltiPro.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в UltiPro.

Чтобы настроить и проверить единый вход Azure AD в UltiPro, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя UltiPro](#creating-a-ultipro-test-user)** требуется для того, чтобы в UltiPro существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении UltiPro.

**Чтобы настроить единый вход Azure AD в UltiPro, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **UltiPro** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_samlbase.png)

3. В разделе **Домены и URL-адреса приложения UltiPro** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.ultipro.com/`|
    | `https://<companyname>.ultiproworkplace.com?cpi=AZUREADISSSUERURL`|
    | ` https://<companyname>.ultipro.ca`|
    
    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.ultipro.com/adfs/services/trust`|
    | `https://<companyname>.ultiproworkplace.com/adfs/services/trust`|
    | `https://<companyname>.ultipro.ca/adfs/services/trust`|
    
    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.ultipro.com/<instancename>`|
    | `https://<companyname>.ultiproworkplace.com/<instancename>`|
    | `https://<companyname>.ultipro.ca/<instancename>`|
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов UltiPro](https://www.ultimatesoftware.com/ContactUs). 

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_general_400.png)
    
7. В разделе **Конфигурация UltiPro** щелкните **Настроить UltiPro**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_configure.png) 

8. Чтобы настроить единый вход на стороне **UltiPro**, нужно отправить скачанный **сертификат в кодировке Base64, URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [группе поддержки UltiPro](https://www.ultimatesoftware.com/ContactUs).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-ultipro-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-ultipro-test-user"></a>Создание тестового пользователя UltiPro

В этом разделе описано, как создать пользователя Britta Simon в приложении UltiPro. Обратитесь к [группе поддержки UltiPro](https://www.ultimatesoftware.com/ContactUs), чтобы добавить пользователей в учетную запись UltiPro. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к UltiPro.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению UltiPro, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **UltiPro**.

    ![Настройка единого входа](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент UltiPro на панели доступа, вы автоматически войдете в приложение UltiPro.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_203.png

