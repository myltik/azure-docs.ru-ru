---
title: "Руководство по интеграции Azure Active Directory с Amazon Web Services (AWS) | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Amazon Web Services (AWS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Руководство по интеграции Azure Active Directory с Amazon Web Services

В этом руководстве описано, как интегрировать приложение Amazon Web Services (AWS) с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Amazon Web Services.
- Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

- подписка Azure AD;
- Подписка с поддержкой единого входа в Amazon Web Services (AWS)

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Amazon Web Services из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции
Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amazon Web Services (AWS) из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Amazon Web Services (AWS)**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. На панели результатов выберите **Amazon Web Services (AWS)** и щелкните **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Amazon Web Services.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Amazon Web Services.

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Amazon Web Services](#creating-an-amazon-web-services-test-user)** требуется для создания соответствующего пользователя Britta Simon в Amazon Web Services, связанного с ее представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amazon Web Services (AWS).

**Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Amazon Web Services (AWS)** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Amazon Web Services (AWS)** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.
    
    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута  | Значение атрибута | Пространство имен |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.Amazon.com/SAML/Attributes |
    | Роль            | user.assignedroles |  https://aws.Amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Необходимо настроить подготовку пользователя в Azure AD для получения всех ролей из консоли AWS. Этапы подготовки см. ниже.

    а. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки. Добавьте значение пространства имен, как показано выше.
    
    г) Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить**, чтобы сохранить параметры в Azure.

    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.

9. Щелкните **Console Home**(Домашняя страница консоли).
   
    ![Настройка единого входа][11]

10. Щелкните **IAM** в службе **Security, Identity & Compliance** (Безопасность, удостоверения и соответствие требованиям).
   
    ![Настройка единого входа][12]

11. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик).
   
    ![Настройка единого входа][13]

12. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия.
   
    ![Настройка единого входа][14]
 
    а. Выберите для параметра **Тип поставщика** значение **SAML**.

    b. В текстовом поле **Имя поставщика** введите имя поставщика (например, *WAAD*).

    В. Чтобы отправить загруженный файл метаданных, нажмите кнопку **Выбрать файл**.

    d. Щелкните **Следующий шаг**.

13. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать). 
    
    ![Настройка единого входа][15]

14. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create New Role** (Создать новую роль). 
    
    ![Настройка единого входа][16]

15. В диалоговом окне **Set Role Name** (Настройка имени роли) выполните следующие действия. 
    
    ![Настройка единого входа][17] 

    а. В текстовом поле **Имя роли** введите имя роли (например, *TestUser*). 

    b. Щелкните **Следующий шаг**.

16. В диалоговом окне **Select Role Type** (Выбор типа роли) выполните следующие действия. 
    
    ![Настройка единого входа][18] 

    а. Выберите **Роль для доступа поставщика удостоверений**. 

    b. В разделе **Grant Web Single Sign-On (WebSSO) access to SAML providers** (Предоставление единого входа через Интернет (WebSSO) для доступа к поставщикам SAML) щелкните **Select** (Выбрать).

17. В диалоговом окне **Establish Trust** (Установка доверия) выполните следующие действия.  
    
    ![Настройка единого входа][19] 

    а. В качестве поставщика SAML выберите поставщик SAML, созданный ранее (например, *WAAD*).
  
    b. Щелкните **Следующий шаг**.

18. В диалоговом окне **Verify Role Trust** (Проверка доверия роли) нажмите кнопку **Next Step** (Следующий шаг).
    
    ![Настройка единого входа][32]

19. В диалоговом окне **Attach Policy** (Присоединение политики) нажмите кнопку **Next Step** (Следующий шаг).
    
    ![Настройка единого входа][33]

20. В диалоговом окне **Review** (Обзор) выполните следующие действия.
    
    ![Настройка единого входа][34]
 
    а. Щелкните **Создать роль**.

    b. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

21. Теперь настройте подготовку пользователя для получения всех ролей из AWS.

    а. В консоли AWS войдите в систему с использованием учетной записи root.

    b. В правом верхнем углу щелкните свое имя и выберите параметр **My Security Credentials** (Мои учетные данные для безопасного доступа). Откроется экран с предупреждением. Нажмите кнопку **Security Credentials** (Учетные данные для безопасного доступа), чтобы закрыть предупреждение.
        
       ![Настройка единого входа][36]

       ![Настройка единого входа][37]

    c. В разделе "Ключи доступа" щелкните **Create New Access Key** (Создать ключ доступа). При этом будет создан идентификатор ключа доступа и значение токена.
    
       ![Настройка единого входа][38]

    d. Скопируйте оба эти значения, а также скачайте их, чтобы не потерять.

    д. На портале Azure на странице интеграции с приложением Amazon Web Services (AWS) щелкните **Подготовка**.
        
       ![Настройка единого входа][35]

    f. Для параметра "Режим подготовки к работе" выберите значение **Автоматически**.
        
       ![Настройка единого входа][39]

    g. Теперь в поле **Секрет клиента** и **Секретный токен** вставьте соответствующие значения, скопированные из консоли AWS ранее.
    
    h. Вы можете нажать кнопку **Проверить подключение**, чтобы проверить возможность подключения. Если все подключено, можно запустить соединитель подготовки.
       
       ![Настройка единого входа][40]

    i. Теперь в разделе "Состояние подготовки" нажмите кнопку **Вкл.** При этом запустится выборка ролей из приложения.

       ![Настройка единого входа][41]

    > [!NOTE]
    > Через некоторое время запустится служба подготовки Azure AD для синхронизации ролей из AWS. В Azure AD должны отображаться все роли AWS, подключенные к поставщику удостоверений. Их можно использовать при назначении приложения пользователям или группам.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-an-amazon-web-services-test-user"></a>Создание тестового пользователя Amazon Web Services

Чтобы пользователи Azure AD могли войти в службу Amazon Web Services (AWS), их необходимо подготовить в ней. В случае с Amazon Web Services (AWS) подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на сайт компании **Amazon Web Services** в качестве администратора.

2. Щелкните значок **домашней страницы консоли** . 
   
    ![Настройка единого входа][11]

3. Щелкните Identity and Access Management (Управление удостоверениями и доступом). 
   
    ![Настройка единого входа][28]

4. На панели мониторинга щелкните **Пользователи**, а затем — **Create New Users** (Создать пользователей). 
   
    ![Настройка единого входа][29]

5. В диалоговом окне Create User (Создание пользователя) выполните следующие действия. 
   
    ![Настройка единого входа][30]   
    
    а. В текстовых полях **Введите имена пользователей** введите имя пользователя (userprincipalname) в Azure AD.

    b. Щелкните **Создать**.
        
### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Amazon Web Services (AWS), чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Amazon Web Services (AWS), сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Amazon Web Services**.

    ![Настройка единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. На вкладке **Выбор роли** выберите соответствующую роль для пользователя. Все эти роли показаны со своими именами и именами поставщика удостоверений. Таким образом, можно легко определить роли из AWS.

8. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services на панели доступа, вы автоматически войдете в приложение Amazon Web Services. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
