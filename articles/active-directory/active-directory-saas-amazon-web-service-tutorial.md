---
title: "Руководство по интеграции Azure Active Directory с Amazon Web Services (AWS) | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Amazon Web Services (AWS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: bc04f4c632daef99a4f12e237dfe395040039afe
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Руководство по интеграции Azure Active Directory с Amazon Web Services

В этом руководстве описано, как интегрировать приложение Amazon Web Services (AWS) с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

- Можно управлять в Azure AD, который имеет доступ к Amazon Web Services (AWS).
- Вы можете включить их учетные записи Azure AD пользователям автоматически получить вошедшего в Amazon Web Services (AWS) (Single Sign-On).
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

- подписка Azure AD;
- Amazon Web Services (AWS) единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Amazon Web Services из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции
Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amazon Web Services (AWS) из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Amazon Web Services (AWS)**выберите **Amazon Web Services (AWS)** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![Amazon Web Services (AWS) в списке результатов](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Amazon Web Services.

В Amazon Web Services (AWS), присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя, прошедшего Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  — иметь аналог Саймон Britta в Amazon Web Services (AWS), связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amazon Web Services (AWS).

**Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия:**

1. На портале Azure на **Amazon Web Services (AWS)** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Amazon Web Services (AWS)** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Домен Amazon Web Services (AWS) и URL-адреса единого входа сведения](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа для атрибутов](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)  

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута  | Значение атрибута | Пространство имен |
    | --------------- | --------------- | --------------- |
    | rolesessionname | user.userprincipalname | https://aws.Amazon.com/SAML/Attributes |
    | role            | user.assignedroles |  https://aws.Amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Необходимо настроить подготовку пользователя в Azure AD для получения всех ролей из консоли AWS. См. этапы подготовки.

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа для атрибутов](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

6. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.

9. Щелкните **Console Home**(Домашняя страница консоли).
   
    ![Настройка домашней единого входа][11]

10. Щелкните **Identity and Access Management**(Управление удостоверениями и доступом). 
   
    ![Настройка удостоверения-][12]

11. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик). 
   
    ![Настройка поставщика единого входа][13]

12. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия. 
   
    ![Настройка единого входа для диалогового окна][14]
 
    a. Выберите для параметра **Provider Type** (Тип поставщика) значение **SAML**.

    Б. В **имя поставщика** текстовом поле введите имя поставщика (например: *WAAD*).

    c. Чтобы отправить загруженный вашей **файл метаданных** из портала Azure щелкните **выбрать файл**.

    d. Щелкните **Следующий шаг**.

13. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать). 
    
    ![Настройка единого входа для проверки][15]

14. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create New Role** (Создать новую роль). 
    
    ![Настройка ролей-][16]

15. В диалоговом окне **Set Role Name** (Настройка имени роли) выполните следующие действия. 
    
    ![Настройка имени-][17] 

    a. В **имя роли** текстовом поле введите имя роли (например: *TestUser*). 

    Б. Щелкните **Следующий шаг**.

16. В диалоговом окне **Select Role Type** (Выбор типа роли) выполните следующие действия. 
    
    ![Настройка роли-тип][18] 

    a. Выберите **Роль для доступа поставщика удостоверений**. 

    Б. В **Предоставление единого входа через Интернет (WebSSO) для доступа к поставщикам SAML** щелкните **Выбрать**.

17. В диалоговом окне **Establish Trust** (Установка доверия) выполните следующие действия.  
    
    ![Настройка доверия-][19] 

    a. В качестве поставщика SAML выбрать поставщика SAML, вы создали ранее (например: *WAAD*) 
  
    Б. Щелкните **Следующий шаг**.

18. В диалоговом окне **Verify Role Trust** (Проверка доверия роли) нажмите кнопку **Next Step** (Следующий шаг). 
    
    ![Настройка роли-доверия][32]

19. В диалоговом окне **Attach Policy** (Присоединение политики) нажмите кнопку **Next Step** (Следующий шаг).  
    
    ![Настройка политики-][33]

20. В диалоговом окне **Review** (Обзор) выполните следующие действия.   
    
    ![Настройка проверки-][34] 

    a. Щелкните **Создать роль**.

    Б. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

21. Использование учетных данных учетной записи службы AWS выборки роли из учетной записи AWS в подготовки пользователя Azure AD. Для этого откройте консоли AWS home.

