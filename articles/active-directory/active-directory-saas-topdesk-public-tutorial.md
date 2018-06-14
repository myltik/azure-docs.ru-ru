---
title: Руководство. Интеграция Azure Active Directory с TOPdesk - Public | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TOPdesk - Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 76b75ffde280047f8ed6c2d4173e905df1d7a658
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352078"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Учебник. Интеграция Azure Active Directory с TOPdesk - Public

В этом руководстве описано, как интегрировать TOPdesk - Public с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением TOPdesk - Public обеспечивает следующие преимущества.

- C помощью Azure AD вы можете контролировать доступ к TOPdesk - Public.
- Вы можете включить автоматический вход пользователей в TOPdesk - Public (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с TOPdesk - Public, вам потребуется следующее:

- подписка Azure AD;
- Подписка TOPdesk — Public с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление TOPdesk - Public из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-topdesk---public-from-the-gallery"></a>Добавление TOPdesk - Public из коллекции
Чтобы настроить интеграцию TOPdesk - Public с Azure AD, необходимо добавить TOPdesk - Public из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TOPdesk - Public из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **TOPdesk - Public**, выберите **TOPdesk - Public** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![TOPdesk - Public в списке результатов](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в TOPdesk - Public с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в TOPdesk - Public соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TOPdesk - Public.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TOPdesk - Public.

Чтобы настроить и проверить единый вход Azure AD в TOPdesk - Public, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TOPdesk - Public](#create-a-topdesk---public-test-user)** требуется для того, чтобы в TOPdesk - Public существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении TOPdesk - Public.

**Чтобы настроить единый вход Azure AD в TOPdesk - Public, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **TOPdesk - Public** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. В разделе **Домены и URL-адреса приложения TOPdesk - Public** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения TOPdesk - Public](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net`
    
    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net/tas/public/login/saml`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. URL-адрес ответа поясняется далее в этом руководстве. Чтобы получить эти данные, обратитесь в [службу поддержки клиентов TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/).  

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация TOPdesk - Public** щелкните **Настройка TOPdesk - Public**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация TOPdesk - Public](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Выполните вход на веб-сайт **TOPdesk — Public** компании в качестве администратора.

8. В меню **TOPdesk** выберите пункт **Settings** (Параметры).
   
    ![Параметры](./media/active-directory-saas-topdesk-public-tutorial/ic790598.png "Параметры")

9. Щелкните **Параметры входа**.
   
    ![Параметры входа](./media/active-directory-saas-topdesk-public-tutorial/ic790599.png "Параметры входа")

10. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).
   
    ![Общие](./media/active-directory-saas-topdesk-public-tutorial/ic790600.png "Общие")

11. В разделе **Public** (Общедоступные) для конфигурации **SAML login** (Вход SAML) выполните следующие действия.
   
    ![Технические параметры](./media/active-directory-saas-topdesk-public-tutorial/ic790601.png "Технические параметры")
   
    a. Нажмите кнопку **Скачать** , чтобы скачать общедоступный файл метаданных, а затем сохраните его локально на компьютере.
   
    Б. Откройте скачанный файл метаданных и определите местоположение узла **AssertionConsumerService**.

    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Скопируйте значение **AssertionConsumerService**, вставьте это значение в текстовое поле **URL-адрес ответа** в разделе **Домен и URL-адреса TOPdesk - Public**.      
   
12. Чтобы создать файл сертификата, выполните следующие действия:
    
    ![Сертификат](./media/active-directory-saas-topdesk-public-tutorial/ic790606.png "Сертификат")
    
    a. Откройте скачанный файл метаданных на портале Azure.
    
    Б. Разверните узел **RoleDescriptor**, содержащий **xsi:type** со значением **fed:ApplicationServiceType**.
    
    c. Скопируйте значение узла **X509Certificate** .
    
    d. Сохраните скопированное значение узла **X509Certificate** локально в файл на компьютере.

13. В разделе **Public** (Общедоступные) щелкните **Add** (Добавить).
    
    ![Вход SAML](./media/active-directory-saas-topdesk-public-tutorial/ic790625.png "Вход SAML")

14. На странице диалогового окна **Помощник настройки SAML** сделайте следующее:
    
    ![Помощник по настройке SAML](./media/active-directory-saas-topdesk-public-tutorial/ic790608.png "Помощник по настройке SAML")
    
    a. Чтобы отправить скачанный файл метаданных через портал Azure, напротив пункта **Federation Metadata** (Метаданные федерации) нажмите кнопку **Browse** (Обзор).

    Б. Чтобы отправить файл сертификата, напротив пункта **Certificate (RSA)** (Сертификат (RSA)) нажмите кнопку **Browse** (Обзор).

    c. Чтобы отправить файл с логотипом, полученный от службы поддержки TOPdesk, напротив пункта **Logo icon** (Значок логотипа) нажмите кнопку **Browse** (Обзор).

    d. В текстовое поле **Атрибут имени пользователя** введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    д. В текстовом поле **Отображаемое имя** введите имя конфигурации.

    f. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-topdesk---public-test-user"></a>Создание тестового пользователя TOPdesk - Public

Чтобы пользователи Azure AD могли выполнять вход в систему TOPdesk — Public, они должны быть подготовлены для нее.  
В случае TOPdesk — Public подготовка пользователей осуществляется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт **TOPdesk — Public** своей компании в качестве администратора.

2. В меню в верхней части страницы щелкните **TOPdesk \> New \> Support Files \> Person** ("TOPdesk" > "Создать" > "Файлы поддержки" > "Пользователь").
   
    ![Пользователь](./media/active-directory-saas-topdesk-public-tutorial/ic790628.png "Пользователь")

3. В диалоговом окне "Новый пользователь" выполните следующие действия:
   
    ![Новый пользователь](./media/active-directory-saas-topdesk-public-tutorial/ic790629.png "Новый пользователь")
   
    a. Перейдите на вкладку "Общее".

    Б. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.
 
    c. Выберите **Веб-сайт** для этой учетной записи.
 
    d. Выберите команду **Сохранить**.

> [!NOTE]
> Для подготовки учетных записей Azure AD вы можете использовать любые другие инструменты или API-интерфейсы создания пользователей, предоставляемые TOPdesk — Public.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TOPdesk - Public.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению TOPdesk - Public, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **TOPdesk - Public**.

    ![Ссылка на TOPdesk - Public в списке "Приложения"](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент TOPdesk - Public на панели доступа, вы автоматически войдете в приложение TOPdesk - Public.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_203.png

