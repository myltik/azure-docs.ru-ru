---
title: "Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication | Документация Майкрософт"
description: "Сведения о настройке единого входа в Azure Active Directory и SAP Cloud Platform Identity Authentication."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication

В этом руководстве описано, как интегрировать приложение SAP Cloud Platform Identity Authentication с Azure Active Directory (AAD). Приложение SAP Cloud Platform Identity Authentication используется в качестве поставщика удостоверений прокси-сервера для доступа к приложениям SAP с использованием AAD в качестве главного поставщика удостоверений.

Интеграция приложения SAP Cloud Platform Identity Authentication с AAD обеспечивает следующие преимущества:

- с помощью AAD вы можете контролировать доступ к приложению SAP;
- вы можете включить автоматический вход пользователей в приложения SAP (единый вход) с использованием учетных записей AAD;
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию AAD с приложением SAP Cloud Platform Identity Authentication, вам потребуются:

- подписка Azure AD;
- подписка на SAP Cloud Platform Identity Authentication с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление приложения SAP Cloud Platform Identity Authentication из коллекции
2. Настройка и проверка единого входа в Azure AD

Прежде чем углубиться в технические сведения, очень важно разобраться в основных понятиях, которые здесь будут представлены. Федерация SAP Cloud Platform Identity Authentication и AAD позволяет реализовать единый вход в приложения и службы, защищенные с помощью AAD (выступающей в качестве поставщика удостоверений), и приложения и службы SAP, защищенные с помощью SAP Cloud Platform Identity Authentication.

Сейчас приложение SAP Cloud Platform Identity Authentication — поставщик удостоверений прокси-сервера для приложений SAP. В свою очередь, Azure Active Directory выступает ведущим поставщиком удостоверений. 

Этот пример представлен на схеме ниже.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

При такой конфигурации клиент SAP Cloud Platform Identity Authentication будет настроен как доверенное приложение в AAD. 

Все приложения и службы SAP, которые необходимо защитить таким образом, затем настраиваются в консоли управления SAP Cloud Platform Identity Authentication.

Это значит, что авторизация для доступа к приложениям и службам SAP для этой установки должна выполняться в приложении SAP Cloud Platform Identity Authentication (в отличие от настройки авторизации в AAD).

После настойки SAP Cloud Platform Identity Authentication как приложения в магазине Azure Active Directory вам не нужно будет настраивать отдельные утверждения или утверждения SAML и преобразования, необходимые для создания действительного маркера проверки подлинности для приложений SAP.

>[!NOTE] 
>В настоящее время единый вход для интернет-решений был проверен только обеими сторонами. Потоки, необходимые для связи "приложение — API" и "API — API", работают, но они еще не проверялись. Они будут проверены в ходе последующих действий.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Добавление приложения SAP Cloud Platform Identity Authentication из коллекции
Чтобы настроить интеграцию SAP Cloud Platform Identity Authentication с AAD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud Platform Identity Authentication из коллекции:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SAP Cloud Platform Identity Authentication**, выберите **SAP Cloud Platform Identity Authentication** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Приложения SAP Cloud Platform Identity Authentication в списке результатов](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа AAD в SAP Cloud Platform Identity Authentication с использованием данных тестового пользователя Britta Simon.

Чтобы настроить единый вход, в AAD должны быть сведения о том, какой пользователь в SAP Cloud Platform Identity Authentication соответствует пользователю в AAD. Иными словами, необходимо установить связь между пользователем AAD и соответствующим пользователем в SAP Cloud Platform Identity Authentication.

Чтобы установить эту связь, назначьте **Имя пользователя** в AAD как **Username** ("Имя пользователя") в SAP Cloud Platform Identity Authentication.

Чтобы настроить и проверить единый вход AAD в SAP Cloud Platform Identity Authentication, вам потребуется:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user)** требуется, чтобы создать пользователя Britta Simon в SAP Cloud Platform Identity Authentication, связанного с этим же пользователем в AAD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить на портале Azure единый вход AAD и настроить его в приложении SAP Cloud Platform Identity Authentication.

