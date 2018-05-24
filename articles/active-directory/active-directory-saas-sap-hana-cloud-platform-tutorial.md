---
title: Руководство по интеграции Azure Active Directory с SAP Cloud Platform | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 4d39c8437cbed497e2a2a7e64caa05f8e3d04869
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Руководство по интеграции Azure Active Directory с SAP Cloud Platform

В этом руководстве описано, как интегрировать SAP Cloud Platform с Azure Active Directory (Azure AD).

Интеграция Azure AD с SAP Cloud Platform обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к SAP Cloud Platform.
- Вы можете включить автоматический вход пользователей в SAP Cloud Platform (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SAP Cloud Platform, вам потребуется:

- подписка Azure AD;
- подписка SAP Cloud Platform с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные SAP Cloud Platform, смогут выполнять единый вход в приложение следуя указаниям в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Для тестирования единого входа необходимо развернуть собственное приложение или оформить подписку на приложение в учетной записи SAP Cloud Platform. В этом учебнике используется развертывание приложения в учетной записи.
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAP Cloud Platform из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Добавление SAP Cloud Platform из коллекции
Чтобы настроить интеграцию SAP Cloud Platform с Azure AD, необходимо добавить SAP Cloud Platform из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud Platform из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SAP Cloud Platform**, выберите **SAP Cloud Platform** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SAP Cloud Platform в списке результатов](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Cloud Platform с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в SAP Cloud Platform соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в SAP Cloud Platform.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP Cloud Platform.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud Platform, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)** нужно для того, чтобы в SAP Cloud Platform также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SAP Cloud Platform.

**Чтобы настроить единый вход Azure AD в SAP Cloud Platform, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SAP Cloud Platform** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SAP Cloud Platform** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. В текстовом поле **URL-адрес входа** введите URL-адрес, который пользователи используют для входа в приложение **Sign On URL**. Это URL-адрес защищенного ресурса для конкретной учетной записи в приложении SAP Cloud Platform. URL-адрес имеет следующий формат: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`.
      
     >[!NOTE]
     >Это URL-адрес в приложении SAP Cloud Platform, запрашивающий аутентификацию пользователя.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    Б. В текстовом поле **Идентификатор** укажите URL-адрес SAP Cloud Platform в одном из следующих форматов: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить URL-адрес входа и идентификатор, обратитесь к [службе поддержки клиентов SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html). URL-адрес ответа можно получить в разделе управления доверием, который рассматривается далее в этом руководстве.
    > 
     
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. В другом окне браузера войдите на панель SAP Cloud Platform по адресу `https://account.<landscape host>.ondemand.com/cockpit` (например, https://account.hanatrial.ondemand.com/cockpit)).

7. Щелкните вкладку **Доверие** .
   
    ![Доверие](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "Доверие")

8. В разделе управления доверием на вкладке **Local Service Provider** (Поставщик локальных служб) выполните следующие действия:

    ![Управление доверием](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "Управление доверием")
   
    a. Нажмите кнопку **Изменить**.

    Б. Для параметра **Configuration Type** (Тип конфигурации) выберите значение **Custom** (Настраиваемая).

    c. В поле **Имя поставщика локальных служб**оставьте значение по умолчанию. Скопируйте это значение и вставьте его в поле **Идентификатор** в конфигурации Azure AD для SAP Cloud Platform.

    d. Чтобы создать пару ключей **Signing Key** (Ключ подписывания) и **Signing Certificate** (Сертификат подписывания), щелкните **Generate Key Pair** (Создать пару ключей).

    д. Для параметра **Principal Propagation** (Распространение субъектов) выберите значение **Disabled** (Отключено).

    f. Для параметра **Force Authentication** (Принудительная аутентификация) выберите значение **Disabled** (Отключено).

    ж. Выберите команду **Сохранить**.

9. После сохранения параметров на вкладке **Local Service Provider** (Поставщик локальных служб) выполните следующие действия, чтобы получить URL-адрес ответа:
   
    ![Получение метаданных](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "Получение метаданных")

    a. Скачайте файл метаданных SAP Cloud Platform, щелкнув ссылку **Get Metadata** (Получить метаданные).

    Б. Откройте скачанный XML-файл метаданных SAP Cloud Platform, а затем найдите тег **ns3:AssertionConsumerService**.
 
    c. Скопируйте значение атрибута **Location** и вставьте его в текстовое поле **URL-адрес ответа** в конфигурации Azure AD для SAP Cloud Platform.