22. Щелкните **службы** -> **безопасности, идентификаторов и соответствия** -> **IAM**.

    ![Получение ролей из учетной записи AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Выберите **политики** вкладке раздела IAM.

    ![Получение ролей из учетной записи AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Создать новую политику, щелкнув **Создание политики**.

    ![Создание новой политики](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Создайте собственную политику для выборки всех ролей из AWS учетных записей. В **создать собственную политику** щелкните **выберите** кнопки.
    
    ![Создание новой политики](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

26. Определите новую политику, выполнив следующие действия.

    ![Определение новой политики](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. Укажите **имя политики** как **AzureAD_SSOUserRole_Policy**.

    Б. Вы можете предоставить **описание** политику как **это политика позволяет выбирать роли из учетных записей AWS**.

    c. Добавьте в документ политики ниже JSON.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    d. Убедитесь, что установлен флажок на **использовать автоматическое форматирование для редактирования политики**.

    д. Щелкните **политики проверки** кнопку внизу.

    f. После политики были проверяется правильность затем можно щелкнуть на **создать политику** кнопки.

    ![Создать новую политику](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)
    
27. Создание новой учетной записи пользователя в службе IAM AWS, выполнив следующие шаги:

    a. Щелкните **пользователей** навигации в консоли AWS IAM.

    ![Определение новой политики](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    Б. Щелкните **добавить пользователя** кнопку, чтобы создать нового пользователя.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. В **добавить пользователя** выполните следующие действия:
    
    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Введите имя пользователя как **AzureADRoleManager**.
    
    * Тип доступа, выберите **программный доступ** параметр. Таким образом, пользователь может вызывать API-интерфейсы, выборка роли из AWS учетной записи.
    
    * Щелкните **Далее разрешения** кнопку в правом нижнем углу.

28. Теперь можно создайте новую политику для этого пользователя, выполнив следующие шаги:

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/policy6.png)
    
    a. Щелкните **подключение существующих политик напрямую** кнопки.

    Б. Поиск вновь созданную политику в разделе фильтра **AzureAD_SSOUserRole_Policy**.
    
    c. Выберите **политики** и выберите команду **далее: просмотрите** кнопки.

29. Просмотрите сведения о политике для подключенных пользователей, выполнив следующие шаги:

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Проверьте имя пользователя, типа доступа и политики, сопоставленное с пользователем.
    
    Б. Щелкните **создать пользователя** кнопку в правом нижнем углу, чтобы создать пользователя.

30. Загрузите учетные данные пользователя, выполнив следующие шаги:

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Скопируйте пользователь **доступа ИД ключа** и **доступа секретный ключ**.
    
    Б. Введите эти учетные данные в Azure AD пользователю подготовки раздел извлекать ролей из консоли AWS.
    
    c. Щелкните **закрыть** кнопку внизу.

31. Перейдите к **подготовки пользователей** раздел Amazon Web Services приложения на портале управления Azure AD.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Введите **ключ доступа** и **секрет** в **секрет клиента** и **секрет маркера** полей соответственно.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Введите ключ доступа пользователя AWS в **clientsecret** поля.
    
    Б. Введите секрет пользователя AWS в **секрет маркера** поля.
    
    c. Щелкните **проверить подключение** кнопки и вы должны иметь возможность успешного тестирования этого подключения.

    d. Сохранить внесенные изменения, щелкнув **Сохранить** наверху.
 
33. Теперь убедитесь, что включен состояние подготовки **на** в разделе "Параметры", что коммутатор на команду на **Сохранить** наверху.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Создание тестового пользователя, прошедшего Amazon Web Services (AWS)

Цель этого раздела — создать пользователя с именем Britta Simon в Amazon Web Services. Amazon Web Services (AWS) не требуется пользователя должны быть созданы в системе для единого входа, поэтому не нужно выполнять никаких действий здесь.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа путем предоставления доступа к Amazon Web Services (AWS).

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Amazon Web Services (AWS), сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Amazon Web Services**.

    ![По ссылке Amazon Web Services (AWS) в список приложений](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services на панели доступа, вы автоматически войдете в приложение Amazon Web Services.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="known-issues"></a>Известные проблемы

 * В **Provisioning** разделе **сопоставления** подраздел будет отображать сообщение «Загрузка...» и никогда не будут отображаться сопоставления атрибутов. Только рабочий процесс подготовки, поддерживаемых сейчас — для импорта ролей из AWS в Azure AD для выбора во время назначения пользователей или групп. Сопоставления атрибутов для этого заранее заданное и не настраивается.
 
 * **Provisioning** раздел поддерживает только ввода один набор учетных данных для одного клиента AWS одновременно. Все импортированные роли записываются в свойство appRoles Azure AD [объект субъекта-службы](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/serviceprincipal) AWS клиента. Можно добавить несколько клиентов AWS (представленным субъекты службы) в Azure AD из коллекции для инициализации, однако имеется известная проблема с не смогла автоматически записи все роли, импортированные из нескольких субъекты службы AWS, используемый для Подготовка в одном субъекта-службы, используемые для единого входа. Чтобы избежать этого [Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/serviceprincipal) можно использовать для извлечения всех appRoles, импортируются в каждом servicePrincipal AWS которой задан режим инициализации. Эти строки роли можно впоследствии добавить servicePrincipal AWS, в котором единого входа настроена.


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
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

