---
title: Руководство. Интеграция Azure Active Directory с Envi MMIS | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 0d338f7f56e668c25378403e88a6532b8b8573c5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344275"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Руководство. Интеграция Azure Active Directory с Envi MMIS

В этом руководстве описано, как интегрировать приложение Envi MMIS с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Envi MMIS обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Envi MMIS.
- Вы можете включить автоматический вход пользователей в Envi MMIS (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Envi MMIS, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа Envi MMIS.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Envi MMIS из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-envi-mmis-from-the-gallery"></a>Добавление Envi MMIS из коллекции
Чтобы настроить интеграцию Envi MMIS с Azure AD, необходимо добавить Envi MMIS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Envi MMIS из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Envi MMIS**, выберите **Envi MMIS** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Envi MMIS в списке результатов](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Envi MMIS с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Envi MMIS соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Envi MMIS.

Чтобы настроить и проверить единый вход Azure AD в Envi MMIS, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Envi MMIS](#create-an-envi-mmis-test-user)** нужно для того, чтобы в Envi MMIS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Envi MMIS.

**Чтобы настроить единый вход Azure AD в Envi MMIS, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Envi MMIS** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Envi MMIS** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Envi MMIS](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.<CUSTOMER DOMAIN>.com/Account`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Envi MMIS](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Envi MMIS](mailto:support@ioscorp.com).

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/tutorial_general_400.png)

7. В другом окне веб-браузера войдите на свой веб-сайт Envi MMIS в качестве администратора.

8. Щелкните вкладку **My Domain** (Мой домен).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/configure1.png)

9. Нажмите кнопку **Изменить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/configure2.png)

10. Установите флажок **Use remote authentication** (Использовать удаленную проверку подлинности), а затем выберите **HTTP Redirect** (Перенаправление HTTP) из раскрывающегося списка **Authentication Type** (Тип проверки подлинности).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/configure3.png)

11. Выберите вкладку **Resources** (Ресурсы) и щелкните **Upload Metadata** (Отправка метаданных).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/configure4.png)

12. Во всплывающем меню **Upload Metadata** (Отправка метаданных) сделайте следующее:

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/configure5.png)

    a. Выберите вариант **File** (Файл) из раскрывающегося списка **Upload From** (Отправить из).

    Б. Чтобы отправить файл метаданных, загруженный с портала Azure, выберите значок **выбора файла**.

    c. Нажмите кнопку **ОК**.

13. После отправки загруженного файла метаданных поля будут заполнены автоматически. Нажмите кнопку **Update** (Обновить).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-envimmis-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-envimmis-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-envimmis-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-envimmis-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-envi-mmis-test-user"></a>Создание тестового пользователя Envi MMIS

Чтобы пользователи Azure AD могли выполнять вход в Envi MMIS, они должны быть подготовлены для Envi MMIS.  
В случае с Envi MMIS подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на веб-сайт компании Envi MMIS в качестве администратора.

2. Щелкните вкладку **User List** (Список пользователей).

    ![Добавление сотрудника](./media/active-directory-saas-envimmis-tutorial/user1.png)

3. Нажмите кнопку **Add User** (Добавить пользователя).

    ![Добавление сотрудника](./media/active-directory-saas-envimmis-tutorial/user2.png)

4. В разделе **Add User** (Добавление пользователя) выполните следующие действия.

    ![Добавление сотрудника](./media/active-directory-saas-envimmis-tutorial/user3.png)

    a. В текстовое поле **User Name** (Имя пользователя) введите имя пользователя для учетной записи Britta Simon, например **brittasimon@contoso.com**.
    
    Б. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию, например **Simon**.

    d. В текстовое поле **Title** (Обращение) введите обращение к пользователю.
    
    д. В текстовое поле **Email Address** (Электронная почта) введите адрес электронной почты для учетной записи Britta Simon, например **brittasimon@contoso.com**.

    f. В текстовое поле **SSO User Name** (Имя пользователя для единого входа) введите имя пользователя для учетной записи Britta Simon, например **brittasimon@contoso.com**.

    ж. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Envi MMIS.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Envi MMIS, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Envi MMIS**.

    ![Ссылка на Envi MMIS в списке приложений](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Envi MMIS на панели доступа, вы автоматически войдете в приложение Envi MMIS.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_203.png

