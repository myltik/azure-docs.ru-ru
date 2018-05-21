---
title: Руководство по интеграции Azure Active Directory с ArcGIS Online | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: fc6bf53e379fec266062753e550ef1c613816443
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Руководство по интеграции Azure Active Directory с ArcGIS Online

В этом руководстве описано, как интегрировать ArcGIS Online с Azure Active Directory (Azure AD).

Интеграция ArcGIS Online с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к ArcGIS Online.
- Вы можете включить автоматический вход пользователей в ArcGIS Online (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ArcGIS Online, вам потребуется:

- подписка Azure AD;
- подписка ArcGIS Online с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ArcGIS Online из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-arcgis-online-from-the-gallery"></a>Добавление ArcGIS Online из коллекции
Чтобы настроить интеграцию ArcGIS Online с Azure AD, необходимо добавить ArcGIS Online из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ArcGIS Online из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **ArcGIS Online**, выберите **ArcGIS Online** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ArcGIS Online в списке результатов](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ArcGIS Online с использованием данных тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в ArcGIS Online соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ArcGIS Online.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ArcGIS Online.

Чтобы настроить и проверить единый вход Azure AD в ArcGIS Online, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ArcGIS Online](#create-a-arcgis-online-test-user)** нужно для того, чтобы в ArcGIS Online также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как на портале Azure включить единый вход Azure AD и настроить его в приложении ArcGIS Online.

**Чтобы настроить единый вход Azure AD в ArcGIS Online, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **ArcGIS Online** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. В разделе **ArcGIS Online Domain and URLs** (Домены и URL-адреса приложения ArcGIS Online) выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения ArcGIS Online](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.maps.arcgis.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Для получения этих значений обратитесь в [группу поддержки ArcGIS Online](http://support.esri.com/en/). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на свой корпоративный веб-сайт ArcGIS в качестве администратора.

7. Щелкните **EDIT SETTINGS** (Изменить параметры).

    ![Изменение параметров](./media/active-directory-saas-arcgis-tutorial/ic784742.png "Изменение параметров")

8. Щелкните **Security**(Безопасность).

    ![Безопасность](./media/active-directory-saas-arcgis-tutorial/ic784743.png "Безопасность")

9. В разделе **Enterprise Logins** (Корпоративные имена для входа) установите флажок **Set Identity Provider** (Задать поставщик удостоверений).

    ![Корпоративные имена входа](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Корпоративные имена входа")

10. На странице **Назначение поставщика удостоверений** выполните следующие действия.
   
    ![Назначение поставщика удостоверений](./media/active-directory-saas-arcgis-tutorial/ic784745.png "Назначение поставщика удостоверений")
   
    a. В текстовое поле **Name** (Имя) введите название своей организации.

    Б. Для параметра **Metadata for the Enterprise Identity Provider will be supplied using** (В предоставлении метаданных для корпоративного поставщика удостоверений будет использоваться) выберите значение **Файл**.

    c. Чтобы отправить загруженный файл метаданных, нажмите кнопку **Выбрать файл**.

    d. Щелкните **SET IDENTITY PROVIDER** (Задать поставщик удостоверений).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-arcgis-online-test-user"></a>Создание тестового пользователя ArcGIS Online

Чтобы пользователи Azure AD могли выполнять вход в ArcGIS Online, они должны быть подготовлены в ArcGIS Online.  
В случае ArcGIS Online подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в клиент **ArcGIS** .

2. Щелкните **INVITE MEMBERS** (Пригласить участников).
   
    ![Приглашение участников](./media/active-directory-saas-arcgis-tutorial/ic784747.png "Приглашение участников")

3. Щелкните переключатель **Add members automatically without sending an email** (Добавлять участников автоматически без отправки электронного сообщения) и нажмите кнопку **NEXT** (Далее).
   
    ![Автоматическое добавление участников](./media/active-directory-saas-arcgis-tutorial/ic784748.png "Автоматическое добавление участников")

4. На странице диалогового окна **Участники** выполните следующие действия.
   
     ![Добавление и просмотр](./media/active-directory-saas-arcgis-tutorial/ic784749.png "Добавление и просмотр")
    
     a. Введите в поля **Email** (Адрес электронной почты), **First name** (Имя) и **Last name** (Фамилия) соответствующие данные действующей учетной записи Azure AD, которую необходимо подготовить.
  
     Б. Нажмите кнопку **ADD AND REVIEW** (Добавить и просмотреть).
5. Просмотрите введенные данные и нажмите кнопку **ADD MEMBERS** (Добавить участников).
   
    ![Добавление участника](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Добавление участника")
        
    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ArcGIS Online.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ArcGIS Online, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **ArcGIS Online**.

    ![Ссылка на ArcGIS Online в списке "Приложения"](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "ArcGIS Online" на панели доступа, вы автоматически войдете в приложение ArcGIS Online.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

