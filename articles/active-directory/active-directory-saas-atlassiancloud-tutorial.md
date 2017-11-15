---
title: "Руководство по интеграции Azure Active Directory с Atlassian Cloud | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в приложении Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: 25a7694cefde344b34d3a46535bf09209b306593
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Руководство по интеграции Azure Active Directory с Atlassian Cloud

В этом руководстве описано, как интегрировать приложение Atlassian Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Atlassian Cloud обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Atlassian Cloud.
- Вы можете включить автоматический вход пользователей в Atlassian Cloud (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Atlassian Cloud, вам потребуется:

- подписка Azure AD;
- Чтобы включить единый вход SAML для продуктов компании Atlassian Cloud, потребуется настроить Identity Manager. Дополнительные сведения об Identity Manager см. [здесь]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. добавление Atlassian Cloud из коллекции;
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции
Чтобы настроить интеграцию Atlassian Cloud с Azure AD, необходимо добавить Atlassian Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Atlassian Cloud из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Atlassian Cloud**, выберите **Atlassian Cloud** на панели результатов и щелкните **Добавить**, чтобы добавить это приложение.

    ![Atlassian Cloud в списке результатов](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Atlassian Cloud с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD необходимо знать, какой пользователь в Atlassian Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Atlassian Cloud.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Atlassian Cloud.

Чтобы настроить и проверить единый вход Azure AD в Atlassian Cloud, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Atlassian Cloud](#create-an-atlassian-cloud-test-user)** требуется для того, чтобы в Atlassian Cloud существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале и настроить его в приложении Atlassian Cloud.

**Чтобы настроить единый вход Azure AD в Atlassian Cloud, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Atlassian Cloud** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Atlassian Cloud** выполните указанные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    а. В текстовом поле **Идентификатор** введите URL-адрес `https://id.atlassian.com/login`.
    
    b. В текстовом поле **URL-адрес ответа** введите URL-адрес `https://id.atlassian.com/login/saml/acs`.

    c. В текстовом поле **Состояние ретранслятора** введите URL-адрес в формате `https://<instancename>.atlassian.net`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Эти значения можно получить на экране настройки SAML для Atlassian Cloud, который описан далее в этом руководстве.

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. В разделе **Настройка Atlassian Cloud** щелкните **Настроить Atlassian Cloud**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Чтобы настроить единый вход для вашего приложения, войдите на портал Atlassian с правами администратора.

9. На веб-сайте Atlassian выберите **Administration** (Администрирование) > **Organizations & Security** (Организации и безопасность). Создайте организацию и присвойте ей имя, если это еще не сделано. В левой области навигации щелкните **Domains** (Домены).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Выберите в качестве способа проверки домена **DNS** или **HTTPS**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. Чтобы применить проверку DNS, выберите вкладку **DNS** на странице **Domains** (Домены) и выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    а. Щелкните **Copy** (Копировать), чтобы скопировать значение для записи типа TXT.

    b. В DNS найдите страницу параметров для добавления новой записи.

    c. Выберите параметр для добавления новой записи и вставьте значение, скопированное на странице **Domains** (Домены) в поле **Value** (Значение). DNS также может ссылаться на него в качестве **ответа** или **описания**.

    d. Запись DNS также может включать в себя следующие поля:
    
    * **Тип записи**. Введите **TXT**.
    * **Имя, узел и псевдоним**. Оставьте значение по умолчанию (@ или пустое).
    * **Срок жизни (TTL)**. Введите **86400**.
    
    д.  Сохраните запись.

12. Вернитесь на **страницу доменов** в разделе администрирования организации и нажмите кнопку **Verify domain** (Проверить домен). Введите доменное имя во всплывающем окне и нажмите кнопку **Verify domain** (Проверить домен).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > До того как изменения записей типа TXT вступят в силу, может пройти приблизительно 72 часа (вы не сразу узнаете, прошла ли проверка вашего домена). Проверьте страницу **Domains** (Домены) вскоре после выполнения действий, чтобы узнать состояние проверки. Появится следующий экран с обновленным состоянием **VERIFIED** (Проверено).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. Для проверки HTTPS выберите вкладку **HTTPS** на странице **Domains** (Домены) и выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    а.  Щелкните **Download file** (Загрузить файл), чтобы загрузить HTML-файл.

    b.  Отправьте HTML-файл в корневой каталог домена.

14. Вернитесь на **страницу доменов** в разделе администрирования организации и нажмите кнопку **Verify domain** (Проверить домен). Введите **доменное имя** во всплывающем окне и нажмите кнопку **Verify domain** (Проверить домен).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Если в процессе проверки удалось найти файл, загруженный в корневой каталог, состояние домена обновляется до значения **Verified** (Проверено).

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Дополнительные сведения см. в [документации по проверке домена Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

16. В левой панели навигации щелкните **SAML single sign-on** (Единый вход SAML). Подпишитесь на Atlassian Identity Manager.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. В диалоговом окне **Add SAML configuration** (Добавление конфигурации SAML) добавьте параметры поставщика удостоверений следующим образом:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    а. В текстовое поле **Identity Provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте значение **Идентификатор сущности SAML**, скопированное на портале Azure.

    b. В текстовое поле **Identity Provider SSO URL** (URL-адрес единого входа поставщика удостоверений) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. Откройте скачанный сертификат в Блокноте, скопируйте значения без строк Begin и End и вставьте его в поле **Public X509 certificate** (Общий сертификат X509).
    
    d. Чтобы сохранить параметры, нажмите кнопку **Save Configuration** (Сохранить конфигурацию).
     
18. В параметрах Azure AD задайте правильные URL-адреса.
  
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    а. Скопируйте **идентификатор удостоверения поставщика услуг** и вставьте его в поле **Идентификатор** на портале Azure в разделе **Домены и URL-адреса приложения Atlassian Cloud**.
    
    b. На экране SAML скопируйте значение **URL-адрес поставщика службы обработчика утверждений поставщика услуг** и вставьте его в поле **URL-адрес ответа** на портале Azure в разделе **Домены и URL-адреса приложения Atlassian Cloud**.
    
    c. URL-адрес входа является URL-адресом клиента приложения Atlassian Cloud. 
    
19. На портале Azure нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    а. В поле **Имя** введите **BrittaSimon**.

    b. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    г) Щелкните **Создать**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Создание тестового пользователя в Atlassian Cloud

Чтобы пользователи Azure AD могли выполнять вход в Atlassian Cloud, они должны быть подготовлены в Atlassian Cloud. В случае Atlassian Cloud подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. На портале Atlassian в разделе "Site administration" (Администрирование сайта) нажмите кнопку **Users** (Пользователи).

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Нажмите кнопку **Invite user** (Пригласить пользователя), чтобы создать пользователя в приложении Atlassian Cloud.

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Введите **адрес электронной почты** и назначьте доступ к приложению. 

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Нажмите кнопку **Invite users** (Пригласить пользователей). После этого пользователю будет отправлено приглашение по электронной почте. Как только пользователь примет приглашение, он станет активным в системе. 

>[!NOTE] 
>Вы также можете создать несколько пользователей, нажав кнопку **Bulk Create** (Массовое создание) в разделе "Users" (Пользователи).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Atlassian Cloud.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Atlassian Cloud, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Atlassian Cloud**.

    ![Ссылка на Atlassian Cloud в списке приложений](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Atlassian Cloud на панели доступа, вы автоматически войдете в приложение Atlassian Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

