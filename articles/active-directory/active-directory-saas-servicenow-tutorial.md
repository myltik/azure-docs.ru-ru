---
title: "Руководство по интеграции Azure Active Directory с ServiceNow | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в ServiceNow или ServiceNow Express."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Руководство: интеграция Azure Active Directory с ServiceNow
В этом руководстве описано, как интегрировать ServiceNow и ServiceNow Express с Azure Active Directory (Azure AD).

Интеграция Azure AD с ServiceNow и ServiceNow Express обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к ServiceNow и ServiceNow Express.
* Вы можете включить автоматический вход пользователей в ServiceNow и ServiceNow Express (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с ServiceNow и ServiceNow Express, вам потребуется следующее.

* подписка Azure AD;
* Экземпляр или клиент ServiceNow версии Calgary или выше (для ServiceNow).
* Экземпляр ServiceNow Express версии Helsinki или выше (для ServiceNow Express).
* В клиенте ServiceNow должен быть включен [подключаемый модуль единого входа для нескольких поставщиков](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0). Это можно сделать путем [отправки запроса на обслуживание](https://hi.service-now.com). 

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление ServiceNow из коллекции.
2. Настройка и проверка единого входа Azure AD в ServiceNow или ServiceNow Express.

## <a name="adding-servicenow-from-the-gallery"></a>Добавление ServiceNow из коллекции
Чтобы настроить интеграцию ServiceNow или ServiceNow Express с Azure AD, необходимо добавить ServiceNow из коллекции в список управляемых приложений SaaS. 

**Чтобы добавить ServiceNow из коллекции, сделайте следующее:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **ServiceNow**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. В области результатов выберите **ServiceNow** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в ServiceNow или ServiceNow Express с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в ServiceNow соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ServiceNow.
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в ServiceNow. Чтобы настроить и проверить единый вход Azure AD в ServiceNow, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD в ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Настройка единого входа Azure AD в ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** необходима, чтобы позволить пользователям использовать эту функцию.
3. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя ServiceNow](#creating-a-servicenow-test-user)** требуется для создания пользователя Britta Simon в ServiceNow, связанного с соответствующим пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
6. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

> [!NOTE]
> Чтобы настроить ServiceNow, пропустите шаг 2, а чтобы настроить ServiceNow Express — шаг 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Настройка единого входа Azure AD в ServiceNow
1. На классическом портале Azure AD на странице интеграции с приложением **ServiceNow** нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Настройка единого входа")

2. На странице **Как пользователи должны входить в ServiceNow?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Настройка единого входа")

3. На странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Настройка URL-адреса приложения")
   
    а. В текстовое поле **URL-адрес входа в ServiceNow** введите URL-адрес, используемый пользователями для входа в приложение ServiceNow, в следующем формате `https://<instance-name>.service-now.com`.
   
    b. В текстовое поле **Идентификатор** введите URL-адрес, используемый пользователями для входа в приложение ServiceNow, в следующем формате `https://<instance-name>.service-now.com`.
   
    c. Щелкните **Далее**

4. Чтобы в Azure AD была выполнена автоматическая настройка ServiceNow для аутентификации на основе SAML, введите имя вашего экземпляра ServiceNow, имя пользователя с правами администратора и пароль администратора в форму **Настроить единый вход автоматически** , после чего щелкните *Настроить*. Обратите внимание, что для этого указываемому пользователю с правами администратора должна быть назначена роль **security_admin** в ServiceNow. Вы также можете вручную настроить в ServiceNow использование Azure AD в качестве поставщика удостоверений SAML. Для этого щелкните **Вручную настроить это приложение для единого входа**, затем нажмите кнопку **Далее** и выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Настройка URL-адреса приложения")

5. На странице **Настройка единого входа в ServiceNow** щелкните **Скачать сертификат** и сохраните файл сертификата на локальном компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Настройка единого входа")

6. Войдите в приложение ServiceNow с правами администратора.

7. Активируйте подключаемый модуль *Integration - Multiple Provider Single Sign-On Installer* (Интеграция — установщик единого входа для нескольких поставщиков), выполнив следующие действия.
   
    а. В левой области навигации выберите раздел **Определение системы** и щелкните **Подключаемые модули**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Активация подключаемого модуля")
   
    b. Найдите *Integration - Multiple Provider Single Sign-On Installer* (Интеграция — установщик единого входа для нескольких поставщиков).
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Активация подключаемого модуля")
   
    c. Выберите подключаемый модуль. Щелкните правой кнопкой мыши и выберите **Activate/Upgrade** (Активировать или обновить).
   
    d. Нажмите кнопку **Активировать**.

8. На панели навигации слева щелкните **Properties**(Свойства).  
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Настройка URL-адреса приложения")

9. В диалоговом окне **Multiple Provider SSO Properties** (Свойства единого входа для нескольких поставщиков) выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Настройка URL-адреса приложения")
   
    а. Для параметра **Enable multiple provider SSO** (Включить единый вход для нескольких поставщиков) выберите значение **Yes** (Да).
   
    b. Для параметра **Enable debug logging got the multiple provider SSO integration** (Включить ведение журнала отладки для интеграции нескольких поставщиков единого входа) выберите **Yes** (Да).
   
    c. В текстовом поле **The field on the user table that...** (Поле в пользовательской таблице) введите значение **user_name**.
   
    d. Щелкните **Сохранить**.

10. На панели навигации слева щелкните **x509 Certificates**(Сертификаты x509).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Настройка единого входа")

11. В диалоговом окне **X.509 Certificates** (Сертификаты X.509) нажмите кнопку **New** (Создать).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Настройка единого входа")

12. В диалоговом окне **X.509 Certificates** (Сертификаты X.509) выполните следующие действия.
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Настройка единого входа")
    
     а. Нажмите кнопку **Создать**.
    
     b. В текстовом поле **Name** (Имя) введите имя конфигурации (например, **TestSAML2.0**).
    
     c. Установите флажок **Активно**.
    
     d. В поле **Format** (Формат) выберите **PEM**.
    
     д. В поле **Type** (Тип) выберите **Trust Store Cert** (Сертификат хранилища доверия).
    
     Е. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **PEM Certificate** (Сертификат PEM).
    
     ж. Нажмите кнопку **Update**(Обновить).

13. На панели навигации слева щелкните **Identity Providers**(Поставщики удостоверений).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Настройка единого входа")

14. В диалоговом окне **Identity Providers** (Поставщики удостоверений) нажмите кнопку **New** (Создать).
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Настройка единого входа")

15. В диалоговом окне **Identity Providers** (Поставщики удостоверений) щелкните **SAML2 Update1?**.
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Настройка единого входа")

16. В диалоговом окне SAML2 Update1 Properties (Свойства SAML2 Update1) выполните следующие действия.
    
     ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Настройка единого входа")

    а. В текстовом поле **Name** (Имя) введите имя конфигурации (например, **SAML 2.0**).

    b. В текстовом поле **User Field** (Поле пользователя) введите значение **email** или **user_name**, в зависимости от того, какое поле используется для уникальной идентификации пользователей в развернутой службе ServiceNow. 

    > [!NOTE] 
    > В Azure AD можно настроить, чтобы в качестве уникального идентификатора в токене SAML выдавался идентификатор пользователя Azure AD (имя участника-пользователя) или его электронный адрес. Для этого перейдите в раздел **ServiceNow > Атрибуты > Единый вход** на классическом портале Azure и сопоставьте нужное поле с атрибутом **nameidentifier**. Значение, хранящееся для выбранного атрибута в Azure AD (например, имя участника-пользователя) должно соответствовать значению, хранящемуся в ServiceNow для заполненного поля (например, user_name).

    c. На классическом портале Azure AD скопируйте значение поля **Идентификатор поставщика удостоверений**, а затем вставьте его в текстовое поле **Identity provider URL** (URL-адрес поставщика удостоверений).

    d. На классическом портале Azure AD скопируйте значение поля **URL-адрес запроса проверки подлинности**, а затем вставьте его в текстовое поле **Identity Provider's AuthnRequest** (Запрос аутентификации поставщика удостоверений).

    д. На классическом портале Azure AD скопируйте значение поля **URL-адрес службы единого входа**, а затем вставьте его в текстовое поле **Identity Provider's SingleLogoutRequest** (Запрос на единый выход поставщика удостоверений).

    Е. В текстовое поле **ServiceNow Homepage** (Домашняя страница ServiceNow) введите URL-адрес домашней страницы экземпляра ServiceNow.

    > [!NOTE] 
    > URL-адрес домашней страницы экземпляра ServiceNow состоит из **URL-адреса клиента ServiceNow** и **/navpage.do** (например, `https://fabrikam.service-now.com/navpage.do`).

    ж. В текстовое поле **Entity ID / Issuer** (Идентификатор сущности или издатель) введите URL-адрес клиента ServiceNow.

    h. В текстовое поле **Audience URL** (URL-адрес аудитории) введите URL-адрес клиента ServiceNow. 

    i. В текстовом поле **Protocol Binding for the IDP's SingleLogoutRequest** (Привязка протокола для запроса на единый выход поставщика удостоверений) введите **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. В текстовое поле NameID Policy (Политика идентификатора имени) введите **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Снимите флажок **Create an AuthnContextClass**(Создать класс контекста проверки подлинности).

    l. В поле **AuthnContextClassRef Method** (Метод AuthnContextClassRef) введите `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Это действие требуется только для организаций на основе облака. Если используется локальная аутентификация AD FS или MFA, это значение указывать не нужно. 

    m. В текстовое поле **Clock Skew** (Разница в показаниях часов) введите **60**.

    n. В поле **Single Sign On Script** (Сценарий единого входа) выберите **MultiSSO_SAML2_Update1**.

    o. В поле **x509 Certificate**(Сертификат x509) выберите сертификат, созданный на предыдущем шаге.

    p. Нажмите кнопку **Submit**(Отправить). 

1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Настройка единого входа")

2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Настройка единого входа")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Настройка единого входа в Azure AD для ServiceNow Express
1. На классическом портале Azure AD на странице интеграции с приложением **ServiceNow** нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Настройка единого входа")

2. На странице **Как пользователи должны входить в ServiceNow?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Настройка единого входа")

3. На странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Настройка URL-адреса приложения")
   
    а. В текстовое поле **URL-адрес входа в ServiceNow** введите URL-адрес, используемый пользователями для входа в приложение ServiceNow, в следующем формате `https://<instance-name>.service-now.com`.
   
    b. В текстовое поле **URL-адрес издателя** введите URL-адрес, используемый пользователями для входа в приложение ServiceNow, в следующем формате `https://<instance-name>.service-now.com`.
   
    c. Щелкните **Далее**

4. Установите флажок **Вручную настроить это приложение для единого входа**, а затем нажмите кнопку **Далее** и выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Настройка URL-адреса приложения")

5. На странице **Настройка единого входа в ServiceNow** щелкните **Скачать сертификат**, сохраните файл сертификата на локальном компьютере, затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Настройка единого входа")

6. Войдите в приложение ServiceNow Express с правами администратора.

7. В расположенной слева области навигации щелкните **Единый вход**.  
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Настройка URL-адреса приложения")

8. В диалоговом окне **Единый вход** щелкните значок конфигурации в правом верхнем углу и настройте следующие свойства.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Настройка URL-адреса приложения")
   
    а. Передвиньте переключатель **Enable multiple provider SSO** (Включить единый вход для нескольких поставщиков) вправо.
   
    b. Передвиньте переключатель **Enable debug logging for the multiple provider SSO integration** (Включить ведение журнала отладки для интеграции нескольких поставщиков единого входа) вправо.
   
    c. В текстовом поле **The field on the user table that...** (Поле в пользовательской таблице) введите значение **user_name**.
9. В диалоговом окне **Единый вход** щелкните **Добавить новый сертификат**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Настройка единого входа")
10. В диалоговом окне **X.509 Certificates** (Сертификаты X.509) выполните следующие действия.
    
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Настройка единого входа")
    
    а. В текстовом поле **Name** (Имя) введите имя конфигурации (например, **TestSAML2.0**).
    
    b. Установите флажок **Активно**.
    
    c. В поле **Format** (Формат) выберите **PEM**.
    
    d. В поле **Type** (Тип) выберите **Trust Store Cert** (Сертификат хранилища доверия).
    
    д. Создайте файл в кодировке Base 64 из скачанного сертификата.
    
    > [!NOTE]
    > Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    Е. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **PEM Certificate** (Сертификат PEM).
    
    ж. Нажмите кнопку **Update**(Обновить).
11. В диалоговом окне **Единый вход** щелкните **Add New IdP** (Добавить нового поставщика удостоверений).
    
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Настройка единого входа")
12. В диалоговом окне **Add New Identity Provider** (Добавление нового поставщика удостоверений) в разделе **Configure Identity Provider** (Настройка поставщика удостоверений) выполните следующие действия.
    
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Настройка единого входа")

    а. В текстовом поле **Имя** введите имя конфигурации (например, **SAML 2.0**).

    b. На классическом портале Azure AD скопируйте значение поля **Идентификатор поставщика удостоверений**, а затем вставьте его в текстовое поле **Identity provider URL** (URL-адрес поставщика удостоверений).

    c. На классическом портале Azure AD скопируйте значение поля **URL-адрес запроса проверки подлинности**, а затем вставьте его в текстовое поле **Identity Provider's AuthnRequest** (Запрос аутентификации поставщика удостоверений).

    d. На классическом портале Azure AD скопируйте значение поля **URL-адрес службы единого входа**, а затем вставьте его в текстовое поле **Identity Provider's SingleLogoutRequest** (Запрос на единый выход поставщика удостоверений).

    д. В поле **Identity Provider Certificate** (Сертификат поставщика удостоверений) выберите сертификат, созданный на предыдущем шаге.


1. Щелкните **Дополнительные параметры** и в разделе **Additional Identity Provider Properties** (Дополнительные свойства поставщика удостоверений) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Настройка единого входа")
   
    а. В текстовом поле **Protocol Binding for the IDP's SingleLogoutRequest** (Привязка протокола для запроса на единый выход поставщика удостоверений) введите **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.
   
    b. В текстовое поле **NameID Policy** (Политика идентификатора имени) введите **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.    
   
    c. В поле **AuthnContextClassRef Method** (Метод AuthnContextClassRef) введите **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Снимите флажок **Create an AuthnContextClass**(Создать класс контекста проверки подлинности).

2. В разделе **Additional Service Provider Properties** (Дополнительные свойства поставщика услуг) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Настройка единого входа")
   
    а. В текстовое поле **ServiceNow Homepage** (Домашняя страница ServiceNow) введите URL-адрес домашней страницы экземпляра ServiceNow.
   
    > [!NOTE]
    > URL-адрес домашней страницы экземпляра ServiceNow состоит из **URL-адреса клиента ServiceNow** и **/navpage.do** (например, `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. В текстовое поле **Entity ID / Issuer** (Идентификатор сущности или издатель) введите URL-адрес клиента ServiceNow.
   
    c. В текстовое поле **Audience URI** (URI аудитории) введите URL-адрес клиента ServiceNow. 
   
    d. В текстовое поле **Clock Skew** (Разница в показаниях часов) введите **60**.
   
    д. В текстовом поле **User Field** (Поле пользователя) введите значение **email** или **user_name**, в зависимости от того, какое поле используется для уникальной идентификации пользователей в развернутой службе ServiceNow.
   
    > [!NOTE]
    > В Azure AD можно настроить, чтобы в качестве уникального идентификатора в токене SAML выдавался идентификатор пользователя Azure AD (имя участника-пользователя) или его электронный адрес. Для этого перейдите в раздел **ServiceNow > Атрибуты > Единый вход** на классическом портале Azure и сопоставьте нужное поле с атрибутом **nameidentifier**. Значение, хранящееся для выбранного атрибута в Azure AD (например, имя участника-пользователя) должно соответствовать значению, хранящемуся в ServiceNow для заполненного поля (например, user_name).
    > 
    > 
   
    Е. Щелкните **Сохранить**. 

3. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Настройка единого входа")

4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Настройка единого входа")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **ServiceNow** щелкните **Настроить подготовку учетных записей пользователей**. 
   
    ![Подготовка учетных записей пользователей](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Подготовка учетных записей пользователей")

2. На странице **Введите учетные данные ServiceNow, чтобы включить автоматическую подготовку учетных записей пользователей** укажите следующие параметры конфигурации:
   
     а. В текстовое поле **Имя экземпляра ServiceNow** введите имя экземпляра ServiceNow.
   
     b. В текстовом поле **Имя пользователя администратора ServiceNow** введите имя учетной записи администратора ServiceNow.
   
     c. В текстовое поле **Пароль администратора ServiceNow** введите пароль для этой учетной записи.
   
     d. Нажмите **проверить** для проверки конфигурации.
   
     д. Нажмите кнопку **Далее**, чтобы открыть страницу **Дальнейшие действия**.
   
     Е. Если вы хотите подготовить всех пользователей для этого приложения, установите флажок**Автоматически настраивать все учетные записи пользователей из каталога в этом приложении**. 
   
    ![Дальнейшие действия](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Дальнейшие действия")
   
     g. На странице **Дальнейшие действия** нажмите кнопку **Завершить**, чтобы сохранить конфигурацию.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   а. В текстовом поле **Имя** введите **Britta**.  
   
   b. В текстовом поле **Фамилия** введите **Simon**.
   
   c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-servicenow-test-user"></a>Создание тестового пользователя ServiceNow
В этом разделе описано, как создать пользователя Britta Simon в ServiceNow. В этом разделе описано, как создать пользователя Britta Simon в ServiceNow. Если вы не знаете, как добавить пользователя в свою учетную запись ServiceNow или ServiceNow Express, обратитесь в службу поддержки ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к ServiceNow.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в ServiceNow, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **ServiceNow**.
   
    ![Настройка единого входа](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 

4. В списке "Все пользователи" выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ServiceNow на панели доступа, вы автоматически войдете в приложение ServiceNow.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
