---
title: Руководство по интеграции Azure Active Directory с OpenAthens | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 3b19f3c7ed84a63f48320a2c7af8d06a9cc5deb4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348908"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Руководство по интеграции Azure Active Directory с OpenAthens

В этом руководстве описано, как интегрировать OpenAthens с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением OpenAthens обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к OpenAthens.
- Вы можете включить автоматический вход пользователей в OpenAthens (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с OpenAthens, вам потребуется:

- подписка Azure AD;
- подписка OpenAthens с поддержкой единого входа.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление OpenAthens из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-openathens-from-the-gallery"></a>Добавление OpenAthens из коллекции
Чтобы настроить интеграцию OpenAthens с Azure AD, необходимо добавить OpenAthens из коллекции в список управляемых приложений SaaS.

**Добавление OpenAthens из коллекции**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Область "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **OpenAthens**, выберите **OpenAthens** на панели результатов и нажмите кнопку **Добавить**.

    ![OpenAthens в списке результатов](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в OpenAthens с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в OpenAthens соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в OpenAthens.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в OpenAthens.

Чтобы настроить и проверить единый вход Azure AD в OpenAthens, вам потребуется выполнить действия в следующих стандартных блоках.

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создайте тестового пользователя Azure AD](#create-an-azure-ad-test-user) для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя OpenAthens](#create-a-openathens-test-user) требуется для того, чтобы в OpenAthens существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначьте тестового пользователя Azure AD](#assign-the-azure-ad-test-user), чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. [Проверка единого входа](#test-single-sign-on) необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении OpenAthens.

**Настройка единого входа Azure AD в OpenAthens**

1. На портале Azure на странице интеграции с приложением **OpenAthens** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить функцию единого входа, в диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. В разделе **Домены и URL-адреса приложения OpenAthens** введите значение `https://login.openathens.net/saml/2/metadata-sp` в текстовом поле **Идентификатор**.

    ![Сведения о домене и URL-адресах единого входа для приложения OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. В разделе **Сертификат подписи SAML** выберите **XML метаданных** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата для подписи SAML](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Нажмите кнопку **Сохранить**.

    ![Кнопка "Сохранить" в окне единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на свой корпоративный сайт OpenAthens в качестве администратора.

7. Выберите **Connections** (Подключения) в списке на вкладке **Management** (Управление). 

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Выберите **SAML 1.1/2.0**, а затем нажмите кнопку **Configure** (Настроить).

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Чтобы добавить конфигурацию, нажмите кнопку **​​Browse** (Обзор), чтобы передать XML-файл метаданных, скачанный на портале Azure, а затем нажмите кнопку **Add** (Добавить).

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Выполните следующие действия на вкладке **Details** (Сведения).

    ![Настройка единого входа](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. Из списка **Display name mapping** (Сопоставление отображаемого имени) выберите **Use Attribute** (Использовать атрибут).

    Б. В текстовом поле **Display name attribute** (Атрибут отображаемого имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Из списка **Unique user mapping** (Сопоставление уникального пользователя) выберите **Use Attribute** (Использовать атрибут).

    d. В текстовом поле **Unique user attribute** (Атрибут уникального пользователя имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    д. В разделе **Status** (Состояние) установите все три флажка.

    f. Из списка **Create local accounts** (Создание локальных учетных записей) выберите **automatically** (Автоматически).

    ж. Щелкните **Save changes** (Сохранить изменения).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Чтобы узнать больше, ознакомьтесь со [встроенной документацией Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Создание тестового пользователя в Azure AD**

1. На портале Azure в области слева щелкните **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите адрес электронной почты пользователя BrittaSimon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в текстовом поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-an-openathens-test-user"></a>Создание тестового пользователя OpenAthens

OpenAthens поддерживает JIT-подготовку, и пользователи создаются автоматически после успешной аутентификации. Не нужно выполнять никаких действий в этом разделе.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к OpenAthens.

![Назначение роли пользователя][200] 

**Назначение пользователя Britta Simon в OpenAthens**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, затем в раздел **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке **Приложения** выберите **OpenAthens**.

    ![Ссылка на OpenAthens в списке приложений](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В списке **Пользователи и группы** выберите **Britta Simon**.

6. В списке **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В области **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент **OpenAthens** на панели доступа, вы автоматически войдете в приложение OpenAthens.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* Руководства по интеграции приложений SaaS с Azure Active Directory см. в статье [Интеграция приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md).
* Дополнительные сведения о доступе к приложениям и едином входе с помощью Azure Active Directory см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

