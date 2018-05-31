---
title: Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication | Документация Майкрософт
description: Сведения о настройке единого входа в Azure Active Directory и SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: e7cc1df5e550dec62869c2a6f68cdc2a84167142
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352418"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication

В этом руководстве описано, как интегрировать приложение SAP Cloud Platform Identity Authentication с Azure Active Directory (AAD). Приложение SAP Cloud Platform Identity Authentication используется в качестве "промежуточного" поставщика удостоверений для доступа к приложениям SAP, основным поставщиком удостоверений для которых является Azure AD.

Настроив интеграцию приложения SAP Cloud Platform Identity Authentication с AAD, вы получите следующие преимущества.

- С помощью AAD вы можете контролировать доступ к приложениям SAP.
- Вы можете включить автоматический вход пользователей в приложения SAP (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию AAD с приложением SAP Cloud Platform Identity Authentication, вам потребуются:

- подписка Azure AD;
- подписка на SAP Cloud Platform Identity Authentication с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление приложения SAP Cloud Platform Identity Authentication из коллекции
2. настройка и проверка единого входа в Azure AD.

Прежде чем углубиться в технические подробности, очень важно разобраться в основных понятиях этого процесса. Службы федерации SAP Cloud Platform Identity Authentication и AD позволяют реализовать единый вход в приложения и службы, защищенные с помощью Azure AD (выступающей в качестве поставщика удостоверений), и приложения и службы SAP, защищенные с помощью SAP Cloud Platform Identity Authentication.

Приложение SAP Cloud Platform Identity Authentication выполняет роль "промежуточного" поставщика удостоверений для приложений SAP. В свою очередь, Azure Active Directory выступает ведущим поставщиком удостоверений. 

Этот пример представлен на схеме ниже.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

В этой конфигурации клиент SAP Cloud Platform Identity Authentication настроен как доверенное приложение в AAD. 

Все приложения и службы SAP, которые вы намерены защитить, настраиваются в консоли управления SAP Cloud Platform Identity Authentication.

Это значит, что авторизация для доступа к этим приложениям и службам SAP будет выполняться в приложении SAP Cloud Platform Identity Authentication, а не в Azure Active Directory.

Настройка SAP Cloud Platform Identity Authentication в роли приложения в Azure Active Directory Marketplace избавляет от необходимости настраивать отдельные утверждения или утверждения SAML.

>[!NOTE] 
>В настоящее время обе стороны проверили только единый вход через веб-интерфейс. Потоки взаимодействия "приложение — API" и "API — API" готовы и работают, но пока не тестировались. Выполнение таких тестов входит в программу дальнейших действий.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Добавление приложения SAP Cloud Platform Identity Authentication из коллекции
Чтобы настроить интеграцию SAP Cloud Platform Identity Authentication с AAD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud Platform Identity Authentication из коллекции, выполните следующие действия.**

1. На [портале Azure](https://portal.azure.com) щелкните значок **Azure Active Directory** в области навигации слева. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SAP Cloud Platform Identity Authentication**. 

5. Выберите **SAP Cloud Platform Identity Authentication** на панели результатов поиска, а затем нажмите кнопку **Добавить**.

    ![Приложения SAP Cloud Platform Identity Authentication в списке результатов](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа AAD в SAP Cloud Platform Identity Authentication. Настройка и проверка выполняются для тестового пользователя с именем Britta Simon.

Чтобы настроить единый вход, в AAD должны быть сведения о том, какой пользователь в SAP Cloud Platform Identity Authentication соответствует пользователю в AAD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud Platform Identity Authentication.

В SAP Cloud Platform Identity Authentication присвойте параметру **Username** (Имя пользователя) то же значение, которое указано в качестве **имени пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями.

Чтобы настроить и проверить единый вход AAD в SAP Cloud Platform Identity Authentication, выполните действия в следующих блоках:

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user) требуется, чтобы создать в SAP Cloud Platform Identity Authentication пользователя Britta Simon, связанного представлением этого пользователя в AAD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить на портале Azure единый вход AAD и настроить его в приложении SAP Cloud Platform Identity Authentication.

**Чтобы настроить единый вход AAD в приложении SAP Cloud Platform Identity Authentication, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SAP Cloud Platform Identity Authentication** выберите пункт **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Вход на основе SAML** выберите **Режим**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Если нужно настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication** выполните следующие действия.  

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    a. В поле **Идентификатор** введите URL-адрес в следующем формате: `<IAS-tenant-id>.accounts.ondemand.com`.

    Б. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), чтобы узнать эти значения. Если вы не знаете, что такое значение идентификатора, изучите документацию по [настройке клиента SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) для SAP Cloud Platform Identity Authentication.

4. Если вы хотите настроить в приложении режим, инициируемый **поставщиком услуг**, установите флажок **Показать дополнительные параметры URL-адресов**.

    ![Сведения для единого входа в разделе "Домен и URL-адреса приложения SAP Cloud Platform Identity Authentication"](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Используйте свой URL-адрес входа для бизнес-приложения. Если у вас возникнут сомнения, обратитесь в [службу поддержки SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

5. В разделе **Сертификат подписи SAML** выберите **XML метаданных**. Затем сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. Приложение SAP Cloud Platform Identity Authentication поддерживает утверждения SAML в определенном формате. Значения этих атрибутов можно изменить в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример формата утверждений. 

    ![Настройка единого входа](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Если ваше приложение SAP ожидает определенный атрибут, например **firstName**, добавьте имя атрибута **firstName** в раздел **Атрибуты пользователя**. Этот раздел находится в диалоговом окне **Единый вход**, которое открывается из окна **Атрибуты токена SAML**.

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**. 
    
    ![Настройка единого входа](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута **firstName**.
    
    c. Из списка **Значение** выберите значение атрибута **user.givenname**.
    
    d. Нажмите кнопку **ОК**.

8. Нажмите кнопку **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. В разделе **Настройка SAP Cloud Platform Identity Authentication** выберите **Настроить SAP Cloud Platform Identity Authentication**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Чтобы настроить единый вход в приложении, откройте консоль администрирования SAP Cloud Platform Identity Authentication. URL-адрес имеет следующий формат: `https://<tenant-id>.accounts.ondemand.com/admin`. Ознакомьтесь с документацией по SAP Cloud Platform Identity Authentication на странице [интеграции с Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. На портале Azure нажмите кнопку **Сохранить**.

12. Следующие действия нужно выполнять только в том случае, если вы хотите добавить и включить единый вход для другого приложения SAP. Повторите весь процесс, описанный в разделе **Добавление приложения SAP Cloud Platform Identity Authentication из коллекции**.

13. На портале Azure на странице интеграции приложения **SAP Cloud Platform Identity Authentication** выберите пункт **Вход по ссылке**.

    ![Настройка связанного единого входа](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Сохраните конфигурацию.

>[!NOTE] 
>Новое приложение применит ту же конфигурацию единого входа, которая настроена для первого приложения SAP. Убедитесь, что в консоли администрирования SAP Cloud Platform Identity Authentication указаны те же поставщики корпоративных удостоверений.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После того, как вы добавите приложение из раздела **Active Directory** > **Корпоративные приложения**, выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Создание тестового пользователя SAP Cloud Platform Identity Authentication

В приложении SAP Cloud Platform Identity Authentication не нужно создавать пользователя. Пользователи, находящиеся в хранилище пользователей в Azure AD, могут использовать функцию единого входа.

Приложение SAP Cloud Platform Identity Authentication поддерживает федерацию удостоверений. Это позволяет приложению проверять, существуют ли пользователи, прошедшие аутентификацию через корпоративного поставщика удостоверений, в хранилище пользователей SAP Cloud Platform Identity Authentication. 

Параметр федерации удостоверений по умолчанию отключен. Если вы включите этот параметр, доступ к приложению смогут получить только те пользователи, которые заранее импортированы в SAP Cloud Platform Identity Authentication. 

Дополнительные сведения о том, как включить или отключить параметр федерации удостоверений для SAP Cloud Platform Identity Authentication см. в соответствующем разделе статьи о [настройке федерации удостоверений с использованием хранилища пользователей SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Cloud Platform Identity Authentication.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SAP Cloud Platform Identity Authentication, выполните следующие действия.**

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Затем перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Cloud Platform Identity Authentication**.

    ![Ссылка на SAP Cloud Platform Identity Authentication в списке приложений](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**.

6. Нажмите кнопку **Выбрать** в диалоговом окне **Пользователи и группы**.

7. Нажмите кнопку **Назначить** в диалоговом окне **Добавление назначения**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Выбрав плитку "SAP Cloud Platform Identity Authentication" на панели доступа, вы автоматически войдете в это приложение.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

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
