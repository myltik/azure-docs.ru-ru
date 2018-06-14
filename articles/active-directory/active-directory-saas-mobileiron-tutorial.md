---
title: Руководство по интеграции Azure Active Directory с MobileIron | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: jeedes
ms.openlocfilehash: 5d98e57a2af71345b404f02b81762d5e0bc0961b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350242"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Руководство по интеграции Azure Active Directory с MobileIron

Из этого руководства вы узнаете, как интегрировать MobileIron с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением MobileIron обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к MobileIron.
- Вы можете включить автоматический вход пользователей в MobileIron (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с MobileIron, вам потребуется следующее:

- подписка Azure AD;
- Подписка MobileIron с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление MobileIron из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mobileiron-from-the-gallery"></a>Добавление MobileIron из коллекции
Чтобы настроить интеграцию MobileIron с Azure AD, необходимо добавить MobileIron из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MobileIron из коллекции, выполните инструкции ниже.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **MobileIron**, выберите **MobileIron** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![MobileIron в списке результатов](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в MobileIron с помощью тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в MobileIron соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MobileIron.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в MobileIron.

Чтобы настроить и проверить единый вход Azure AD в MobileIron, вам потребуется выполнить действия в указанных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создав тестового пользователя MobileIron](#create-a-mobileiron-test-user)** Britta Simon, мы сможем связать его с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Из этого раздела вы узнаете, как включить единый вход Azure AD на портале Azure и настроить его в приложении MobileIron.

**Чтобы настроить единый вход AzureAD в MobileIron, выполните инструкции ниже.**

1. На портале Azure на странице интеграции с приложением **MobileIron** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_samlbase.png)

3. Чтобы настроить приложение в режиме, инициированном **IDP**, в разделе **MobileIron Domain and URLs** (Домен и URL-адреса приложения MobileIron) сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения MobileIron](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.mobileiron.com/<key>`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Домен и URL-адреса единого входа для приложения MobileIron](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<host>.mobileiron.com/user/login.html`
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Значения ключа и узла вы получите на портале администрирования MobileIron, как описано далее в этом руководстве.

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-mobileiron-tutorial/tutorial_general_400.png)

7. В другом окне веб-браузера войдите на корпоративный сайт MobileIron в качестве администратора.

8. Последовательно выберите пункты **Администратор** > **Удостоверение**.

   * Выберите действие **AAD** (Добавить) в поле **Info on Cloud IDP Setup** (Информация о настройке IDP в облаке).

    ![Кнопка настройки единого входа для администратора](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_admin.png)

9. Скопируйте значения **ключа** и **узла** и дополните ими URL-адреса в разделе **Домены и URL-адреса приложения MobileIron** на портале Azure.

    ![Кнопка настройки единого входа для администратора](./media/active-directory-saas-mobileiron-tutorial/key.png)

10. В разделе **Export metadata file from AAD and import to MobileIron Cloud Field** (Экспорт файла метаданных из AAD и импорт данных в облако MobileIron) и щелкните **Choose File** (Выбрать файл), чтобы отправить метаданные, скачанные с портала Azure. Когда процесс завершится, нажмите кнопку **Done** (Готово).
 
    ![Настройка единого входа для администратора — кнопка метаданных](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-mobileiron-test-user"></a>Создание тестового пользователя MobileIron

Чтобы пользователи Azure AD могли выполнять вход в MobileIron, они должны быть подготовлены для MobileIron.  
Для MobileIron подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративный веб-сайт MobileIron в качестве администратора.

2. Выберите пункт **Users** (Пользователи) и щелкните **Add** (Добавить)  > **Single User** (Один пользователь).

    ![Настройка единого входа — кнопка "Пользователь"](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_user.png)

3. На странице диалогового окна **Single User** (Один пользователь) выполните инструкции ниже.

    ![Настройка единого входа— кнопка "Добавить пользователя"](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. В текстовое поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    Б. В текстовое поле **First Name** (Имя) введите имя пользователя, например Britta.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.
    
    d. Нажмите кнопку **Done**(Готово).  

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе пользователю Britta Simon предоставляется разрешение на использование единого входа Azure для доступа к MobileIron.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в MobileIron, выполните инструкции ниже.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **MobileIron**.

    ![Ссылка на MobileIron в списке "Приложения"](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент MobileIron на панели доступа, вы автоматически войдете в приложение MobileIron.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_203.png

