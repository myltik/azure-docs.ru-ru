---
title: Руководство по интеграции Azure Active Directory с Qlik Sense Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 8cca4fb3ee10d56481cefccb7ea869b6bf13109c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Руководство. Интеграция Azure Active Directory с Qlik Sense Enterprise

В этом руководстве описано, как интегрировать Qlik Sense Enterprise с Azure Active Directory (Azure AD).

Интеграция Qlik Sense Enterprise с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать, кто будет иметь доступ к Qlik Sense Enterprise.
- Вы можете включить автоматический вход пользователей в Qlik Sense Enterprise (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Qlik Sense Enterprise, вам потребуется:

- подписка Azure AD;
- подписка Qlik Sense Enterprise с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Qlik Sense Enterprise из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Добавление Qlik Sense Enterprise из коллекции
Чтобы настроить интеграцию Qlik Sense Enterprise в Azure AD, необходимо добавить Qlik Sense Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Qlik Sense Enterprise из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Qlik Sense Enterprise**, выберите **Qlik Sense Enterprise** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Qlik Sense Enterprise в списке результатов](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Qlik Sense Enterprise с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Qlik Sense Enterprise соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Qlik Sense Enterprise.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Qlik Sense Enterprise.

Чтобы настроить и проверить единый вход Azure AD в Qlik Sense Enterprise, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user)** требуется для того, чтобы в Qlik Sense Enterprise существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Qlik Sense Enterprise.

