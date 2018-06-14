---
title: Руководство по интеграции Azure Active Directory с T&E Express | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в T&E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 8d3980b6169c8a9fc60c04e527814159082a46e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350225"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Руководство по интеграции Azure Active Directory с T&E Express

В этом руководстве описано, как интегрировать приложение T&E Express с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением T&E Express обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к T&E Express.
- Вы можете включить автоматический вход пользователей в T&E Express (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с T&E Express, вам потребуется:

- подписка Azure AD;
- подписка T&E Express с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление T&E Express из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-te-express-from-the-gallery"></a>Добавление T&E Express из коллекции
Чтобы настроить интеграцию T&E Express с Azure AD, необходимо добавить T&E Express из коллекции в список управляемых приложений SaaS.

**Чтобы добавить T&E Express из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **T&E Express**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_search.png)

5. На панели результатов выберите **T&E Express** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в T&E Express с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в T&E Express соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в T&E Express.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в T&E Express.

Чтобы настроить и проверить единый вход Azure AD в T&E Express, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя T&E Express](#creating-a-te-express-test-user)** требуется для создания пользователя Britta Simon в T&E Express, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure, а также как его настроить в приложении T&E Express.

**Чтобы настроить единый вход Azure AD в T&E Express,сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **T&E Express** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

3. В разделе **Домены и URL-адреса приложения T&E Express** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. В текстовом поле **Идентификатор** введите значение `https://<domain>.tyeexpress.com`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`.

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо указать фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки T&E Express](http://www.tyeexpress.com/contacto.aspx).

5. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_400.png)

8. Для настройки единого входа в **T&E Express** войдите в приложение T&E Express без единого входа SAML с использованием учетных данных администратора.

9. На вкладке **Администрирование** щелкните **SAML domain** (Домен SAML), чтобы открыть страницу параметров SAML.

    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tye-SAML.png)

10. Измените для параметра **Activar(Activate)** (Активировать) значение с **Нет** на **SI(Yes)** (Да). В текстовом поле **Identity Provider Metadata** (Метаданные поставщика удостоверений) вставьте код XML метаданных, скачанный с портала Azure.

    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tyeAdmin.png)

11. Щелкните кнопку **Guardar(Save)** (Сохранить), чтобы сохранить параметры. 


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-te-express-test-user"></a>Создание тестового пользователя T&E Express

Чтобы пользователи Azure AD могли выполнять вход в систему T&E Express, они должны быть подготовлены для нее.  
В случае T&E Express подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Войдите на веб-сайт T&E Express компании в качестве администратора.

2. В разделе "Администрирование" щелкните "Пользователи", чтобы открыть главную страницу пользователей.

    ![Добавление сотрудника](./media/active-directory-saas-tyeexpress-tutorial/tye-adminusers.png)

3. На домашней странице щелкните **+** для добавления пользователей.

    ![Добавление сотрудника](./media/active-directory-saas-tyeexpress-tutorial/tye-usershome.png)

4. Введите все обязательные сведения в форме и нажмите кнопку "Сохранить" для сохранения данных.

    ![Добавление сотрудника](./media/active-directory-saas-tyeexpress-tutorial/tye-usersadd.png)

    ![Добавление сотрудника](./media/active-directory-saas-tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к T&E Express, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в T&E Express, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **T&E Express**.

    ![Настройка единого входа](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку T&E Express на панели доступа, вы автоматически войдете в приложение T&E Express.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_203.png

