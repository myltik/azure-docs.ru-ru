---
title: Руководство по интеграции Azure Active Directory с Pega Systems | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 539de49f24b2ca0c9b70be5a339625c1e14edc44
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350558"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Руководство по интеграции Azure Active Directory с Pega Systems

В этом руководстве объясняется, как интегрировать приложение Pega Systems с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Pega Systems обеспечивает следующие преимущества:

- C помощью Azure AD вы можете контролировать доступ к Pega Systems.
- Вы можете включить автоматический вход пользователей в Pega Systems (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Pega Systems, вам потребуется:

- подписка Azure AD;
- подписка Pega Systems с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Pega Systems из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-pega-systems-from-the-gallery"></a>Добавление Pega Systems из коллекции.
Чтобы настроить интеграцию Pega Systems с Azure AD, необходимо добавить Pega Systems из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pega Systems из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Pega Systems**, выберите **Pega Systems** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Pega Systems в списке результатов](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Pega Systems с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в Pega Systems соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pega Systems.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pega Systems.

Чтобы настроить и проверить единый вход Azure AD в Pega Systems, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pega Systems](#create-a-pega-systems-test-user)** требуется для того, чтобы в приложении Pega Systems существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе объясняется, как включить единый вход Azure AD на портале Azure и настроить его в приложении Pega Systems.

**Чтобы настроить единый вход Azure AD в Pega Systems, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Pega Systems** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Pega Systems** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    В текстовом поле **Состояние ретранслятора** введите URL-адрес в формате `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим идентификатором, URL-адресом ответа и URL-адресом состояния ретранслятора. Далее в этом руководстве объясняется, как найти значения идентификатора и URL-адреса ответа в приложении Pega Systems. Чтобы получить значение состояния ретрансляции, обратитесь в [службу поддержки клиентов Pega Systems](https://www.pega.com/contact-us). 

5. Приложение Pega Systems ожидает проверочные утверждения SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. Эти утверждения зависят от конкретного клиента и от ваших требований. Указанные ниже необязательные утверждения являются только примерами, которые можно настроить для приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. 

    ![Настройка единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Номер телефона | *********** |

    > [!NOTE]
    > Эти значения зависят от конкретного клиента. Укажите свои собственные значения.

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

7. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Чтобы настроить единый вход на стороне **Pega Systems**, откройте в отдельном окне браузера **портал Pega** и войдите с помощью учетной записи администратора.

10. Выберите **Create** -> **SysAdmin** -> **Authentication Service** (Создать > Системный администратор > Служба аутентификации).

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. В окне **Create Authentication Service** (Создание службы аутентификации) выполните следующие действия:

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. В поле Type (Тип) укажите **SAML 2.0**.

    Б. В текстовом поле **Name** (Имя) введите любое имя, например Azure AD SSO.

    c. В текстовом поле **Short Description** (Краткое описание) введите любое описание.  

    d. Нажмите кнопку **Create and open** (Создать и открыть). 
    
12. В разделе **Identity Provider (IdP) information** (Сведения о поставщике удостоверений) выберите **Import IdP metadata** (Метаданные поставщика удостоверений импорта) и найдите файл метаданных, который вы скачали с портала Azure. Нажмите кнопку **Submit** (Отправить), чтобы загрузить метаданные.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. После этого данные поставщика удостоверений будут заполнены, как показано ниже.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. В разделе **Service Provider (SP) settings** (Параметры поставщика услуг) выполните следующие действия:

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Скопируйте значение **Entity Identification** (Идентификатор сущности) и вставьте его в текстовое поле **Идентификатор** на портале Azure.

    Б.  Скопируйте значение **Assertion Consumer Service (ACS) location** (Расположение службы обработчика утверждений (ACS)) и вставьте его в текстовое поле **URL-адрес ответа** на портале Azure.

    c. Выберите **Disable request signing** (Отключить подписывание запросов).

15. Нажмите кнопку **Сохранить**
    
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-pega-systems-test-user"></a>Создание тестового пользователя Pega Systems

В этом разделе вы создадите пользователя с именем Britta Simon в Pega Systems. Чтобы создать пользователей в Pega Systems, обратитесь в [службу поддержки клиентов Pega Systems](https://www.pega.com/contact-us).


### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе объясняется, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Pega Systems.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении Pega Systems, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pega Systems**.

    ![Ссылка на Pega Systems в списке приложений](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Pega Systems на панели доступа, вы автоматически войдете в приложение Pega Systems.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