10. Откройте вкладку **Trusted Identity Provider** (Доверенный поставщик удостоверений) и щелкните **Add Trusted Identity Provider** (Добавить доверенный поставщик удостоверений).
   
    ![Управление доверием](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "Управление доверием")
   
    >[!NOTE]
    >Для управления списком доверенных поставщиков удостоверений необходимо выбрать тип конфигурации «Настраиваемая» в разделе «Поставщик локальных служб». В качестве типа конфигурации по умолчанию используется неизменяемое неявное доверие к службе SAP ID Service. Для значения «Нет» параметры доверия отсутствуют.
    > 
    > 

11. Откройте вкладку **General** (Общие) и нажмите кнопку **Browse** (Обзор), чтобы передать скачанный файл метаданных.
    
    ![Управление доверием](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "Управление доверием")
    
    >[!NOTE]
    >После передачи файла метаданных поля **Single Sign-on URL** (URL-адрес единого входа), **Single Logout URL** (URL-адрес единого выхода) и **Signing Certificate** (Сертификат для подписи) заполняются автоматически.
    > 
     
12. Перейдите на вкладку **Атрибуты** .

13. На вкладке **Атрибуты** сделайте следующее:
    
    ![Атрибуты](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "Атрибуты") 

    a. Щелкните **Add Assertion-Based Attribute** (Добавить атрибут на основе утверждений) и добавьте следующие атрибуты на основе утверждений:
       
    | Атрибут утверждения | Атрибут субъекта |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >Конфигурация атрибутов зависит от способа разработки приложений в SCP, т. е. какие атрибуты предполагается использовать в ответе SAML и какое имя (атрибут субъекта) используется для доступа к этому атрибуту в коде.
     > 
    
    Б. **Атрибут по умолчанию** на снимке экрана предоставляется только для примера. Он не требуется для того, чтобы сценарий работал.  
 
    c. Имена и значения для **атрибута субъекта** , показанные на снимке экрана, зависят от способа разработки приложения. Возможно, приложению требуются другие сопоставления.

### <a name="assertion-based-groups"></a>Группы на основе утверждений

При необходимости для поставщика удостоверений Azure Active Directory можно настроить группы на основе утверждений.

Использование групп на SAP Cloud Platform позволяет динамически назначать одного или нескольких пользователей одной или нескольким ролям в приложениях SAP Cloud Platform, которые определяются по значениям атрибутов в утверждении SAML 2.0. 

Например, если утверждение содержит атрибут *contract=temporary*, возможно, вам потребуется добавить всех затронутых пользователей в группу *TEMPORARY*. Группа *TEMPORARY* может содержать одну или несколько ролей из одного или нескольких приложений, развернутых в учетной записи SAP Cloud Platform.
 
Используйте группы на основе утверждений, чтобы одновременно назначить большое число пользователей для одной или нескольких ролей приложений в учетной записи SAP Cloud Platform. Если требуется назначить одного или небольшое число пользователей конкретным ролям, мы советуем назначить их непосредственно на вкладке **Authorizations** (Разрешения) панели SAP Cloud Platform.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Создание тестового пользователя SAP Cloud Platform

Чтобы разрешить пользователям Azure AD входить в SAP Cloud Platform, им необходимо назначить роли в SAP Cloud Platform.

**Чтобы назначить роль пользователю, сделайте следующее:**

1. Войдите на панель **SAP Cloud Platform**.

2. Выполните следующие действия.
   
    ![Авторизации](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "Авторизации")
   
    a. Щелкните **Авторизация**.

    Б. Откройте вкладку **Пользователи** .

    c. В тестовом поле **Пользователь** введите электронный адрес пользователя.

    d. Щелкните **Назначить** , чтобы назначить роль пользователю.

    д. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SAP Cloud Platform.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SAP Cloud Platform, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Cloud Platform**.

    ![Ссылка на SAP Cloud Platform в списке приложений](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAP Cloud Platform на панели доступа, вы автоматически войдете в приложение SAP Cloud Platform.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

