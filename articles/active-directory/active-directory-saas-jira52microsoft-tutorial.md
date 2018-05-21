---
title: Руководство по интеграции Azure Active Directory с приложением единого входа Microsoft Azure Active Directory для JIRA 5.2 | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и приложением единого входа Microsoft Azure Active Directory для JIRA 5.2.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 785a75d3d9a748e3ddf01099fc844b5c1099d0b9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-microsoft-azure-active-directory-single-sign-on-for-jira-52"></a>Руководство по интеграции Azure Active Directory с приложением единого входа Microsoft Azure Active Directory для JIRA 5.2

В этом руководстве описано, как интегрировать приложение единого входа Microsoft Azure Active Directory для JIRA 5.2 с Azure Active Directory (Azure AD).

Интеграция приложения единого входа Microsoft Azure Active Directory для JIRA 5.2 с Azure AD предоставляет следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению единого входа Microsoft Azure Active Directory для JIRA 5.2.
- Вы можете включить автоматический вход пользователей в приложение единого входа Microsoft Azure Active Directory для JIRA 5.2 (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>ОПИСАНИЕ

Используйте учетную запись Microsoft Azure Active Directory с сервером Atlassian JIRA, чтобы включить единый вход. Таким образом все пользователи вашей организации смогут использовать учетные данные Azure AD для входа в приложение JIRA. Этот подключаемый модуль для федерации использует SAML 2.0.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением единого входа Microsoft Azure Active Directory для JIRA 5.2, вам потребуется следующее:

- подписка Azure AD;
- выпуск JIRA Core and Software 5.2, установленный и настроенный в 64-разрядной версии Windows;
- поддержка HTTPS на сервере JIRA;
- поддерживаемые версии подключаемого модуля JIRA указаны в разделе ниже;
- сервер JIRA должен быть доступен через Интернет, в частности, на странице входа Azure AD для аутентификации, и должен иметь возможность получать маркер из Azure AD;
- в JIRA должны быть настроены учетные данные администратора;
- в JIRA должна быть отключена поддержка WebSudo;
- в серверном приложении JIRA должен быть создан тестовый пользователь.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду JIRA для проверки действий в этом руководстве. Сначала протестируйте интеграцию в среде разработки или промежуточной среде приложения, а затем используйте ее в рабочей среде.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

**Поддерживаемые версии:**

*   JIRA Core и JIRA Software: 5.2;
*   также JIRA поддерживает 6.0 и 7.2.0. Подробнее см. в руководстве по [использованию единого входа Microsoft Azure Active Directory для JIRA](./active-directory-saas-jiramicrosoft-tutorial.md).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление приложения единого входа Microsoft Azure Active Directory для JIRA 5.2 из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-microsoft-azure-active-directory-single-sign-on-for-jira-52-from-the-gallery"></a>Добавление приложения единого входа Microsoft Azure Active Directory для JIRA 5.2 из коллекции
Чтобы настроить интеграцию приложения единого входа Microsoft Azure Active Directory для JIRA 5.2 с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение единого входа Microsoft Azure Active Directory для JIRA 5.2 из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Microsoft Azure Active Directory single sign-on for JIRA 5.2**, выберите **Microsoft Azure Active Directory single sign-on for JIRA 5.2** в области результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Приложение единого входа Microsoft Azure Active Directory для JIRA 5.2 в списке результатов](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описывается настройка и проверка единого входа Azure AD в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2 с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2 соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2.

Чтобы настроить и проверить единый вход Azure AD в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2, вам потребуется выполнить приведенные далее действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения единого входа Microsoft Azure Active Directory для JIRA 5.2](#create-a-singlesign-onforjira5.2-test-user)** требуется, чтобы в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2 существовал пользователь Britta Simon, связанный с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описывается, как включить единый вход Azure AD на портале Azure и настроить его в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2.

**Чтобы настроить единый вход Azure AD в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2, сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **единого входа Microsoft Azure Active Directory для JIRA 5.2** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_samlbase.png)

