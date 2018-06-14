---
title: Руководство по интеграции Azure Active Directory с порталом управления облачными службами для Microsoft Azure | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и порталом управления облачными службами для Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 5dfd5377fa669322d0a61c71996badd45bc3d441
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346005"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Учебник. Интеграция Azure Active Directory с порталом управления облачными службами для Microsoft Azure

В этом учебнике описано, как интегрировать портал управления облачными службами для Microsoft Azure с Azure Active Directory (Azure AD).

Интеграция портала управления облачными службами для Microsoft Azure с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ пользователей к порталу управления облачными службами для Microsoft Azure.
- Вы можете включить автоматический вход пользователей в портал управления облачными службами (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с порталом управления облачными службами для Microsoft Azure, вам потребуется следующее:

- подписка Azure AD;
- подписка портала управления облачными службами для Microsoft Azure с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление портала управления облачными службами для Microsoft Azure из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Добавление портала управления облачными службами для Microsoft Azure из коллекции
Чтобы настроить интеграцию с портала управления облачными службами для Microsoft Azure с Azure AD, необходимо добавить портал управления облачными службами для Microsoft Azure из коллекции в список управляемых приложений SaaS.

**Чтобы добавить портал управления облачными службами для Microsoft Azure из коллекции, выполните приведенные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Портал управления облачными службами для Microsoft Azure**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_search.png)

5. В области результатов выберите **Портал управления облачными службами для Microsoft Azure** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описывается настройка и проверка единого входа Azure AD на портале управления облачными службами в Microsoft Azure с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь на портале управления облачными службами для Microsoft Azure соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем на портале управления облачными службами для Microsoft Azure.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** на портале управления облачными службами для Microsoft Azure.

Чтобы настроить и проверить единый вход Azure AD на портал управления облачными службами для Microsoft Azure, вам потребуется выполнить приведенные далее действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя портала управления облачными службами для Microsoft Azure](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)** требуется для создания пользователя Britta Simon на портале управления облачными службами для Microsoft Azure, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описывается, как включить единый вход Azure AD на портале Azure и настроить его на портале управления облачными службами для Microsoft Azure.

**Чтобы настроить единый вход Azure AD на портал управления облачными службами для Microsoft Azure, вам потребуется выполнить приведенные далее действия.**

1. На портале управления Azure на странице интеграции с приложением **Портал управления облачными службами Microsoft Azure** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. В разделе **Домен и URL-адреса портала управления облачными службами для Microsoft Azure** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки портала управления облачными службами для Microsoft Azure](mailto:jczernuszka@newsignature.com). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_general_400.png)

6. В разделе **Настройка портала управления облачными службами для Microsoft Azure** щелкните **Настроить портал управления облачными службами для Microsoft Azure**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Для настройки единого входа на стороне **портала управления облачными службами для Microsoft Azure** необходимо отправить загруженный **сертификат**, **URL-адрес выхода**, **URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** в [службу поддержки портала управления облачными службами для Microsoft Azure](mailto:jczernuszka@newsignature.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Создание тестового пользователя портала управления облачными службами для Microsoft Azure

Цель этого раздела — создать пользователя с именем Britta Simon на портале управления облачными службами для Microsoft Azure. Обратитесь в [службу поддержки портала управления облачными службами для Microsoft Azure](mailto:jczernuszka@newsignature.com), чтобы добавить пользователей в учетную запись портала.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к порталу управления облачными службами для Microsoft Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon порталу управления облачными службами для Microsoft Azure, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **портал управления облачными службами для Microsoft Azure**.

    ![Настройка единого входа](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
Щелкнув элемент портала управления облачными службами для Microsoft Azure на панели доступа, вы автоматически войдете в приложение портала управления облачными службами для Microsoft Azure.

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png