**Чтобы настроить единый вход Azure AD в Qlik Sense Enterprise, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Qlik Sense Enterprise** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Qlik Sense Enterprise** сделайте следующее:

    ![Сведения о домене и URL-адресах в приложении Qlik Sense Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Обратите внимание на косую черту в конце этого URI. Она обязательная.
    
    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Вместо этих значений укажите фактические идентификатор и URL-адрес ответа, к которым мы вернемся позже в этом руководстве, или обратитесь к [группе поддержки Qlik Sense Enterprise](https://www.qlik.com/us/services/support), чтобы получить эти значения. 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Подготовьте XML-файл метаданных федерации для отправки на сервер Qlik Sense.
   
    > [!NOTE]
    > Перед отправкой метаданных IdP на сервер Qlik Sense файл необходимо изменить. В нем нужно удалить определенные сведения, чтобы обеспечить корректное взаимодействие Azure AD и сервера Qlik Sense.
    
    ![QlikSense][qs24]
   
    a. Откройте в текстовом редакторе файл FederationMetaData.xml, скачанный с портала Azure.
   
    Б. Найдите значение **RoleDescriptor**.  Вы найдете четыре записи (две пары открывающих и закрывающих тегов для элементов).
   
    c. Удалите теги RoleDescriptor и все данные между ними из файла.
   
    d. Сохраните файл и держите его под рукой для дальнейшего использования в этом документе.

7. Перейдите к консоли управления Qlik Management Console (QMC) Qlik Sense как пользователь с возможностью создания конфигураций виртуального прокси-сервера.

8. В QMC щелкните пункт меню **Virtual Proxies** (Виртуальные прокси-серверы).
   
    ![QlikSense][qs6] 

9. В нижней части страницы нажмите кнопку **Create new** (Создать).
   
    ![QlikSense][qs7]

10. Появится экран Virtual proxy edit (Редактирование виртуального прокси-сервера).  В правой части экрана расположено меню для отображения параметров конфигурации.
   
    ![QlikSense][qs9]

11. Установите галочку возле пункта меню Identification (Идентификация) и введите идентифицирующие сведения для конфигурации виртуального прокси-сервера Azure.
    
    ![QlikSense][qs8]  
    
    a. В поле **Description** (Описание) укажите понятное имя для конфигурации виртуального прокси-сервера.  Введите значение в поле Description (Описание).
    
    Б. Значение в поле **Prefix** (Префикс) определяет конечную точку виртуального прокси-сервера для подключения к Qlik Sense с использованием единого входа Azure AD.  Введите уникальное имя префикса для этого виртуального прокси-сервера.
    
    c. **Session inactivity timeout (minutes)** (Время ожидания при бездействии сеанса (в минутах)) — это время для ожидания подключений через этот виртуальный прокси-сервер.
    
    d. **Session cookie header name** (Имя заголовка сеанса в файле cookie) — имя cookie, которое содержит идентификатор для сеанса Qlik Sense, полученный пользователем после успешной проверки подлинности.  Это имя должно быть уникальным.

12. Щелкните пункт меню Authentication (Проверка подлинности), чтобы сделать его видимым.  Появится экран Authentication (Проверка подлинности).
    
    ![QlikSense][qs10]
    
    a. Значение в раскрывающемся списке **Anonymous access mode** (Режим анонимного доступа) определяет, могут ли анонимные пользователи получить доступ к Qlik Sense через виртуальный прокси-сервер.  Параметр по умолчанию — No anonymous user (Без анонимных пользователей).
    
    Б. В раскрывающемся меню **Authentication method** (Метод аутентификации) следует выбрать схему аутентификации, которую будет использовать виртуальный прокси-сервер.  Выберите в этом списке элемент SAML.  После этого появятся дополнительные параметры.
    
    c. В поле **SAML host URI** (URI узла SAML) введите имя узла, которое пользователи будут вводить для доступа к Qlik Sense через этот виртуальный прокси-сервер SAML.  Имя узла представляет собой URI сервера Qlik Sense.
    
    d. В поле **SAML entity ID** (Идентификатор сущности SAML) введите то же значение, что и в поле SAML host URI (URI узла SAML).
    
    д. **SAML IdP metadata** (Метаданные IdP SAML) — это файл, измененный ранее в разделе **Изменение метаданных федерации из конфигурации Azure AD**.  **Перед отправкой метаданных IdP файл необходимо изменить.** В нем нужно удалить определенные сведения, чтобы обеспечить корректное взаимодействие между Azure AD и сервером Qlik Sense.  **Если файл нужно изменить, см. инструкции выше.**  Если файл изменен, щелкните кнопку Browse (Обзор) и выберите измененный файл метаданных для передачи в конфигурацию виртуального прокси-сервера.
    
    f. Введите имя атрибута или ссылку на схему для атрибута SAML, представляющего **идентификатор пользователя**, который Azure AD отправит на сервер Qlik Sense.  Сведения о ссылке на схему доступны в постконфигурации экранов приложения Azure.  Чтобы использовать атрибут имени, введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    ж. Введите значение для **каталога пользователя** , который будет присоединен к пользователям при проверке подлинности на сервере Qlik Sense с использованием Azure AD.  Жестко заданные значения нужно заключить в **квадратные скобки []**.  Чтобы использовать атрибут, отправленный в утверждении SAML Azure AD, введите имя атрибута в этом текстовом поле **без** квадратных скобок.
    
    h. В раскрывающемся списке **SAML signing algorithm** (Алгоритм подписывания SAML) можно задать подписывание сертификата поставщика услуг (в этом случае сервер Qlik Sense) для конфигурации виртуального прокси-сервера.  Если сервер Qlik Sense использует доверенный сертификат, созданный с помощью Microsoft Enhanced RSA и AES Cryptographic Provider, измените алгоритм подписывания SAML на **SHA-256**.
    
    i. В разделе SAML attribute mapping (Сопоставление атрибутов SAML) можно отправлять дополнительные атрибуты, например группы, в Qlik Sense для использования в правилах безопасности.

13. Щелкните пункт меню **Load balancing** (Балансировка нагрузки), чтобы сделать его видимым.  Появится экран Load balancing (Балансировка нагрузки).
    
    ![QlikSense][qs11]

14. Нажмите кнопку **Add new server node** (Добавить новый узел сервера), выберите узел или узлы обработчика, на которые Qlik Sense будет отправлять сеансы для балансировки нагрузки, а затем нажмите кнопку **Add** (Добавить).
    
    ![QlikSense][qs12]

15. Щелкните пункт меню Advanced (Дополнительно), чтобы сделать его видимым. Откроется экран Advanced (Дополнительно).
    
    ![QlikSense][qs13]
    
    В разделе Host white list (Список разрешенных узлов) определяются имена принимаемых узлов при подключении к серверу Qlik Sense.  **Введите имя узла, которое пользователи будут указывать при подключении к серверу Qlik Sense.** Для имени узла указывается то же значение, что и для URI узла SAML, только без https://.

16. Щелкните кнопку **Apply** (Применить).
    
    ![QlikSense][qs14]

17. Нажмите кнопку OK (ОК), чтобы принять предупреждение о перезапуске прокси-серверов, связанных с виртуальным прокси-сервером.
    
    ![QlikSense][qs15]

18. В правой части экрана отображается меню Associated items (Связанные элементы).  Щелкните пункт меню **Proxies** (Прокси-серверы).
    
    ![QlikSense][qs16]

19. Появится экран прокси-сервера.  Щелкните кнопку **Link** (Связать) внизу, чтобы связать прокси-сервер с виртуальным прокси-сервером.
    
    ![QlikSense][qs17]

20. Выберите узел прокси-сервера, который будет поддерживать подключение к этому виртуальному прокси-серверу, и щелкните кнопку **Link** (Связать).  После установки связи прокси-сервер отобразится в списке связанных прокси-серверов.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Через 5–10 секунд появится сообщение об обновлении QMC.  Нажмите кнопку **Refresh QMC** (Обновить QMC).
    
    ![QlikSense][qs20]

22. После обновления QMC щелкните пункт меню **Virtual proxies** (Виртуальные прокси-серверы). Новая запись виртуального прокси-сервера SAML указана в таблице на экране.  Выделите запись виртуального прокси-сервера.
    
    ![QlikSense][qs51]

23. В нижней части экрана станет активной кнопка Download SP metadata (Скачать метаданные SP).  Щелкните **Download SP metadata** (Скачать метаданные SP), чтобы сохранить метаданные в файл.
    
    ![QlikSense][qs52]

24. Откройте файл метаданных SP.  Обратите внимание на записи **entityID** и **AssertionConsumerService**.  Значения этих записей должны быть эквивалентны **идентификатору** и **URL-адресу для входа** в конфигурации приложения Azure AD. Вставьте эти значения в разделе **Домены и URL-адреса приложения Qlik Sense Enterprise** в параметрах конфигурации приложения в Azure AD. Если они не совпадают, измените их через мастер настройки приложения Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

   ![Кнопка "Azure Active Directory"](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

   ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

   ![Кнопка "Добавить"](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

   ![Диалоговое окно "Пользователь"](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. В поле **Имя** введите **BrittaSimon**.

   Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

   c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

   d. Нажмите кнопку **Создать**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Создание тестового пользователя Qlik Sense Enterprise

В этом разделе описано, как создать пользователя Britta Simon в приложении Sense Enterprise. Обратитесь в [службу поддержки Qlik Sense Enterprise](https://www.qlik.com/us/services/support), чтобы добавить пользователей для платформы Qlik Sense Enterprise. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Qlik Sense Enterprise.

![Назначение роли пользователя][200] 

**Чтобы назначить Britta Simon в Qlik Sense Enterprise, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Qlik Sense Enterprise**.

    ![Ссылка на Qlik Sense Enterprise в списке приложений](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Qlik Sense Enterprise на панели доступа, вы автоматически войдете в приложение Qlik Sense Enterprise. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