3. В разделе **Домены и URL-адреса приложения единого входа Microsoft Azure Active Directory для JIRA 5.2** выполните следующие действия.

    ![Сведения о домене и URL-адресах приложения единого входа Microsoft Azure Active Directory для JIRA 5.2](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<domain:port>/plugins/servlet/saml/auth`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<domain:port>/`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<domain:port>/plugins/servlet/saml/auth`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Если это именованный URL-адрес, то порт указывать необязательно. Эти значения предоставляются во время настройки подключаемого модуля JIRA, которая описывается далее в этом руководстве.

4. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.
    
    ![Настройка единого входа](./media/active-directory-saas-msaadssojira5.2-tutorial/tutorial_metadataurl.png)
     
5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_400.png)

6. В другом окне веб-браузера войдите в свой экземпляр JIRA в качестве администратора.

7. Наведите указатель мыши на шестеренку и щелкните **Add-ons** (Надстройки).
    
    ![Настройка единого входа](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon1.png)

8. На вкладке "Надстройки" щелкните **Управление надстройками**.

    ![Настройка единого входа](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon7.png)

9. Скачайте подключаемый модуль из [Центра загрузки Майкрософт](https://www.microsoft.com/en-us/download/details.aspx?id=56521). Вручную передайте подключаемый модуль, предоставленный корпорацией Майкрософт, воспользовавшись меню **Upload add-on** (Отправка надстройки). Загрузка подключаемого модуля выполняется в соответствии с [соглашением об обслуживании Майкрософт](https://www.microsoft.com/en-us/servicesagreement/).

    ![Настройка единого входа](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon12.png)

10. После установки подключаемый модуль появится в разделе **Установлено пользователем**. Щелкните **Configure** (Настройка), чтобы настроить новый подключаемый модуль.

    ![Настройка единого входа](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon13.png)

11. Выполните следующие действия на странице настройки:

    ![Настройка единого входа](.\media\active-directory-saas-msaadssojira5.2-tutorial\addon52.png)

    > [!TIP]
    > Убедитесь, что с приложением сопоставлен только один сертификат, чтобы при разрешении метаданных не возникла ошибка. Если имеется несколько сертификатов, то при разрешении метаданных администратор увидит сообщение об ошибке.

    a. В текстовое поле **URL-адрес метаданных** вставьте значение **URL-адреса метаданных федерации приложений**, скопированное на портале Azure, и нажмите кнопку **Разрешить**. Будет прочитан URL-адрес метаданных поставщика удостоверений, а также будут заполнены все поля сведений.

    Б. Скопируйте значения **идентификатора, URL-адреса ответа и URL-адреса входа** и вставьте их в **соответствующие поля** в разделе **Домены и URL-адреса приложения единого входа Microsoft Azure Active Directory для JIRA 5.2** на портале Azure.

    c. В поле **Имя кнопки входа** введите имя кнопки, которую должны видеть на экране входа пользователи вашей организации.

    d. Для параметра **SAML User ID Locations** (Расположения идентификатора пользователя SAML) выберите значение **User ID is in the NameIdentifier element of the Subject statement** (Идентификатор пользователя указан в элементе NameIdentifier утверждения Subject) или **User ID is in an Attribute element** (Идентификатор пользователя указан в элементе Attribute).  Этим идентификатором должен быть идентификатор пользователя JIRA. Если идентификатор пользователя не совпадет, система не позволит пользователям выполнить вход.

    > [!Note]
    > По умолчанию идентификатор пользователя SAML указан в идентификаторе имени. Его можно заменить атрибутом и ввести имя соответствующего атрибута. 
    
    д. Если выбран параметр **Идентификатор пользователя указан в элементе Attribute**, то в текстовом поле **Имя атрибута** введите имя атрибута, в котором ожидается идентификатор пользователя. 

    f. При использовании федеративного домена (например, AD FS и т. д.) для Azure AD выберите параметр **Включить обнаружение домашней области** и настройте **доменное имя**.
    
    ж. В поле **Доменное имя** введите доменное имя, если вы используете вход на основе AD FS.

    h. Установите флажок **Enable Single Sign out** (Включить единый выход), если после выхода пользователя из JIRA требуется выходить и из Azure AD. 

    i. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

    > [!NOTE]
    > Дополнительные сведения об установке и устранении неполадок см. в [руководстве администратора соединителя единого входа MS JIRA](ms-confluence-jira-plugin-adminguide.md) и в [часто задаваемых вопросах](ms-confluence-jira-plugin-faq.md).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](.\media\active-directory-saas-msaadssojira5.2-tutorial\create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-microsoft-azure-active-directory-single-sign-on-for-jira-52-test-user"></a>Создание тестового пользователя приложения единого входа Microsoft Azure Active Directory для JIRA 5.2

Чтобы пользователи Azure AD могли выполнять вход на локальный сервер JIRA, их необходимо подготовить в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2. Подготовка пользователей в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2 выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на локальный сервер JIRA в качестве администратора.

2. Наведите указатель мыши на шестеренку и щелкните **User management** (Управление пользователями).

    ![Добавление сотрудника](.\media\active-directory-saas-msaadssojira5.2-tutorial\user1.png) 

3. Вы будете перенаправлены на страницу доступа с правами администратора. Введите **пароль** и нажмите кнопку **Confirm** (Подтвердить).

    ![Добавление сотрудника](.\media\active-directory-saas-msaadssojira5.2-tutorial\user2.png) 

4. В разделе **User management** (Управление пользователями) щелкните **Create user** (Создать пользователя).

    ![Добавление сотрудника](.\media\active-directory-saas-msaadssojira5.2-tutorial\user3.png) 

5. На странице **Create New User** (Создание пользователя) выполните следующие действия.

    ![Добавление сотрудника](.\media\active-directory-saas-msaadssojira5.2-tutorial\user4.png) 

    a. В текстовом поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    Б. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например Britta Simon.

    c. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Brittasimon@contoso.com.

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя.

    д. Щелкните **Create user** (Создать пользователя).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к приложению единого входа Microsoft Azure Active Directory для JIRA 5.2, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении единого входа Microsoft Azure Active Directory для JIRA 5.2, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений щелкните **Microsoft Azure Active Directory single sign-on for JIRA 5.2**.

    ![Ссылка на приложение единого входа Microsoft Azure Active Directory для JIRA 5.2 в списке приложений](.\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_singlesign-onforjira5.2_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Microsoft Azure Active Directory single sign-on for JIRA 5.2 на панели доступа, вы должны автоматически войти в приложение единого входа Microsoft Azure Active Directory для JIRA 5.2.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_01.png
[2]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_02.png
[3]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_03.png
[4]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_04.png

[100]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_100.png

[200]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_200.png
[201]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_201.png
[202]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_202.png
[203]: .\media\active-directory-saas-msaadssojira5.2-tutorial\tutorial_general_203.png