**Чтобы настроить единый вход AAD в приложении SAP Cloud Platform Identity Authentication:**

1. На портале Azure на странице интеграции приложения **SAP Cloud Platform Identity Authentication** выберите пункт **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Если нужно настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication** сделайте следующее.

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него нужно указать фактический идентификатор. Обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), чтобы узнать это значение. Если вы не знаете это значение, следуйте инструкциям в документации SAP Cloud Platform Identity Authentication по [настройке клиента SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<entity-id>.accounts.ondemand.com/admin`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), чтобы узнать это значение.

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. Приложение SAP Cloud Platform Identity Authentication поддерживает утверждения SAML в определенном формате. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример. 

    ![Настройка единого входа](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. В диалоговом окне **Единый вход** в разделе **Атрибуты пользователя** укажите, ожидает ли приложение SAP атрибут, например firstName. В диалоговом окне "Атрибуты токена SAML" добавьте атрибут firstName.

    а. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.
    
    ![Настройка единого входа](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. В текстовом поле **Имя** введите firstName.
    
    c. В списке **Значение** выберите user.givenname.
    
    d. Нажмите кнопку **ОК**.

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. В разделе **Настройка SAP Cloud Platform Identity Authentication** щелкните **Настроить SAP Cloud Platform Identity Authentication**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Чтобы настроить единый вход для приложения, перейдите в консоль администрирования SAP Cloud Platform Identity Authentication. URL-адрес имеет следующий формат: `https://<tenant-id>.accounts.ondemand.com/admin`. Следуйте инструкциям в документации SAP Cloud Platform Identity Authentication, чтобы [настроить Microsoft Azure AD в качестве корпоративного поставщика удостоверений в SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. На портале Azure нажмите кнопку **Сохранить**.

12. Продолжайте выполнять описанные действия, только если вы хотите добавить и включить единый вход для другого приложения SAP. Повторите шаги, описанные в разделе "Добавление приложения SAP Cloud Platform Identity Authentication из коллекции", чтобы добавить другой экземпляр.

13. На портале Azure на странице интеграции приложения **SAP Cloud Platform Identity Authentication** выберите пункт **Вход по ссылке**.

    ![Настройка связанного единого входа](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Сохраните конфигурацию.

>[!NOTE] 
>Новое приложение будет использовать конфигурацию единого входа для предыдущих приложений SAP. Убедитесь, что в консоли администрирования SAP Cloud Platform Identity Authentication используются те же поставщики корпоративных удостоверений.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    а. В поле **Имя** введите **BrittaSimon**.

    b. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    г) Щелкните **Создать**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Создание тестового пользователя SAP Cloud Platform Identity Authentication

В приложении SAP Cloud Platform Identity Authentication не нужно создавать пользователя. Пользователи, находящиеся в хранилище пользователей в Azure AD, могут использовать функцию единого входа.

Приложение SAP Cloud Platform Identity Authentication поддерживает федерацию удостоверений. Это позволяет приложению проверять, есть ли пользователи, прошедшие проверку с помощью корпоративного поставщика удостоверений, в хранилище пользователей в приложении SAP Cloud Platform Identity Authentication. 

По умолчанию параметр федерации удостоверений отключен. Если он включен, только у пользователей, импортированных в SAP Cloud Platform Identity Authentication, есть доступ к приложению. 

Дополнительные сведения о том, как включить или отключить параметр федерации удостоверений с помощью SAP Cloud Platform Identity Authentication см. в разделе статьи [Configure Identity Federation with the User Store of SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Настройка федерации удостоверений с использованием хранилища пользователей в приложении SAP Cloud Platform Identity Authentication).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Cloud Platform Identity Authentication.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SAP HANA Cloud Platform Identity Authentication:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Cloud Platform Identity Authentication**.

    ![Ссылка на SAP Cloud Platform Identity Authentication в списке приложений](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "SAP Cloud Platform Identity Authentication" на панели доступа, вы автоматически войдете в это приложение.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

